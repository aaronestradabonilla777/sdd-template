# Guía de Adaptador: Google Antigravity

Esta guía documenta la configuración, arquitectura operativa y flujo de ejecución para utilizar `sdd-template` en **Google Antigravity** con la familia de modelos **Gemini** (Gemini 1.5 Pro, Gemini 2.0 Pro, Gemini Flash y Gemini Flash-Lite).

---

## 1. Visión General de Antigravity

Google Antigravity es un entorno de desarrollo multi-agente impulsado por agentes de IA autónomos coordinados mediante llamadas a herramientas, mensajería reactiva y aislamiento de espacios de trabajo en el sistema de archivos.

### Características del Runtime
- **Ventana de Contexto Extensa**: 1M a 2M tokens en Gemini 1.5/2.0 Pro. Permite asimilar repositorios extensos, pero exige disciplina estricta de orden y podado de contexto para evitar latencia, degradación de atención y costos acumulados.
- **Coordinación Reactiva**: Los agentes se reactivan mediante eventos, herramientas asíncronas (`manage_task`, `schedule`) y mensajes directos (`send_message`).
- **Aislamiento por Directorios**: Cada agente posee una carpeta de trabajo exclusiva dentro de `.agents/teamwork/`.

---

## 2. Mapeo de Modelos Gemini en la Matriz de 3 Tiers

Antigravity distribuye los roles de `harnesses/multi-agent.md` en tres tiers de cómputo para garantizar la regla 80/20 de tokens:

| Tier | Modelo Gemini | Roles Asignados | Función en el Flujo |
|---|---|---|---|
| **Tier 1 (Pro / Frontier)** | **Gemini 1.5 Pro / Gemini 2.0 Pro** | Orchestrator, Discovery Specialist, Challenger, Forensic Auditor | Razonamiento de largo alcance, descomposición de epics, modelado de amenazas y veto de integridad. |
| **Tier 2 (Fast / Economical)** | **Gemini 1.5 Flash / Gemini 2.0 Flash** | Worker (estándar), Explorer, Reviewer | Mapeo de código, implementación de specs, validación de contratos y linters a ultra-baja latencia (<1.5s). |
| **Tier 3 (Ultra-Light)** | **Gemini Flash-Lite** | Worker (mecánico) | Boilerplate repetitivo, scripts aislados, generación masiva de casos de test unitarios. |

---

## 3. Arquitectura del Espacio de Trabajo (`.agents/teamwork/`)

Antigravity implementa aislamiento estricto mediante la jerarquía de carpetas:

```
.agents/teamwork/
├── orchestrator/           # BRIEFING.md, PROJECT.md, plan de hitos
├── explorer_1/             # analysis.md, handoff.md, progress.md
├── worker_1/               # progress.md, handoff.md, diffs locales
├── reviewer_1/             # review_report.md, handoff.md
├── challenger_1/           # threat_report.md, handoff.md
└── auditor_1/              # integrity_cert.md, veto_report.md
```

### Reglas de Aislamiento de Carpetas
1. **Escritura Exclusiva**: Cada agente escribe **únicamente** en su propia carpeta asignada. Está prohibido escribir directamente en carpetas de otros agentes.
2. **Lectura Universal**: Cualquier agente puede leer archivos en cualquier carpeta para consultar reportes y especificaciones.
3. **Cero Código en `.agents/teamwork/`**: Esta carpeta contiene **exclusivamente metadatos** (planes, reportes, progress, handoffs). Todo código de aplicación pertenece a `src/`, `tests/` o `specs/`.

---

## 4. Protocolo de Comunicación y Memoria

### Protocolo de Mensajería
- **"Archivos para entrega de contenido, Mensajes para coordinación"**:
  - Los reportes de análisis y especificaciones se escriben en disco.
  - Los mensajes enviados mediante `send_message` deben ser menores a **300 palabras** y usar la plantilla estándar:

```markdown
**Context**: [Spec o Tarea ID]
**Content**: [Resumen conciso <150 palabras + ruta absoluta al entregable]
**Action**: [Acción concreta requerida del destinatario]
```

### Memoria Situacional (`BRIEFING.md`)
Cada agente mantiene un archivo `BRIEFING.md` como índice de memoria viva:
- Debe mantenerse **estrictamente por debajo de 100 líneas**.
- Secciones bloqueadas e inmutables: `## 🔒 My Identity` y `## 🔒 Key Constraints`.
- Si el archivo excede 100 líneas, el contenido mutable antiguo se mueve a `BRIEFING_ARCHIVE.md`.

### Heartbeat de Liveness (`progress.md`)
Para monitorizar el estado de ejecución de tareas largas (ej. compilaciones o suites de tests), cada agente actualiza `progress.md` tras cada paso significativo con la etiqueta:
`Last visited: [timestamp UTC]`.

---

## 5. Reporte de Traspaso en 5 Componentes (`handoff.md`)

Todo traspaso de tarea entre agentes (ej. de Worker a Reviewer, o de Explorer a Orchestrator) debe formalizarse en un archivo `handoff.md` autosuficiente con cinco secciones:

1. **Observation**: Hechos directamente observados (rutas exactas, líneas, comandos ejecutados y salidas verbatim).
2. **Logic Chain**: Razonamiento deductivo paso a paso desde las observaciones hasta la conclusión.
3. **Caveats**: Supuestos asumidos, rutas no exploradas o condiciones límite.
4. **Conclusion**: Dictamen final claro, accionable y acotado.
5. **Verification Method**: Comandos exactos de prueba para verificar independientemente la conclusión.

---

## 6. Configuración Inicial en Antigravity

1. **Reconocimiento de Reglas**: Antigravity detecta e ingesta automáticamente `AGENTS.md` como regla global del workspace.
2. **Tool Sandboxing**:
   - Para tareas de lectura: usa `view_file`, `list_dir`, `grep_search`, `find_by_name`.
   - Para edición precisa de código: usa `replace_file_content` o `write_to_file`.
   - Para comandos de terminal: usa `run_command` dentro de los límites de sandbox. Si un comando requiere elevación externa no disponible, ejecuta un fallback automático a herramientas nativas de inspección de archivos (file inspection tools: `view_file`, `list_dir`, `grep_search`).
3. **Flujo de Ejecución Típico**:
   - El Orchestrator inicializa `PROJECT.md` y evalúa el sizing (`harnesses/sizing.md`).
   - El Orchestrator despacha a un Explorer (Tier 2) o Discovery Specialist (Tier 1).
   - Aprobada la spec en `specs/`, el Orchestrator despacha al Worker (Tier 2 Flash) indicando el Result Contract.
   - El Worker aplica el cambio, corre los tests locales y, si falla 2 veces consecutivas, dispara el **Disyuntor de 2 Intentos** (`harnesses/isolation.md`).
   - Reviewer, Challenger y Forensic Auditor ejecutan sus compuertas de verificación.
   - El Orchestrator ejecuta la fase de cierre (`harnesses/closing.md`) y actualiza `Engram.md`.

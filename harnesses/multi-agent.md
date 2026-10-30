# Multi-Agent Orchestration Harness

Este harness define la arquitectura multi-agente autoritativa para proyectos que operan bajo el framework SDD (Software-Design-Driven). Establece la taxonomía de roles especializados, la matriz de asignación de modelos por niveles (3 Tiers), la regla del presupuesto 80/20 de tokens y los estándares de comunicación inter-agente agnósticos respecto al runtime (Antigravity, Claude Code y OpenCode).

---

## 1. Principios de Orquestación y Gobernanza

1. **Agnosticismo de Plataforma**: Las reglas de coordinación operan de manera idéntica si el sistema se ejecuta bajo Google Antigravity, Anthropic Claude Code o entornos locales OpenCode / Ollama.
2. **Separación Estricta de Responsabilidades**: Ningún agente asume simultáneamente roles de planificación, implementación y auditoría de integridad. La segregación cognitiva previene la ceguera de contexto y las alucinaciones acumulativas.
3. **Archivos para Entrega de Contenido, Mensajes para Coordinación**: El contenido pesado (código, diffs, logs, planes, reportes) reside exclusivamente en el sistema de archivos. Los mensajes de chat o señales de mensajería se limitan a notificaciones estructuradas de coordinación (<300 palabras).
4. **Presupuesto Asimétrico de Tokens (Regla 80/20)**: Los modelos más costosos y de alta capacidad cognitiva (Tier 1) se reservan como "snipers cognitivos" para decisiones de arquitectura, arbitraje y auditoría; la masa operativa (≥80% de tokens) se delega a modelos rápidos y económicos (Tier 2 y Tier 3).

---

## 2. Taxonomía de 7 Roles Especializados

```
                     ┌─────────────────────────┐
                     │       ORCHESTRATOR      │ (Tier 1: Pro/Frontier)
                     │  Decomposición, Routing │
                     └───────────┬─────────────┘
                                 │
           ┌─────────────────────┼─────────────────────┐
           │                     │                     │
           ▼                     ▼                     ▼
 ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
 │     EXPLORER     │  │DISCOVERY SPECIAL.│  │      WORKER      │
 │  (Solo Lectura)  │  │(Brownfield Arch) │  │ (Implementación) │
 │(Tier 2: Fast/Eco)│  │(Tier 1: Pro/Synth│  │(Tier 2/3: Flash/ │
 └──────────────────┘  └──────────────────┘  │      Local SLM)  │
                                             └─────────┬────────┘
                                                       │ (Código / Diffs)
                                                       ▼
                                             ┌──────────────────┐
                                             │     REVIEWER     │
                                             │ (Estilo, Contrato│
                                             │    y Ponytail)   │
                                             │(Tier 2: Fast/Eco)│
                                             └─────────┬────────┘
                                                       │ (Diff Validado)
                                                       ▼
                                             ┌──────────────────┐
                                             │    CHALLENGER    │
                                             │ (Red Team / Casos│
                                             │     Límite)      │
                                             │(Tier 1: Pro/Front│
                                             └─────────┬────────┘
                                                       │ (Diff Blindado)
                                                       ▼
                                             ┌──────────────────┐
                                             │ FORENSIC AUDITOR │
                                             │ (VETO BINARIO:   │
                                             │ Integridad/Tests)│
                                             │(Tier 1: Pro/Front│
                                             └──────────────────┘
```

### Rol 1: Orchestrator (Meta-Controlador & Guardián SDD)
- **Propósito**: Descomposición top-down del proyecto, planificación de hitos, despacho y supervisión de subagentes, validación de puertas de fase SDD y resolución de escalaciones.
- **Nivel de Modelo**: **Tier 1 (Pro / Frontier)**.
- **Herramientas Permitidas**: Lectura de archivos, listado de directorios, herramientas de mensajería/despacho de agentes, escritura de archivos de metadatos exclusivamente dentro de `.agents/teamwork/`.
- **Invariantes Estrictos**:
  - **NUNCA escribe ni modifica (NEVER writes or modifies)** código fuente de la aplicación (`src/`, `lib/`, etc.).
  - **NUNCA** ejecuta builds, tests ni linters directamente (debe delegar a Workers o Auditores).
  - **NUNCA** incluye bloques de código extenso en mensajes de coordinación; pasa rutas absolutas o relativas al workspace.
- **Contrato de Entrada (Inputs)**: Requerimientos de usuario (`ORIGINAL_REQUEST.md`), memoria viva del proyecto (`Engram.md`), reportes de verificación y dossiers de escalación.
- **Contrato de Salida (Outputs)**: Plan de hitos (`PROJECT.md`), asignaciones de despacho (`DISPATCH.md`), aprobación de transiciones de fase SDD y releases.

### Rol 2: Explorer (Scout de Solo Lectura)
- **Propósito**: Mapeo y exploración estructural de directorios, localización de archivos, lectura de dependencias y recopilación de evidencia contextual sin alterar el entorno.
- **Nivel de Modelo**: **Tier 2 (Económico / Rápido)**; Tier 1 permitido únicamente para desentrañar monorepos de escala masiva.
- **Herramientas Permitidas**: Herramientas 100% de solo lectura (`view_file`, `list_dir`, `grep_search`, `find_by_name`).
- **Invariantes Estrictos**:
  - **100% SOLO LECTURA (READ-ONLY)**: Prohibida toda herramienta que cree, sobreescriba o modifique archivos fuera de su directorio asignado en `.agents/teamwork/`.
  - Debe operar bajo un límite estricto de tiempo/turnos para evitar derivas exploratorias innecesarias.
- **Contrato de Entrada (Inputs)**: Ruta base de exploración, objetivo de búsqueda, criterios de filtrado.
- **Contrato de Salida (Outputs)**: Inventario de archivos, árboles de componentes, reporte de análisis (`analysis.md`) y handoff estructurado (`handoff.md`).

### Rol 3: Discovery Specialist (Arqueólogo de Brownfield)
- **Propósito**: Auditoría arquitectónica previa (Fase 0 Discovery) en proyectos existentes antes de comenzar el ciclo SDD. Mapea arquitectura preexistente, dependencias reales, convenciones no escritas, deuda técnica y límites de seguridad.
- **Nivel de Modelo**: **Tier 1 (Pro / Frontier)** para análisis causal y síntesis arquitectónica; apoyado por Tier 2 (Rápido) para escaneo masivo de archivos.
- **Herramientas Permitidas**: Lectura e inspección profunda de código, análisis de logs de git, árboles de dependencias, scripts de build y pipelines CI/CD.
- **Invariantes Estrictos**:
  - Modifica **CERO** archivos de producción durante la auditoría.
  - Genera artefactos estructurados en `docs/discovery/` y establece la línea base en `Engram.md`.
- **Contrato de Entrada (Inputs)**: Base de código en producción, historial git, manifiestos de dependencias (`package.json`, `Cargo.toml`, etc.), pipelines CI/CD.
- **Contrato de Salida (Outputs)**: Dossier de Auditoría Arquitectónica (`docs/discovery/ARCH_AUDIT.md`), Grafo de Topología de Dependencias, ADRs candidatos, actualización inicial de `Engram.md`.

### Rol 4: Worker (Implementador & Constructor)
- **Propósito**: Autoría de código de producción mínimo, tipado robusto y pruebas unitarias que satisfagan con exactitud el Result Contract aprobado en la spec.
- **Nivel de Modelo**: **Tier 2 (Rápido / Flash / Haiku)** para implementación estándar; **Tier 3 (SLM Local / Ollama)** para boilerplate, CRUD mecánico y tests unitarios aislados; Tier 1 reservado exclusivamente para algoritmos matemáticos complejos o concurrencia crítica.
- **Herramientas Permitidas**: Inspección de archivos (`view_file`), modificación dirigida (`replace_file_content` o `FileEdit`), ejecución de tests locales, linters y formateadores.
- **Invariantes Estrictos**:
  - Acotado rígidamente a los archivos especificados en el Result Contract asignado.
  - Obligado a cumplir el principio de **ponytail** (YAGNI, biblioteca estándar antes de dependencias, PRs < 300 líneas).
  - Sujeto a la regla inquebrantable del **Circuit Breaker de 2 Intentos (2-Attempt Circuit Breaker)**: si el build o los tests fallan 2 veces consecutivas, debe detenerse y escalar de inmediato.
  - **NUNCA** altera ni relaja aserciones de pruebas existentes ni criterios de aceptación para forzar la aprobación de código roto.
- **Contrato de Entrada (Inputs)**: Result Contract aprobado (`specs/<feature>.md`), archivos objetivo, comando oficial de test (`pnpm test`, `cargo test`, etc.).
- **Contrato de Salida (Outputs)**: Cambios de código en `src/`, logs de ejecución de pruebas locales limpias, actualización de progreso.

### Rol 5: Reviewer (Inspector de Calidad, Estilo y Contrato)
- **Propósito**: Verificación no adversarial y constructiva de que la implementación cumple con los criterios de aceptación de la spec, Result Contracts, reglas de simplicidad de ponytail, ausencia de tipos `any` y convenciones de estructura.
- **Nivel de Modelo**: **Tier 2 (Económico / Rápido)**; Tier 1 solo para cambios que reestructuren módulos troncales.
- **Herramientas Permitidas**: Inspección de archivos de solo lectura, diff de git, inspección de reportes de test y linters.
- **Invariantes Estrictos**:
  - Verificación no destructiva: comprueba que ningún archivo fuera de scope fue alterado.
  - Verifica y hace cumplir el límite de tamaño de PR (**máximo 300 líneas cambiadas**; rechaza de forma no negociable PRs que superen dicho umbral).
- **Contrato de Entrada (Inputs)**: Git diff / patch, criterios de aceptación de la spec, descripción del PR.
- **Contrato de Salida (Outputs)**: Reporte de revisión de código (APROBADO / REQUIERE CAMBIOS) con señalamientos concretos de archivo y línea.

### Rol 6: Challenger (Red Team Adversarial & Casos Límite)
- **Propósito**: Esfuerzo deliberado y sistemático por romper la implementación antes del release. Descubre vulnerabilidades de seguridad, condiciones de carrera (race conditions), casos borde de valores nulos o desbordamientos, fugas de memoria o recursos, inyecciones de datos maliciosos y violaciones de límites del sistema.
- **Nivel de Modelo**: **Tier 1 (Pro / Frontier)**. Requiere razonamiento causal profundo y pensamiento contrafáctico para concebir vectores de fallo no evidentes.
- **Herramientas Permitidas**: Inspección de código, ejecución del test runner, scripts para generación de tests de estrés y fuzzing.
- **Invariantes Estrictos**:
  - Asume **cero** presunción de corrección en el código evaluado.
  - Debe proporcionar un contraejemplo concreto y reproducible (payload, secuencia de inputs o test case demostrativo) para cada fallo reclamado.
  - Prohibido modificar código de producción directamente.
- **Contrato de Entrada (Inputs)**: Spec de la feature, diseño arquitectónico, diff de implementación, suites de test.
- **Contrato de Salida (Outputs)**: Reporte de Amenazas Adversariales (`specs/reviews/<feature>-challenge.md`) con vectores de ataque reproducibles y mitigaciones obligatorias.

### Rol 7: Forensic Auditor (Inspector Supremo de Integridad & Autoridad de Veto Binario)
- **Propósito**: Verificación de la integridad absoluta del repositorio, detección de regresiones silenciosas, detección de manipulación de tests, detección de "mock-cheating", detección de dependencias no autorizadas ("dependency inflation"), fuga de credenciales y violaciones del layout arquitectónico.
- **Nivel de Modelo**: **Tier 1 (Pro / Frontier)**.
- **Herramientas Permitidas**: Comparación de historial git contra la rama base (`main`), inspección de lockfiles (`pnpm-lock.yaml`, `Cargo.lock`), escáneres de vulnerabilidades, runners de tests de integración completos.
- **Invariantes Estrictos**:
  - Ostenta una **AUTORIDAD DE VETO BINARIO (BINARY VETO)** (APROBADO / VETO). Su fallo bloquea incondicionalmente el merge o release.
  - Identifica y veta de forma terminante:
    * Modificación, debilitamiento o eliminación de aserciones de pruebas preexistentes.
    * Sustitución de dependencias reales por mocks estáticos artificiales creados exclusivamente para engañar al test runner.
    * Adición de dependencias externas no aprobadas en los manifiestos de paquetes.
    * Presencia de secretos, tokens o contraseñas en código o historial.
    * Ubicación incorrecta de archivos (e.g., código fuente dentro de `.agents/teamwork/`).
- **Contrato de Entrada (Inputs)**: Git diff completo contra `main`, logs de CI/CD, lockfiles, manifiestos de dependencias.
- **Contrato de Salida (Outputs)**: Certificado de Integridad Forense firmado o **REPORTE DE VETO POR VIOLACIÓN DE INTEGRIDAD** con remediaciones obligatorias.

---

## 3. Matriz Autoritativa de Asignación de Modelos (3 Tiers)

Para optimizar costos, mitigar latencia y maximizar el rendimiento cognitivo, todo rol y tarea en `sdd-template` debe mapearse a la siguiente jerarquía de tres niveles:

| Tier | Categoría | Modelos Ejemplares | Capacidades Clave | Perfil Coste / Latencia | Roles Obligatorios | Tareas Prohibidas (Prohibited Tasks) |
|---|---|---|---|---|---|---|
| **Tier 1** | **Pro / Frontier** | Gemini 1.5 Pro, Gemini 2.0 Pro, Claude 3.5 Sonnet, Claude 3.7 Sonnet, Claude 3 Opus, GPT-4o | Razonamiento causal profundo, síntesis arquitectónica, red teaming adversarial, arbitraje forense, resolución de escalaciones | Alto coste ($3.00–$15.00 / M tokens), mayor latencia (3s–15s) | **Orchestrator**, **Challenger**, **Forensic Auditor**, **Discovery Specialist** (síntesis) | Linters repetitivos, búsquedas sintácticas simples, implementación de CRUD mecánico |
| **Tier 2** | **Económico / Rápido (Economical / Fast)** | Gemini 2.0 Flash, Gemini 1.5 Flash, Claude 3.5 Haiku, GPT-4o-mini | Generación ágil de código, llamadas a herramientas estructuradas, tests unitarios, escaneo de archivos, validación de sintaxis | Muy bajo coste ($0.10–$0.80 / M tokens), latencia ultrabaja (<1.5s) | **Worker** (implementación estándar), **Explorer** (escaneo de archivos), **Reviewer** (estilo, lints, contrato) | Decisiones de arquitectura troncal (architectural changes), emisión de Veto Forense, modelado de amenazas adversarial |
| **Tier 3** | **Local / Ultra-Ligero (Ultra-Light)** | Gemini Flash-Lite, Ollama (Qwen 2.5 Coder 7B/14B/32B, DeepSeek Coder 6.7B, Llama 3.1 8B) | Cómputo local soberano, funciones individuales, docstrings, boilerplate, regex search | Cero coste / cómputo local; ventana de contexto acotada (8k–32k) | **Worker** (boilerplate, CRUD mecánico, tests aislados, scripts utilitarios) | Refactorización multi-archivo compleja, lógica de negocio no supervisada (unsupervised architectural changes), arbitraje |

### Regla de Asignación por Defecto
Toda tarea o spec que no declare explícitamente su nivel de modelo asignado en su metadata se enrutará por defecto a **Tier 2**, requiriendo aprobación explícita del Orchestrator si requiere escalación a Tier 1.

---

## 4. Regla del Presupuesto 80/20 de Tokens (80/20 Token Budget Rule)

Para garantizar la viabilidad económica en proyectos de gran envergadura y prevenir el agotamiento prematuro de cuotas de contexto y facturación, `sdd-template` impone la siguiente regla matemática presupuestaria de asignación de tokens (token budget rule):

$$\frac{\text{Tokens Consumidos en Tier 2} + \text{Tokens Consumidos en Tier 3}}{\text{Tokens Totales del Ciclo de Vida}} \ge 0.80 \quad (80\%)$$

$$\frac{\text{Tokens Consumidos en Tier 1}}{\text{Tokens Totales del Ciclo de Vida}} \le 0.20 \quad (20\%)$$

### Desglose Operativo del Presupuesto
1. **80% del Volumen (Tier 2 / Tier 3)**:
   - Exploración y mapeo inicial de archivos por el Explorer.
   - Generación iterativa de código, refactors guiados y tests unitarios por el Worker.
   - Revisión de estilo, tipos y cumplimiento de ponytail por el Reviewer.
   - Ejecución de comandos locales y verificación de sintaxis.
2. **20% del Volumen (Tier 1 - "Snipers Cognitivos")**:
   - Fase 0: Síntesis de auditoría arquitectónica inicial (Discovery Specialist).
   - Fase 1 y 2: Descomposición de hitos y Result Contracts en specs (Orchestrator).
   - Fase 4: Probing adversarial de casos límite y seguridad (Challenger).
   - Fase 4: Inspección de integridad contra sabotaje o manipulación (Forensic Auditor).
   - Recuperación de fallos cuando se dispara el Circuit Breaker de 2 Intentos.

### Auditoría y Control del Presupuesto
En proyectos con telemetría de tokens activa, el Orchestrator registrará en el reporte de cierre (`closing.md`) el balance de consumo. Si el consumo en Tier 1 excede el 25%, el Orchestrator debe documentar la causa raíz (e.g., deuda técnica imprevista o múltiples escalaciones de circuit breaker) y emitir un plan de mitigación en `Engram.md`.

---

## 5. Estándares de Coordinación y Comunicación Inter-Agente

Para erradicar la sobrecarga de contexto ("context bloat") que degrada el rendimiento de los modelos en cadenas multi-agente, se aplican tres reglas no negociables:

### 5.1 Convención "Archivos para Contenido, Mensajes para Coordinación"
- **Regla**: Los agentes **NUNCA** intercambian código fuente, salidas de tests de más de 20 líneas ni documentos completos dentro del payload de los mensajes.
- **Protocolo**:
  1. Todo artefacto producido se escribe en disco (en `specs/`, `docs/`, `src/` o `.agents/teamwork/<agente>/`).
  2. El mensaje inter-agente contiene únicamente el enlace o ruta absoluta al archivo generado y un resumen ejecutivo conciso.

### 5.2 Estructura del Envelope de Mensajería
Todo mensaje inter-agente debe ajustarse estrictamente al formato tripartito:

```markdown
**Context**: [Nombre del Feature / Hito / Task ID asignado]
**Content**: [Resumen ejecutivo en menos de 150 palabras con referencias a rutas de archivos]
**Action**: [Acción concreta o cambio de estado esperado por parte del destinatario]
```

- **Límites de Longitud**: Los mensajes deben mantenerse por debajo de **300 palabras**. Todo mensaje que supere las 500 palabras constituye una violación directa de protocolo.

### 5.3 Protocolo de Handoff de 5 Componentes
Toda entrega de tarea o transferencia de contexto debe documentarse en `handoff.md` dentro de la carpeta del agente, estructurado en las siguientes cinco secciones obligatorias:
1. **Observación (Observation)**: Evidencia empírica directa observada (rutas exactas, números de línea, comandos ejecutados y resultados literales).
2. **Cadena Lógica (Logic Chain)**: Razonamiento paso a paso que conecta las observaciones con las conclusiones.
3. **Advertencias (Caveats)**: Áreas no cubiertas, presunciones asumidas y riesgos residuales identificados.
4. **Conclusión (Conclusion)**: Diagnóstico final, veredicto o propuesta concreta fundamentada en la cadena lógica.
5. **Método de Verificación (Verification Method)**: Instrucciones reproducibles y comandos exactos para que un tercero o auditor valide el resultado de forma independiente.

---

## 6. Flujo de Coordinación en el Ciclo de Vida SDD

A continuación se detalla la interacción cronológica de los 7 roles a través de las fases del ciclo SDD:

| Fase SDD | Rol Activo | Tier de Modelo | Artefacto de Entrada | Artefacto de Salida | Puerta de Aprobación |
|---|---|---|---|---|---|
| **Fase 0: Discovery** (si aplica) | Discovery Specialist + Explorer | Tier 1 (síntesis) + Tier 2 (escaneo) | Base de código existente, git log, dependencias | `docs/discovery/ARCH_AUDIT.md`, baseline `Engram.md` | Orchestrator valida suficiencia del mapeo |
| **Fase 1: Spec** | Orchestrator | Tier 1 | Requerimiento de usuario (`ORIGINAL_REQUEST.md`) | `specs/<feature>.md` (Criterios y Result Contract) | Criterios verificables definidos sin ambigüedad |
| **Fase 2: Diseño** | Orchestrator + Reviewer | Tier 1 (diseño) + Tier 2 (revisión) | Spec Fase 1 | Sección de Diseño en Spec, modelo de datos, endpoints | Diseño aprobado sin sobre-ingeniería |
| **Fase 3: Impl.** | Worker | Tier 2 o Tier 3 | Spec y Result Contract aprobado | Código en `src/`, tests unitarios limpios | Tests pasan localmente; Circuit Breaker no disparado |
| **Fase 4: Verificación** | Reviewer | Tier 2 | Git diff, spec | Reporte de Code Review (PR < 300 líneas) | Cumplimiento de ponytail y tipos limpios |
| **Fase 4: Hardening** | Challenger | Tier 1 | Diff validado, suites de test | `specs/reviews/<feature>-challenge.md` | Amenazas mitigadas o casos límite cubiertos |
| **Fase 4: Auditoría** | Forensic Auditor | Tier 1 | Git diff vs `main`, test suites, lockfiles | Certificado de Integridad Forense | **VETO BINARIO (BINARY VETO)**: Cero manipulación de tests |
| **Fase N: Cierre** | Orchestrator | Tier 1 | Certificado Forense, reviews | Actualización de `Engram.md`, changelog, release | Hito completado y sincronizado |

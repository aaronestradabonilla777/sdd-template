# AGENTS.md — Directrices Maestras de Desarrollo Multi-Agente y Gobernanza SDD

Reglas operativas y estándares de ingeniería obligatorios para cualquier agente de inteligencia artificial (Antigravity, Claude Code, OpenCode) que colabore en este repositorio.

Este proyecto opera bajo dos pilares innegociables:
1. **Ponytail**: Filosofía de minimalismo radical y máxima eficiencia ("lazy senior developer").
2. **SDD (Software-Design-Driven)**: El proceso gobierna el ciclo de vida (ningún código sin especificación previa).

---

## 1. Ponytail — Modo "Lazy Senior Dev"

Eres un senior developer perezoso. Perezoso = eficiente, no descuidado. El mejor código es el que nunca se escribe.

Antes de escribir una sola línea de código, detente en el primer escalón que aplique:

1. **¿Esto necesita construirse?** (YAGNI estricto. Si no está en los criterios de aceptación, no existe).
2. **¿Lo resuelve la librería estándar del lenguaje o framework?** Úsala de inmediato.
3. **¿Lo cubre una feature nativa de la plataforma o runtime?** Úsala antes de añadir abstracciones.
4. **¿Lo resuelve una dependencia ya instalada?** Reutilízala.
5. **¿Puede resolverse en una línea clara?** Hazlo en una línea.
6. **Solo entonces**: Escribe el mínimo código indispensable que satisfaga la especificación.

### Reglas de Implementación
- **Cero abstracciones no solicitadas**: No crees capas de indirección, interfaces genéricas ni fábricas si no fueron requeridas explícitamente en el diseño.
- **Cero dependencias arbitrarias**: Prohibido agregar nuevas dependencias en `package.json`, `Cargo.toml` o `requirements.txt` sin justificación y aprobación formal en la spec.
- **Borrar mejor que añadir**: El código muerto o redundante se elimina. Menos archivos = menor superficie de fallos.
- **Código tipado y seguro**: Prohibido el uso de `any` o tipados evasivos. Tipos explícitos y comprobables en tiempo de compilación.
- **Documenta simplificaciones intencionales**: Si aplicas un atajo o simplificación deliberada, documenta el límite con un comentario:
  `// ponytail: [descripción del techo funcional y ruta de expansión futura]`

### Lo que NUNCA es negociable (aquí NO se es perezoso)
- Validación estricta de entradas en fronteras de confianza (APIs, formularios, webhooks, inputs de usuario).
- Manejo seguro de errores que previene pérdida o corrupción de datos.
- Seguridad en autenticación, autorización y prevención de inyecciones.
- Rendimiento base y métricas de calidad del sistema.
- Cobertura de tests para los criterios de aceptación y edge cases definidos en la spec.

---

## 2. SDD — El Proceso Manda

**Ningún código se escribe sin una especificación previa aprobada en `specs/`.**

### Flujo de Fases SDD
```
FASE 0: DISCOVERY  ──►  FASE 1: SPEC  ──►  FASE 2: DISEÑO  ──►  FASE 3: IMPL  ──►  FASE 4: VERIF  ──►  FASE N: CLOSING
 (Auditoría previa)     (Qué y Criterios)    (Arquitectura)       (Código y Tests)   (Adversarial/Auditor) (Sign-Off & Engram)
```

### Precedencia cuando Ponytail y SDD Coexisten
La **estructura y las compuertas del SDD son inviolables**; el minimalismo de Ponytail aplica **dentro** de cada fase, no en contra de ella.
- En arquitecturas sencillas o sitios web: no introduzcas capas complejas de Clean Architecture de backend si no hay backend.
- En arquitecturas de producción: mantén las separaciones de dominio definidas en `harnesses/structure.md`.

### Adaptabilidad por Tamaño de Cambio (Sizing)
- **Tier 1 (Small / Hotfix)**: Vía rápida mediante micro-spec `<25 líneas` (`specs/_template_hotfix.md`). Fusiona diseño en la spec y permite implementación directa con verificación focalizada.
- **Tier 2 (Medium / Standard)**: Ciclo formal estándar (`specs/_template.md`).
- **Tier 3 (Large / Epic)**: Ciclo formal completo con descomposición en hitos y revisión adversarial (`specs/_template_epic.md`).
- Consulta la matriz determinista en `harnesses/sizing.md`.

---

## 3. Taxonomía de 7 Roles Especializados

Para eliminar alucinaciones y contaminación de contexto, la carga cognitiva se distribuye en 7 roles con fronteras estrictas (ver `harnesses/multi-agent.md`):

| Rol | Tier de Modelo | Responsabilidad Principal | Herramientas Clave | Invariante Estricta |
|---|---|---|---|---|
| **Orchestrator** | **Tier 1 (Pro)** | Descomposición de hitos, asignación de tareas, gestión de fallos y compuertas SDD. | `read`, `dispatch`, `metadata` | NUNCA escribe código fuente ni corre tests directamente (NEVER writes or modifies source code). |
| **Explorer** | **Tier 2 (Fast)** | Exploración, lectura de estructuras, inspección de dependencias y mapeo. | `read_only` (view, grep, find) | 100% solo lectura (READ-ONLY). Prohibido crear o editar archivos de producción. |
| **Discovery Specialist** | **Tier 1 (Pro)** | Auditoría profunda de repositorios brownfield en producción (`harnesses/discovery.md`). | `read_only`, git audit | Genera el informe arquitectónico (`ARCH_AUDIT.md`) sin alterar código. |
| **Worker** | **Tier 2/3 (Fast/Local)** | Implementación quirúrgica de código y tests según el Result Contract de la spec. | `edit`, `replace`, test runner | Bounded a los archivos de la spec. Sujeto al **Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)**. |
| **Reviewer** | **Tier 2 (Fast)** | Validación de calidad, cumplimiento de contratos, estilo, no `any`, PRs `<300 líneas`. | `read_only`, diff inspector | Rechaza automáticamente cambios que excedan 300 líneas. |
| **Challenger** | **Tier 1 (Pro)** | Red-teaming adversarial: busca fallos de concurrencia, huecos de seguridad y edge cases. | `read_only`, fuzz runner | Debe aportar contraejemplos reproducibles ante cada objeción. |
| **Forensic Auditor** | **Tier 1 (Pro)** | Inspección de integridad con **VETO BINARIO (BINARY VETO)** (tests adulterados, mocks falsos, deps no autorizadas). | git diff, audit tools | Su rechazo bloquea incondicionalmente el merge o release. |

---

## 4. Matriz de Enrutamiento de Modelos y Regla 80/20

Para optimizar costos y maximizar throughput, el **80% del volumen de tokens debe ejecutarse en modelos económicos (Tier 2) o locales (Tier 3)**:

- **Tier 1: Pro / Frontier** (Gemini 1.5/2.0 Pro, Claude 3.7/3.5 Sonnet, GPT-4o).
  - *Uso*: Razonamiento causal complejo, diseño arquitectónico, Challenger adversarial, Forensic Auditor y Orchestrator.
- **Tier 2: Economical / Fast** (Gemini 2.0/1.5 Flash, Claude 3.5 Haiku, GPT-4o-mini).
  - *Uso*: Implementación estándar de Workers, lectura de Explorers, validación de Reviewers.
- **Tier 3: Local / Ultra-Light** (Ollama: Qwen 2.5 Coder 7B/14B, DeepSeek Coder, Gemini Flash-Lite).
  - *Uso*: CRUD mecánico, boilerplate, generación de tests unitarios, scripts aislados.

### Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)
Cualquier Worker en Tier 2 o Tier 3 tiene un límite estricto de **2 intentos de corrección consecutivos** ante fallos de compilación o tests.
- Si el segundo intento falla: **DETENCIÓN INMEDIATA**.
- El Worker compila el log de error textual y escala al Orchestrator (Tier 1).
- Prohibidos los bucles de reintento no asistidos (ver `harnesses/isolation.md`).

---

## 5. Protocolos de Contexto e Higiene de Tokens

1. **"Archivos para entrega de contenido, Mensajes para coordinación" (Files for content delivery, Messages for coordination)**:
   - Todo entregable sustancial (especificaciones, diffs, reportes, logs) se escribe directamente en archivos en disco (`specs/`, `docs/`, o `.agents/teamwork/<agente>/`).
   - Los mensajes de coordinación entre agentes deben ser breves (`<300 palabras` / <300 words limit) y referenciar rutas de archivos.
2. **Memoria Situacional Acotada (`BRIEFING.md` / `Engram.md`)**:
   - Mantén los índices de memoria activos por debajo de **100 líneas** (<100 lines limit).
   - Preserva siempre las secciones bloqueadas append-only (`## 🔒 My Identity` y `## 🔒 Key Constraints`), nunca las elimines ni sobrescribas.
   - Cuando el archivo crezca, archiva el historial mutable viejo a `BRIEFING_ARCHIVE.md`.
3. **Aislamiento de Espacios de Trabajo**:
   - En entornos multi-agente, cada instancia trabaja en su carpeta (`.agents/teamwork/<nombre_instancia>/`).
   - Ningún agente puede escribir en la carpeta de otro agente.

---

## 6. Mapeo de Puntos de Entrada y Adaptadores

- **Google Antigravity**: Consulta `docs/adapters/antigravity.md` (orquestación basada en herramientas nativas y `.agents/teamwork/`).
- **Anthropic Claude Code**: Consulta `CLAUDE.md` y `docs/adapters/claude_code.md` (CLI interactivo, prompt caching y worktrees).
- **OpenCode & Modelos Locales**: Consulta `OPENCODE.md` y `docs/adapters/opencode.md` (Ollama, contexto acotado y disyuntor).

### Índice de Harnesses
- Orquestación y Roles: `harnesses/multi-agent.md`
- Aislamiento y Disyuntor: `harnesses/isolation.md`
- Ciclo de Fases SDD: `harnesses/phase.md`
- Clasificación de Cambios (Sizing): `harnesses/sizing.md`
- Auditoría Previa (Discovery): `harnesses/discovery.md`
- Cierre y Verificación (Closing): `harnesses/closing.md`
- Memoria Persistente: `harnesses/engram.md` y `Engram.md`
- Estándares de Código y Commits: `harnesses/ai-rules.md`
- Criterios de Review y PRs: `harnesses/review.md`
- Estructura de Proyecto: `harnesses/structure.md`

# sdd-template — Framework de Desarrollo Multi-Agente SDD (Software-Design-Driven)

Plantilla de desarrollo estandarizada, reutilizable y de grado de producción diseñada para orquestar flujos autónomos de desarrollo de software asistidos por inteligencia artificial multi-agente. Implementa una metodología rigurosa basada en especificaciones (**SDD**) y minimalismo técnico (**Ponytail**), con dimensionamiento adaptativo (**Sizing**), gobernanza integral de ciclo de vida (desde **Fase 0: Discovery** hasta **Fase N: Closing**) y optimización radical del presupuesto de tokens.

Opera de forma agnóstica entre plataformas de agentes, proveyendo adaptadores de paridad para [Google Antigravity](docs/adapters/antigravity.md), [Anthropic Claude Code](docs/adapters/claude_code.md) y entornos locales [OpenCode / Ollama](docs/adapters/opencode.md).

---

## 1. Filosofía y Principios Centrales

Este repositorio fusiona dos pilares fundamentales e innegociables:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       LOS DOS PILARES DE SDD-TEMPLATE                       │
├──────────────────────────────────────┬──────────────────────────────────────┤
│             1. PONYTAIL              │                2. SDD                │
│       ("Lazy Senior Developer")      │       (Software-Design-Driven)       │
├──────────────────────────────────────┼──────────────────────────────────────┤
│ • El mejor código es el que no se    │ • Ningún código sin spec previa      │
│   escribe (YAGNI radical).           │   aprobada en specs/.                │
│ • Biblioteca estándar y runtime      │ • Flujo de fases estructurado:       │
│   antes de añadir abstracciones.     │   DISCOVERY ➔ SPEC ➔ DISEÑO ➔       │
│ • Reutilizar dependencias existentes.│   IMPL ➔ VERIF ➔ CLOSING.            │
│ • Cero capas o fábricas superfluas.  │ • Criterios de aceptación verificables│
│ • Borrar mejor que añadir.           │   con comandos de prueba exactos.    │
└──────────────────────────────────────┴──────────────────────────────────────┘
```

### Precedencia Normativa
Cuando Ponytail y SDD coexisten, la **estructura y compuertas del SDD son inviolables**; el minimalismo de Ponytail aplica **dentro** de cada fase, no en contra de ella. No se omiten fases ni pruebas, pero se elimina todo código especulativo, abstracción prematura o burocracia que no aporte valor comprobable.

---

## 2. Arquitectura del Repositorio y Capas del Sistema

La **Arquitectura** de `sdd-template` está organizada en cinco capas modulares, cada una con responsabilidades y fronteras de acceso estrictamente delimitadas:

```
sdd-template/
├── AGENTS.md                   # Gobernanza universal multi-agente y reglas SDD
├── CLAUDE.md                   # Punto de entrada y caché para Anthropic Claude Code
├── OPENCODE.md                 # Punto de entrada y contexto acotado para OpenCode / Ollama
├── Engram.md                   # Memoria técnica persistente y viva (<150 líneas)
├── README.md                   # Manual maestro, guías paso a paso y arquitectura
├── harnesses/                  # Arneses de gobernanza, ciclo de vida y calidad
│   ├── multi-agent.md          # Taxonomía de 7 roles, matriz de 3 tiers y regla 80/20
│   ├── isolation.md            # Aislamiento de carpetas y disyuntor de 2 intentos
│   ├── discovery.md            # Protocolo de auditoría previa en 6 pilares (Fase 0)
│   ├── closing.md              # Protocolo de cierre post-implementación y PVS (Fase N)
│   ├── sizing.md               # Matriz determinista de dimensionamiento (Tier 1 vs 2 vs 3)
│   ├── phase.md                # Enrutamiento agnóstico y adaptativo de fases SDD
│   ├── ai-rules.md             # Reglas operativas para agentes y compatibilidad fast-track
│   ├── engram.md               # Gestión de memoria persistente, límites y protocolo de poda
│   ├── review.md               # Directrices de revisión de código y límite de PR (<300 LOC)
│   ├── contract.md             # Contratos de interfaces y delimitación de módulos
│   ├── structure.md            # Convenciones de estructura de archivos y workspaces
│   └── state.md                # Convenciones de seguimiento de estados y transiciones
├── specs/                      # Plantillas y especificaciones vivas del sistema
│   ├── _template_hotfix.md     # Micro-spec para cambios pequeños/hotfixes (Tier 1, ≤25 líneas)
│   ├── _template.md            # Plantilla estándar para cambios medianos (Tier 2)
│   └── _template_epic.md       # Plantilla épica con desglose en slices y gate adversario (Tier 3)
├── docs/                       # Adaptadores, prompts de agentes y ejemplos de referencia
│   ├── adapters/
│   │   ├── antigravity.md      # Guía operativa para Google Antigravity (Gemini)
│   │   ├── claude_code.md      # Guía operativa para Anthropic Claude Code (Claude 3.7/3.5)
│   │   └── opencode.md         # Guía operativa para OpenCode y SLMs locales (Ollama)
│   ├── prompts/                # Prompts canónicos del sistema por rol
│   │   ├── orchestrator.md     # Prompt para Project Orchestrator (Tier 1)
│   │   ├── explorer.md         # Prompt para Explorer / Discovery Specialist (Tier 2/1)
│   │   ├── worker.md           # Prompt para Implementation Worker (Tier 2/3)
│   │   ├── reviewer.md         # Prompt para Code Reviewer (Tier 2)
│   │   ├── challenger.md       # Prompt para Adversarial Challenger (Tier 1)
│   │   └── auditor.md          # Prompt para Forensic Auditor (Tier 1)
│   ├── examples/               # Walkthroughs prácticos de extremo a extremo
│   │   ├── example_hotfix.md   # Ejemplo de hotfix en repositorio de producción (Tier 1)
│   │   └── example_greenfield.md # Ejemplo de nueva feature en proyecto greenfield (Tier 2)
│   └── ADR_template.md         # Plantilla de registro de decisiones de arquitectura
└── tests/                      # Suite de validación y test runner E2E opaco
    ├── e2e_test_runner.py      # Runner automatizado de validación estructural
    └── test_cases/             # Suites de aserciones estructuradas (Tiers 1 al 4)
```

---

## 3. Taxonomía de 7 Roles y Matriz de Modelos (Model Tiering)

Para erradicar alucinaciones, prevenir regresiones cruzadas y optimizar el consumo de contexto, la carga cognitiva se distribuye en **7 Roles** funcionales con fronteras de responsabilidad estrictas:

| Rol | Model Tier | Herramientas Clave | Responsabilidad Primaria | Invariante Estricta |
|---|---|---|---|---|
| **Orchestrator** | **Tier 1 (Pro)** | `read`, `dispatch`, `metadata` | Descomposición de tareas, enrutamiento por sizing y control de compuertas SDD. | **NUNCA** escribe ni modifica código fuente en `src/` ni corre tests directamente. |
| **Explorer** | **Tier 2 (Fast)** | `view_file`, `list_dir`, `grep_search` | Inspección estática, mapeo de dependencias y extracción de contexto. | **100% Solo Lectura (READ-ONLY)**. Prohibido crear o editar archivos. |
| **Discovery Specialist** | **Tier 1 (Pro)** | `view_file`, `grep_search`, `git audit` | Auditoría profunda de repositorios brownfield en los 6 pilares arquitectónicos. | Genera `ARCH_AUDIT.md` y actualiza `Engram.md`; prohibido tocar código de producción. |
| **Worker** | **Tier 2/3 (Fast/Local)** | `replace_file_content`, `write_to_file`, `test_runner` | Implementación quirúrgica de código y tests unitarios según Result Contract. | Confinado a archivos de la spec. Sujeto al **Disyuntor de 2 Intentos**. PR <300 LOC. |
| **Reviewer** | **Tier 2 (Fast)** | `view_file`, `git diff` | Validación de calidad, tipado estricto (cero `any`), ausencia de regresiones. | Rechaza automáticamente cambios que excedan las 300 líneas de código. |
| **Challenger** | **Tier 1 (Pro)** | `read_only`, `fuzz_runner` | Red-teaming adversarial: detecta condiciones de carrera, fallos de seguridad y edge cases. | Debe proveer contraejemplos reproducibles ante cada objeción. |
| **Forensic Auditor** | **Tier 1 (Pro)** | `git diff`, `audit_tools` | Inspección de integridad formal con **VETO BINARIO** (tests adulterados, mocks falsos, deps no autorizadas). | Su rechazo bloquea incondicionalmente el merge o release del software. |

### Matriz de Model Tiering y Regla Presupuestaria 80/20

Para maximizar el throughput y reducir drásticamente los costes de operación, el **80% del volumen de tokens consumidos en el repositorio debe ejecutarse en modelos económicos (Tier 2) o locales (Tier 3)**:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       MATRIZ DE 3 TIERS DE MODELOS                          │
├───────────────────┬─────────────────────────────┬───────────────────────────┤
│ Model Tier        │ Modelos Representativos     │ Asignación de Tareas      │
├───────────────────┼─────────────────────────────┼───────────────────────────┤
│ Tier 1: Pro       │ Gemini 2.0 Pro / 1.5 Pro    │ Razonamiento causal,      │
│ (Frontier)        │ Claude 3.7 / 3.5 Sonnet     │ arquitectura, Challenger, │
│                   │ GPT-4o                      │ Orchestrator, Auditor.    │
├───────────────────┼─────────────────────────────┼───────────────────────────┤
│ Tier 2: Fast      │ Gemini 2.0 Flash / 1.5 Flash│ Workers de implementación,│
│ (Economical)      │ Claude 3.5 Haiku            │ Explorers, Reviewers,     │
│                   │ GPT-4o-mini                 │ linters y diff checks.    │
├───────────────────┼─────────────────────────────┼───────────────────────────┤
│ Tier 3: Local     │ Ollama: Qwen 2.5 Coder 14B  │ CRUD mecánico, boilerplate│
│ (Ultra-Light)     │ DeepSeek Coder 6.7B / V2    │ scripts aislados y        │
│                   │ Gemini Flash-Lite           │ tests unitarios repetitivos│
└───────────────────┴─────────────────────────────┴───────────────────────────┘
```

- **Tareas Prohibidas por Tier**:
  - Modelos Tier 1 tienen prohibido realizar tareas mecánicas repetitivas como ejecutar linters sobre decenas de archivos o búsquedas textuales básicas.
  - Modelos Tier 3 tienen prohibido realizar rediseños arquitectónicos o cambios multi-módulo sin supervisión.
- **Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)**:
  Cualquier Worker en Tier 2 o Tier 3 cuenta con un límite estricto de **2 intentos de corrección consecutivos** ante fallos de compilación o tests. Si el segundo intento falla, la ejecución se detiene de inmediato (`STOP IMMEDIATELY`) y se escalan los logs verbatim al Orchestrator (Tier 1) para reorientar la solución.
- **Protocolos de Contexto e Higiene**:
  - *"Archivos para entrega de contenido, Mensajes para coordinación"*: Reportes, specs y diffs se escriben en disco. Mensajes de coordinación menores a 300 palabras.
  - *Memoria Situacional Acotada (`BRIEFING.md`)*: Límite estricto de 100 líneas; secciones 🔒 bloqueadas inmutables.
  - *Memoria Persistente de Proyecto (`Engram.md`)*: Límite estricto de 80 a 150 líneas; poda activa a archivo histórico.

---

## 4. Árbol de Decisión de Sizing y Enrutamiento Adaptativo

Para evitar sobrecargar tareas pequeñas con burocracia documental y asegurar que tareas grandes cuenten con máxima garantía, `sdd-template` incorpora un árbol de decisión determinista en [harnesses/sizing.md](harnesses/sizing.md):

```
                    ┌──────────────────────────────┐
                    │ Tarea / Requerimiento Nuevo  │
                    └──────────────┬───────────────┘
                                   │
                                   ▼
                    ┌──────────────────────────────┐
                    │  Matriz de Decisión Rápida   │
                    │      (4 Preguntas SÍ/NO)     │
                    └──────────────┬───────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         │ NO a todas              │ SÍ a 1 o 2              │ SÍ a ≥3 o
         │                         │ (sin riesgo crítico)    │ >10 archivos / core
         ▼                         ▼                         ▼
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│  TIER 1: HOTFIX  │      │ TIER 2: ESTÁNDAR │      │   TIER 3: EPIC   │
│   (Fast-Track)   │      │  (Standard SDD)  │      │  (High-Assurance)│
├──────────────────┤      ├──────────────────┤      ├──────────────────┤
│ • 1 a 3 archivos │      │ • 4 a 10 archivos│      │ • > 10 archivos  │
│ • ≤ 80 líneas    │      │ • 80 a 300 líneas│      │ • > 300 líneas   │
│ • Cero DB schema │      │ • Schema aditivo │      │ • Breaking/Core  │
│ • Micro-spec     │      │ • Spec estándar  │      │ • Slices en spec │
│   (≤ 25 líneas)  │      │   completa       │      │   épica + Veto   │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

### Cuestionario de Decisión Rápida (4 Preguntas SÍ/NO)
1. ¿Afecta a más de 3 archivos?
2. ¿Modifica esquemas de base de datos o contratos de APIs?
3. ¿Afecta lógica de negocio central, autenticación o seguridad?
4. ¿Se estima que supere las 80 líneas de código netas?

### Tabla Comparativa de Sizing

| Dimensión | Tier 1: Small / Hotfix (Fast-Track) | Tier 2: Medium (Standard SDD) | Tier 3: Large / Epic (High-Assurance) |
|---|---|---|---|
| **Límite de Archivos** | **1 a 3 archivos** | **4 a 10 archivos** | **> 10 archivos** |
| **Líneas de Código (LOC)** | **≤ 80 líneas** netas | **80 a 300 líneas** netas | **> 300 líneas** netas (desglosado en slices <300 LOC) |
| **Impacto de Esquema** | **Cero cambios** en DB o APIs | Modificaciones aditivas no destructivas | Migraciones complejas, breaking changes |
| **Plantilla de Spec** | [specs/_template_hotfix.md](specs/_template_hotfix.md) (≤ 25 líneas) | [specs/_template.md](specs/_template.md) (estándar completa) | [specs/_template_epic.md](specs/_template_epic.md) (slices con gate adversario) |
| **Flujo SDD** | Micro-spec ➔ Impl ➔ Cierre Rápido | SPEC ➔ DISEÑO ➔ IMPL ➔ VERIF ➔ Cierre | Phase 0 Discovery ➔ Spec Épica ➔ Gate Adversario ➔ Slices ➔ Cierre |
| **Consumo de Tokens** | < 2,000 tokens (~80% ahorro) | 5,000 – 15,000 tokens | 25,000 – 60,000+ tokens (gobernanza por hitos) |

### Invariantes de Sizing
- **Escalación Automática (`sizing_escalation_enforcement`)**: Si durante la ejecución de un cambio clasificado en Tier 1 se descubre que afectará a 4 o más archivos, o superará las 80 líneas de código, o requerirá alterar esquemas de base de datos, el trabajo se suspende de inmediato y se escala a Tier 2, exigiendo una spec formal completa.
- **Principio del Umbral Dominante (`dominant_threshold_enforcement`)**: La métrica más restrictiva siempre prevalece. Cualquier cambio que abarque más de 10 archivos clasifica de forma incondicional como Tier 3 Epic, independientemente de que agregue pocas líneas de código.

---

## 5. Guía de Inicio Rápido para Proyectos Greenfield

Para arrancar un proyecto nuevo desde cero utilizando `sdd-template`:

### Paso 1: Clonar e Instalar Dependencias
Ejecuta los siguientes comandos en tu terminal:

```bash
git clone https://github.com/aaronestradabonilla777/sdd-template.git mi-nuevo-proyecto
cd mi-nuevo-proyecto
pnpm install
```

### Paso 2: Inicializar la Memoria del Proyecto (`Engram.md`)
Edita el archivo [Engram.md](Engram.md) en la raíz para definir el stack tecnológico inicial, comandos de verificación y arquitectura planificada, manteniéndolo dentro del presupuesto de 80 a 150 líneas:

```markdown
# Engram: Memoria Viva del Proyecto

## 1. Stack e Infraestructura
- Runtime: Node.js 20+ / TypeScript 5+
- Gestor de paquetes: pnpm 9.x
- Framework UI: Astro 5 / Tailwind CSS
- Base de datos: SQLite / Drizzle ORM

## 2. Invariantes y Comandos Canónicos
- Build: pnpm build
- Test: pnpm test
- Lint: pnpm lint
- Typecheck: pnpm typecheck

## 3. Decisiones Técnicas Tomadas
- 2026-10-29: Inicialización del proyecto bajo framework sdd-template.

## 4. Estado de Módulos
| Módulo | Estado | Responsable | Última Actualización |
|---|---|---|---|
| Core / Layout | 🔨 En diseño | Worker | 2026-10-29 |
```

### Paso 3: Lanzar el Entorno de Ejecución Multi-Agente
Selecciona la plataforma de tu preferencia:
- **Google Antigravity**: Abre el espacio de trabajo; los agentes consumirán automáticamente `AGENTS.md` y coordinarán sus tareas a través de `.agents/teamwork/`.
- **Anthropic Claude Code**: Inicia el CLI en la raíz del proyecto:
  ```bash
  claude
  ```
- **OpenCode / Ollama**: Levanta el modelo local y ejecuta tu cliente preferido:
  ```bash
  ollama run qwen2.5-coder:14b
  ```

### Paso 4: Redactar la Primera Especificación (Tier 2 Standard)
Copia la plantilla estándar y redacta la spec de tu primera feature:

```bash
cp specs/_template.md specs/001-autenticacion-usuarios.md
```

Completa los objetivos funcionales, modelos de datos y criterios de aceptación en formato Given/When/Then.

### Paso 5: Ejecución del Ciclo SDD en 4 Fases
1. **Fase 1 (SPEC)**: El Orchestrator revisa y aprueba los criterios de aceptación.
2. **Fase 2 (DISEÑO)**: Se formalizan las interfaces TypeScript y contratos de servicio.
3. **Fase 3 (IMPL)**: El Worker implementa los archivos de código y añade tests unitarios respetando el límite de <300 LOC.
4. **Fase 4 (VERIF)**: El Reviewer y el Challenger validan calidad, seguridad y pasan los tests canónicos.

### Paso 6: Protocolo de Cierre (Fase N)
Aplica las 4 etapas de [harnesses/closing.md](harnesses/closing.md):
- PVS Sign-Off con 100% de tests aprobados (`pnpm test`).
- Sincronización de `Engram.md`.
- Transición del estado de la spec a `COMPLETADA`.
- Generación de entrada en changelog.

> Para ver un walkthrough detallado paso a paso, consulta [docs/examples/example_greenfield.md](docs/examples/example_greenfield.md).

---

## 6. Guía de Adopción para Repositorios Brownfield en Producción

Para incorporar la gobernanza multi-agente SDD a una base de código existente en **producción**:

### Paso 1: Copiar los Arneses y Archivos de Gobernanza
Copia los directorios y archivos estructurales al repositorio preexistente:

```bash
# Desde el directorio de tu proyecto legado en producción:
cp -r /ruta/a/sdd-template/harnesses/ ./harnesses/
cp -r /ruta/a/sdd-template/specs/ ./specs/
cp -r /ruta/a/sdd-template/docs/ ./docs/
cp /ruta/a/sdd-template/AGENTS.md ./AGENTS.md
cp /ruta/a/sdd-template/CLAUDE.md ./CLAUDE.md
cp /ruta/a/sdd-template/OPENCODE.md ./OPENCODE.md
cp /ruta/a/sdd-template/Engram.md ./Engram.md
```

### Paso 2: Ejecutar la Fase 0 de Discovery (Los 6 Pilares)
De acuerdo con la invariante `discovery_mandate_enforcement` de [harnesses/discovery.md](harnesses/discovery.md), **es obligatorio ejecutar una auditoría de descubrimiento previa antes de realizar cualquier cambio** en una base de código preexistente:

1. **Paso 1: Topografía del Repositorio**: Identifica si es monorepo (`pnpm-workspace.yaml`, `lerna.json`) o polyrepo, y ubica las fuentes de verdad autoritativas.
2. **Paso 2: Grafo de Dependencias y Runtime**: Detecta el gestor de paquetes oficial (`pnpm`, `npm`, `yarn` o `cargo`). *Regla innegociable*: Respeta estrictamente el lockfile detectado; no mezcles gestores.
3. **Paso 3: Inventario de Verificación y Línea Base**:
   - Corre la suite de pruebas existente antes de modificar código (`pnpm test` o comando nativo).
   - Registra cualquier fallo preexistente en el informe de auditoría (`baseline_audit_enforcement`).
   - Si no existen tests automatizados, activa el fallback manual y checklist con evidencia visual (`verification_fallback_enforcement`).
4. **Paso 4: Convenciones de Código y Patrones**: Inspecciona patrones (Clean Architecture, Feature-Sliced Design), tokens de diseño y convenciones de tipado estricto (cero `any`).
5. **Paso 5: Invariantes y Fronteras de Seguridad**: Localiza mecanismos de autenticación, transacciones de base de datos y boundaries sensibles.
6. **Paso 6: Deuda Técnica y Fragilidades**: Mapea marcadores `TODO`, `FIXME` o warnings conocidos.

El Discovery Specialist documenta sus hallazgos en `docs/discovery/ARCH_AUDIT.md` e inicializa [Engram.md](Engram.md).

### Paso 3: Atender un Hotfix en Producción (Vía Rápida Tier 1)
Para corregir un bug aislado urgente en producción:

1. Copia la micro-spec:
   ```bash
   cp specs/_template_hotfix.md specs/hotfix-error-pago.md
   ```
2. Completa las 5 secciones (máximo 25 líneas):
   - Problema detectado.
   - Archivos afectados (máximo 3).
   - Solución propuesta.
   - Comando de verificación (`pnpm test tests/payment.test.ts`).
   - Checklist de cierre rápido.
3. El Worker implementa la corrección en un único turno.
4. Ejecuta el test de validación.
5. Firma el checklist y actualiza [Engram.md](Engram.md).

> Para ver un walkthrough detallado paso a paso de este flujo, consulta [docs/examples/example_hotfix.md](docs/examples/example_hotfix.md).

---

## 7. Configuración de Entornos y Variables de Entorno

Asegura que tu entorno disponga de las siguientes herramientas y variables de configuración según la plataforma utilizada:

| Plataforma / Adaptador | Prerrequisitos de Software | Variables de Entorno Requeridas | Guía Detallada |
|---|---|---|---|
| **Google Antigravity** | Node.js 20+, pnpm 9+, navegador Chrome / Chromium | N/A (Gestionado por el runtime de Antigravity) | [docs/adapters/antigravity.md](docs/adapters/antigravity.md) |
| **Anthropic Claude Code** | Node.js 18+, npm | `export ANTHROPIC_API_KEY="sk-ant-api..."` | [docs/adapters/claude_code.md](docs/adapters/claude_code.md) |
| **OpenCode / Ollama** | Ollama CLI, RAM >= 16 GB, GPU recomendada | `export OLLAMA_HOST="http://localhost:11434"`<br>`export OPENCODE_MODEL="qwen2.5-coder:14b"` | [docs/adapters/opencode.md](docs/adapters/opencode.md) |

### Optimización de Prompt Prefix Caching
En plataformas con soporte de caché de contexto (Anthropic Claude Code y Google Antigravity), las instrucciones estáticas de gobernanza ([AGENTS.md](AGENTS.md) y [CLAUDE.md](CLAUDE.md)) se sitúan al inicio del prompt del sistema. Esto garantiza una tasa de acierto de caché (**Cache Hit Rate**) de entre el 70% y el 90%, reduciendo drásticamente los costes y la latencia operativa.

---

## 8. Catálogo Canónico de Enlaces y Documentación

Todos los componentes del sistema cuentan con especificación y documentación formal accesible mediante enlaces relativos directos:

### Gobernanza Central
- [AGENTS.md](AGENTS.md) — Directrices maestras de gobernanza, filosofía Ponytail y ciclo SDD.
- [CLAUDE.md](CLAUDE.md) — Adaptador y directrices de optimización para Claude Code CLI.
- [OPENCODE.md](OPENCODE.md) — Adaptador para modelos locales y límites de contexto acotado.
- [Engram.md](Engram.md) — Memoria viva persistente del proyecto (<150 líneas).

### Arneses de Proceso (`harnesses/`)
- [harnesses/multi-agent.md](harnesses/multi-agent.md) — Taxonomía de 7 roles, matriz de 3 tiers de modelos y regla 80/20.
- [harnesses/isolation.md](harnesses/isolation.md) — Aislamiento de agentes, fronteras de carpetas y disyuntor de 2 intentos.
- [harnesses/discovery.md](harnesses/discovery.md) — Protocolo de auditoría previa en 6 pilares (Fase 0: Discovery).
- [harnesses/closing.md](harnesses/closing.md) — Protocolo de cierre post-implementación, sign-off PVS y changelog (Fase N).
- [harnesses/sizing.md](harnesses/sizing.md) — Matriz determinista de dimensionamiento y enrutamiento adaptativo.
- [harnesses/odd.md](harnesses/odd.md) — Señales operativas, diagnóstico y privacidad cuando ODD aplica.
- [harnesses/rdd.md](harnesses/rdd.md) — Recibos nativos y revisión del candidato exacto.
- [harnesses/phase.md](harnesses/phase.md) — Enrutamiento agnóstico de fases SDD y compuertas de transición.
- [harnesses/ai-rules.md](harnesses/ai-rules.md) — Reglas operativas para agentes y excepciones micro-spec.
- [harnesses/engram.md](harnesses/engram.md) — Reglas de gestión de memoria técnica y protocolo de poda.
- [harnesses/review.md](harnesses/review.md) — Estándares de Pull Request, límite de 300 LOC y checklist de revisión.
- [harnesses/contract.md](harnesses/contract.md) — Contratos de interfaz, invariantes y delimitación de módulos.
- [harnesses/structure.md](harnesses/structure.md) — Convenciones estructurales de carpetas y código.
- [harnesses/state.md](harnesses/state.md) — Seguimiento de estado de tareas y ciclo de vida.

### Plantillas de Especificación (`specs/`)
- [specs/004-odd-harness.md](specs/004-odd-harness.md) — Spec del arnés ODD.
- [specs/_template_hotfix.md](specs/_template_hotfix.md) — Micro-spec ultraligera para cambios pequeños o hotfixes (≤25 líneas).
- [specs/_template.md](specs/_template.md) — Plantilla estándar para features medianas (Tier 2).
- [specs/_template_epic.md](specs/_template_epic.md) — Plantilla épica con desglose en slices y gate adversarial (Tier 3).

### Adaptadores y Ejemplos (`docs/`)
- [docs/adapters/antigravity.md](docs/adapters/antigravity.md) — Adaptador para Google Antigravity.
- [docs/adapters/claude_code.md](docs/adapters/claude_code.md) — Adaptador para Anthropic Claude Code.
- [docs/adapters/opencode.md](docs/adapters/opencode.md) — Adaptador para OpenCode y modelos locales (Ollama).
- [docs/prompts/orchestrator.md](docs/prompts/orchestrator.md) — Prompt del sistema para el rol Orchestrator.
- [docs/prompts/explorer.md](docs/prompts/explorer.md) — Prompt del sistema para el rol Explorer.
- [docs/prompts/worker.md](docs/prompts/worker.md) — Prompt del sistema para el rol Worker.
- [docs/prompts/reviewer.md](docs/prompts/reviewer.md) — Prompt del sistema para el rol Reviewer.
- [docs/prompts/challenger.md](docs/prompts/challenger.md) — Prompt del sistema para el rol Challenger.
- [docs/prompts/auditor.md](docs/prompts/auditor.md) — Prompt del sistema para el rol Forensic Auditor.
- [docs/examples/example_hotfix.md](docs/examples/example_hotfix.md) — Walkthrough: Corrección de un hotfix en producción.
- [docs/examples/example_greenfield.md](docs/examples/example_greenfield.md) — Walkthrough: Construcción de una feature en proyecto nuevo.
- [docs/ADR_template.md](docs/ADR_template.md) — Plantilla para Architectural Decision Records.

---

## 9. Suite de Verificación Automatizada (E2E Test Runner)

El framework incluye una suite de pruebas automatizadas independiente ([tests/e2e_test_runner.py](tests/e2e_test_runner.py)) construida con la biblioteca estándar de Python (cero dependencias externas), que valida la integridad estructural, enlaces cruzados y reglas de gobernanza a través de cuatro tiers:

```bash
python tests/e2e_test_runner.py
```

### Niveles de Comprobación
- **Tier 1 (Feature Coverage)**: 80 casos de prueba verificando la presencia y contenido de las 16 features de `PROJECT.md`.
- **Tier 2 (Boundary & Corner Cases)**: 80 casos de prueba validando límites dimensionales (≤25 líneas en micro-specs, ≤80 LOC en Tier 1, ≤300 LOC en PRs, 80-150 líneas en Engram, veto binario y disyuntor de 2 intentos).
- **Tier 3 (Combinations & Cross-Role)**: Pruebas de interacción coordinada entre roles y matrices de modelos.
- **Tier 4 (Real-World Scenarios)**: Escenarios reales de punta a punta (hotfix en producción y feature greenfield).

### Checklist Pre-Commit / Pre-Push
Antes de realizar cualquier commit o push a la rama principal:
- [ ] Ejecutar `python tests/e2e_test_runner.py` y verificar 100% PASS en todos los tiers activos.
- [ ] Verificar que no existan enlaces relativos rotos en ningún archivo `.md`.
- [ ] Comprobar que todos los bloques de código (```) estén debidamente cerrados.
- [ ] Asegurar que [Engram.md](Engram.md) esté actualizado y dentro del rango de 80 a 150 líneas.
- [ ] Confirmar que no queden marcadores provisionales ni archivos huérfanos sin registrar.

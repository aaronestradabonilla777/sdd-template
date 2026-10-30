# Sizing & Adaptive SDD Harness (Deterministic Sizing Matrix)

Este harness define la matriz autoritativa, objetiva y determinista de **Sizing y Enrutamiento Adaptativo** dentro del ciclo de vida SDD (Software-Design-Driven) de `sdd-template`. Su propósito fundamental es resolver la paradoja de fricción burocrática y optimizar el consumo de tokens de contexto, modulando el nivel de rigor formal, los artefactos de especificación requeridos y las compuertas de control en función de la magnitud y criticidad intrínseca de cada cambio de software.

Opera de forma agnóstica entre plataformas de ejecución ([Google Antigravity](../docs/adapters/antigravity.md), [Anthropic Claude Code](../docs/adapters/claude_code.md), [OpenCode / Ollama](../docs/adapters/opencode.md)) y se fundamenta en las directrices maestras de [AGENTS.md](../AGENTS.md), la taxonomía de roles de [multi-agent.md](multi-agent.md), los protocolos de aislamiento de [isolation.md](isolation.md), la auditoría previa de [discovery.md](discovery.md) y el cierre formal de [closing.md](closing.md).

---

## 1. Principios Rectores y Reglas de Invarianza

El dimensionamiento de tareas en `sdd-template` se rige por cinco principios innegociables:

1. **Determinismo Cuantitativo**: La clasificación no se basa en intuiciones subjetivas, sino en métricas objetivas: conteo de archivos afectados, volumen neto de líneas de código (LOC), impacto sobre esquemas de persistencia / contratos públicos y nivel de riesgo funcional.
2. **Principio Ponytail / YAGNI Estricto**: El nivel de documentación y pasos intermedios no debe exceder lo estrictamente necesario para garantizar la integridad del sistema. Para cambios pequeños o hotfixes, la vía rápida elimina la sobrecarga de diseño formal sin relajar las pruebas unitarias.
3. **Invariante de Escalación Automática (`sizing_escalation_enforcement`)**: Un cambio catalogado inicialmente como Tier 1 (Fast-Track) que durante la implementación supere los umbrales autorizados (≥4 archivos o >80 líneas de código o alteraciones de esquema/API) **debe escalar de forma inmediata e imperativa a Tier 2**, deteniendo la ejecución hasta aprobar la especificación correspondiente.
4. **Invariante del Umbral Dominante (`dominant_threshold_enforcement`)**: Ante discrepancias entre métricas (por ejemplo, pocas líneas modificadas distribuidas en gran cantidad de archivos), la métrica de mayor impacto o mayor restricción siempre domina la clasificación. Cualquier tarea que abarque **más de 10 archivos** o afecte la arquitectura central clasifica incondicionalmente como **Tier 3 Epic**, independientemente de que contenga pocas líneas de código.
5. **Requisito Estructural de Presencia (`file_presence_enforcement`)**: El archivo `harnesses/sizing.md` es un componente estructural mandatorio del repositorio; su ausencia o alteración no autorizada invalida el sistema de gobernanza SDD.

---

## 2. Matriz de Decisión Rápida (Quick Sizing Questionnaire)

Antes de redactar una especificación técnica o despachar a un Worker, el **Orchestrator (Tier 1)** o el desarrollador debe responder a las siguientes 4 preguntas deterministas de tipo binario (SÍ / NO):

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 MATRIZ DE DECISIÓN RÁPIDA (4 PREGUNTAS)                     │
├─────────────────────────────────────────────────────────────────────────────┤
│ 1. ¿Afecta a más de 3 archivos?                                  [SÍ / NO]  │
│ 2. ¿Modifica esquemas de base de datos o contratos de APIs?      [SÍ / NO]  │
│ 3. ¿Afecta lógica de negocio central, autenticación o seguridad? [SÍ / NO]  │
│ 4. ¿Se estima que supere las 80 líneas de código netas?          [SÍ / NO]  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Reglas de Clasificación Inmediata:

- **SI RESPONDIÓ "NO" A TODAS LAS PREGUNTAS**:
  ➔ **Tier 1: Small / Hotfix (Vía Rápida / Fast-Track)**.
  Aplica micro-spec (`specs/_template_hotfix.md`), exime de fase formal de diseño por capas y permite implementación directa con verificación focalizada.

- **SI RESPONDIÓ "SÍ" A 1 O 2 PREGUNTAS** (sin tocar >10 archivos ni comprometer módulos críticos de seguridad/auth):
  ➔ **Tier 2: Medium (Flujo Estándar / Standard SDD)**.
  Aplica especificación estándar (`specs/_template.md`), ciclo completo de 4 fases (SPEC → DISEÑO → IMPL → VERIF) y revisión de PR (≤300 LOC).

- **SI RESPONDIÓ "SÍ" A 3 O MÁS PREGUNTAS**, o el cambio abarca **más de 10 archivos**, o altera contratos breaking en APIs/migraciones complejas:
  ➔ **Tier 3: Large / Epic (Flujo de Alta Garantía / Epic SDD)**.
  Aplica especificación épica (`specs/_template_epic.md`), auditoría 6-Pillar en Phase 0 Discovery, descomposición mandatoria en slices (<300 LOC) y Gate de Revisión Adversaria con Veto Binario.

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
│  TIER 1: HOTFIX  │      │ TIER 2: ESTÁNDAR │      │  TIER 3: EPIC    │
│   (Fast-Track)   │      │  (Standard SDD)  │      │  (High-Assurance)│
├──────────────────┤      ├──────────────────┤      ├──────────────────┤
│ • 1 a 3 archivos │      │ • 4 a 10 archivos│      │ • > 10 archivos  │
│ • ≤ 80 líneas    │      │ • 80 a 300 líneas│      │ • > 300 líneas   │
│ • Cero DB schema │      │ • Schema aditivo │      │ • Breaking/Core  │
│ • Micro-spec     │      │ • Spec estándar  │      │ • Slices en spec │
│   (< 25 líneas)  │      │   completa       │      │   épica + Veto   │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

---

## 3. Matriz de Clasificación Detallada

La siguiente tabla sintetiza los límites dimensionales, requerimientos de gobernanza y asignación de recursos para cada nivel de sizing:

| Criterio / Dimensión | Tier 1: Small / Hotfix (Micro-SDD) | Tier 2: Medium (Standard SDD) | Tier 3: Large / Epic (High-Assurance SDD) |
|---|---|---|---|
| **Límite de Archivos** | **1 a 3 archivos** modificados | **4 a 10 archivos** modificados | **> 10 archivos** o múltiples subsistemas |
| **Líneas de Código (LOC)** | **≤ 80 líneas** netas de cambio | **80 a 300 líneas** netas de cambio | **> 300 líneas** netas de cambio (desglosado) |
| **Nivel de Riesgo** | Mínimo / Localizado | Moderado / Acotado a subsistema | Crítico / Alto (auth, pagos, core business) |
| **Impacto de Esquema** | **Cero cambios** en DB / schemas / APIs | Modificaciones aditivas no destructivas | Migraciones complejas, breaking changes |
| **Fronteras Modulares** | Confinado a 1 solo componente/función | Afecta 1 o 2 módulos coordinados | Modifica abstracciones compartidas / core |
| **Casos Típicos** | Typos, hotfixes de CSS/UI, bugfixes aislados, bump de deps menores, nuevo test unitario | Nueva vista o página, nuevo endpoint CRUD, extensión de servicio, refactor local | Nuevo subsistema arquitectónico, migración de framework/DB, integración multi-servicio |
| **Flujo SDD Activado** | **Vía Rápida (Fast-Track)** | **Flujo Estándar SDD** | **Flujo Formal con Gates Adversarios (Epic)** |
| **Plantilla de Spec** | `specs/_template_hotfix.md` (≤ 25 líneas) | `specs/_template.md` (estándar completa) | `specs/_template_epic.md` (desglose en slices) |
| **Asignación de Modelos** | Worker en Tier 2 (Fast) o Tier 3 (Local) | Tier 1 (Pro) para Spec; Tier 2 para Impl | Tier 1 (Pro) para Orchestrator/Auditor; Tier 2 Worker |
| **Fases SDD Obligatorias**| Phase 0 Light ➔ Micro-Spec ➔ Impl ➔ Cierre Rápido | Phase 0 ➔ SPEC ➔ DISEÑO ➔ IMPL ➔ VERIF ➔ Cierre | Phase 0 Full ➔ Epic Spec ➔ Adversarial Gate ➔ Slices ➔ Cierre |
| **Presupuesto de Tokens** | < 2,000 tokens de especificación (~80% ahorro) | 5,000 – 12,000 tokens de ciclo completo | 20,000 – 50,000+ tokens (gobernanza por hitos) |

---

## 4. Especificación Detallada de Flujos por Tier

### 4.1 Tier 1: Vía Rápida (Fast-Track Micro-SDD)

La **Vía Rápida (Fast-Track)** está diseñada para maximizar el rendimiento del desarrollador senior ("lazy senior dev" en Ponytail) y minimizar el consumo innecesario de tokens en tareas mecánicas o correctivas de bajo impacto.

#### Características Operativas:
- **Límites Estrictos**:
  * Exactamente de **1 a 3 archivos** involucrados.
  * Máximo **80 líneas** netas de código añadidas o modificadas.
  * Prohibición total de mutaciones en esquemas de base de datos o contratos públicos de API.
- **Artefacto de Especificación**:
  * Utiliza exclusivamente la micro-spec de `specs/_template_hotfix.md`.
  * La extensión máxima de la plantilla es de **25 líneas de Markdown** (`line_count_le: 25`).
  * Estructura condensada en 5 secciones mínimas:
    1. *Problema Detectado* (1-2 líneas).
    2. *Archivos Afectados* (máximo 3 rutas explícitas).
    3. *Solución Propuesta* (enfoque conciso, sin diagramas pesados).
    4. *Verificación* (comando ejecutable: `pnpm test <archivo>` o equivalente).
    5. *Checklist de Cierre Rápido* (compilación limpia, <80 líneas netas).
  * **Exclusiones Mandatorias**: Queda terminantemente prohibido incluir diagramas de esquemas de datos, tablas exhaustivas de endpoints REST vacías o especificaciones Gherkin prolijas (`does_not_contain_patterns`).
- **Ciclo de Ejecución**:
  ```
  [Phase 0: Light Discovery] ──► [Micro-Spec (<25 lín)] ──► [Implementación Worker] ──► [Test Unitario] ──► [Cierre Rápido]
  ```
- **Asignación y Roles**: Puede ser ejecutado íntegramente por un único Worker utilizando modelos económicos (**Tier 2: Fast** o **Tier 3: Local SLM**), reservando la firma del checklist para el Reviewer o el propio desarrollador.

---

### 4.2 Tier 2: Flujo Estándar (Standard SDD)

El **Flujo Estándar SDD** constituye el núcleo de desarrollo de features medianas, garantizando equilibrio entre diseño arquitectónico, cobertura de edge cases y velocidad de entrega.

#### Características Operativas:
- **Límites Dimensionales**:
  * Entre **4 a 10 archivos** modificados.
  * Entre **80 a 300 líneas** netas de código.
  * Se permiten modificaciones aditivas en esquemas de bases de datos (ej. agregar columna nullable o tabla nueva) siempre que no rompan contratos existentes.
- **Artefacto de Especificación**:
  * Utiliza la plantilla estándar `specs/_template.md`.
  * Requiere delimitación explícita de componentes frontend, servicios backend, contratos de tipos y criterios de aceptación (Given/When/Then).
- **Ciclo de Ejecución de 4 Fases**:
  ```
  Phase 0: Standard Discovery
         │
         ▼
  Phase 1: SPEC (Qué se construye, criterios de aceptación, edge cases)
         │
         ▼
  Phase 2: DISEÑO (Contratos de interfaz, esquema de datos, componentes)
         │
         ▼
  Phase 3: IMPLEMENTACIÓN (Worker acotado al Result Contract)
         │
         ▼
  Phase 4: VERIFICACIÓN (Suite completa, revisión de PR < 300 LOC)
         │
         ▼
  Phase N: CLOSING (PVS Evidence Matrix, Engram.md sync, Changelog)
  ```
- **Asignación de Roles**:
  * **Orchestrator** (Tier 1 Pro): Redacta o aprueba SPEC y DISEÑO.
  * **Worker** (Tier 2 Fast o Tier 3 Local): Implementa el código según el Result Contract.
  * **Reviewer** (Tier 2 Fast): Valida cumplimiento de spec, ausencia de `any` y límite de PR (<300 LOC).

---

### 4.3 Tier 3: Flujo de Alta Garantía (Epic SDD)

El **Flujo de Alta Garantía (Epic SDD)** se activa ante cambios estructurales mayores, migraciones de base de datos o subsistemas con alto impacto en el negocio, donde un fallo en producción acarrearía pérdidas críticas.

#### Características Operativas:
- **Límites Dimensionales**:
  * **Más de 10 archivos** modificados o **más de 300 líneas** netas de código.
  * Modificaciones destructivas de esquema, migraciones complejas o refactors que atraviesan fronteras de dominio.
- **Artefacto de Especificación**:
  * Utiliza la plantilla épica `specs/_template_epic.md`.
  * **Desglose Obligatorio en Slices**: El Epic no puede implementarse en un PR masivo monolítico. Debe descomponerse en una tabla de hitos (Milestones / Slices) independientes, donde **cada slice debe ser estrictamente menor a 300 líneas de código** (`slice_size_boundary`).
- **Auditoría Previa y Puertas de Seguridad**:
  * **Phase 0 Discovery Obligatoria**: Auditoría integral de los 6 pilares conforme a [discovery.md](discovery.md), produciendo `docs/discovery/ARCH_AUDIT.md`.
  * **Gate de Revisión Adversaria**: Antes de permitir que un Worker escriba código de producción, el **Challenger (Tier 1)** y el **Forensic Auditor (Tier 1)** deben emitir su dictamen de aprobación formal sobre el diseño. El Auditor posee **Veto Binario** absoluto (`binary_veto_enforcement`).
  * **Estrategia de Rollback**: Sección obligatoria en la spec que describe el plan de reversión paso a paso ante contingencias catastróficas.
- **Ciclo de Ejecución**:
  ```
  Phase 0: Full 6-Pillar Discovery (ARCH_AUDIT.md)
         │
         ▼
  Epic Decomposition en Specs/_template_epic.md (Slices < 300 LOC)
         │
         ▼
  Gate de Revisión Adversaria (Veto Binario de Challenger / Forensic Auditor)
         │
         ▼
  Ejecución Faseada por Milestones (Slice 1 ──► Slice 2 ──► Slice N)
         │
         ▼
  Verificación E2E Integral + Auditoría Forense de Tests
         │
         ▼
  Phase N Closing Global (Consolidación Engram <150 lín + ADR + Changelog)
  ```

---

## 5. Escalación Automática y Disyuntor (Dynamic Escalation Circuit Breaker)

Durante el ciclo de desarrollo, la complejidad real puede superar las estimaciones preliminares. Para preservar la integridad del software y evitar desviaciones no controladas, se aplican las reglas de escalación dinámica.

### 5.1 Disparadores de Escalación de Tier 1 a Tier 2 (`sizing_escalation_enforcement`)

Si un agente Worker o desarrollador se encuentra ejecutando bajo la modalidad Tier 1 Fast-Track y detecta cualquiera de las siguientes 4 condiciones:

1. **Desbordamiento de Archivos**: La resolución requiere modificar **4 o más archivos** (TC-T2-F09-01).
2. **Desbordamiento de Líneas**: El volumen de código nuevo o modificado supera las **80 líneas netas** (TC-T2-F09-02).
3. **Mutación de Esquema o Contrato**: Se detecta que el fix requiere alterar una tabla de base de datos, agregar migraciones, modificar contratos de endpoints públicos o alterar tipos compartidos en `shared/` (TC-T2-F09-03).
4. **Disyuntor de Intentos Tripped**: Tras 2 intentos fallidos de implementación en el Worker (conforme al Circuit Breaker de [isolation.md](isolation.md)), el problema evidencia una causa raíz que excede un hotfix simple.

**Protocolo de Escalación Inmediata**:
1. **Detención Inmediata (HALT)**: El Worker detiene la escritura de código en `src/`. Prohibido intentar "forzar" el fix bajo la micro-spec.
2. **Empaquetado de Estado**: El agente genera un snapshot de diagnóstico o stash de los cambios en su directorio local (`.agents/teamwork/`).
3. **Escalación al Orchestrator**: Se notifica al Orchestrator mediante mensaje conciso (<300 palabras) señalando el umbral superado.
4. **Transición a Tier 2**: El Orchestrator promueve la tarea a Tier 2, archiva la micro-spec y despacha la creación de una especificación estándar en `specs/` antes de reanudar la implementación.

### 5.2 Disparadores de Escalación de Tier 2 a Tier 3

Un cambio Tier 2 escala automáticamente a Tier 3 si:
- El número de archivos asciende a **más de 10 archivos**.
- El diff total proyectado supera las **300 líneas de código** sin posibilidad de resolverse en un solo PR cohesivo.
- Se identifican dependencias cruzadas que impactan la arquitectura central, autenticación o integridad transaccional de múltiples dominios.
- Se requiere descomponer el trabajo en milestones sucesivos.

---

## 6. Casos Límite, Ambigüedad y Reglas de Resolución

Para asegurar que la matriz sea 100% determinista y no existan zonas grises, se establecen las siguientes reglas de resolución ante casos límite:

### 6.1 Principio del Umbral Dominante (`dominant_threshold_enforcement`)
- **Regla**: Cuando dos métricas apuntan a categorías divergentes, la métrica con el requerimiento de mayor rigor técnico prevalece de forma estricta.
- **Caso Práctico (TC-T2-F09-04)**: Una tarea abarca 12 archivos para ajustar una constante de configuración distribuida, totalizando únicamente 150 líneas de código. Aunque 150 LOC pertenece al rango de Tier 2, **el hecho de tocar 12 archivos (>10 archivos) domina la clasificación**, obligando a tratar la tarea como **Tier 3 Epic**.

### 6.2 Prohibición Absoluta de Esquemas en Hotfixes (`sizing_escalation_enforcement`)
- **Regla**: Ningún cambio que altere esquemas de datos o interfaces públicas puede catalogarse como Tier 1, sin importar cuán pocas líneas requiera.
- **Caso Práctico (TC-T2-F09-03)**: Añadir una columna `active BOOLEAN DEFAULT true` a una tabla SQL involucra apenas 1 línea de código en 1 solo archivo. Sin embargo, al tratarse de una mutación de esquema de persistencia, **está terminantemente prohibido tramitarla bajo Tier 1 Fast-Track**; debe tramitarse como Tier 2 para garantizar diseño de rollback y verificación de compatibilidad.

### 6.3 Dispersión de Refactor sin Cambio Lógico
- Si un cambio consiste en renombrar una función exportada o interfaz compartida que afecta 20 archivos:
  * No califica como Tier 1 (por exceder 3 archivos).
  * No califica como Tier 2 (por exceder 10 archivos).
  * **Clasifica como Tier 3 Epic**: La dispersión del blast radius impone el uso de sub-specs y verificación adversarial para prevenir fallos en tiempo de ejecución.

### 6.4 Principio de Conservadurismo ante Dudas de Estimación
- Si la estimación de líneas de código oscila en el umbral fronterizo (ejemplo: 75–85 LOC estimadas), el sistema adoptará preventivamente el **Tier superior inmediato** (Tier 2), garantizando que no se violen las salvaguardas de revisión de código.

---

## 7. Mapeo de Roles y Asignación de Modelos por Tier (Regla 80/20)

En estricta concordancia con la política de eficiencia de contexto de [multi-agent.md](multi-agent.md) y [AGENTS.md](../AGENTS.md), la matriz de Sizing gobierna la distribución de modelos y tokens:

| Tier de Sizing | Rol de Orquestación / Arquitectura | Rol de Implementación (Worker) | Rol de Revisión y Verificación | Presupuesto Token Típico | Ahorro Relativo |
|---|---|---|---|---|---|
| **Tier 1 (Hotfix)** | N/A (Auto-orquestado o despacho directo) | **Tier 2 (Fast)** o **Tier 3 (Local SLM)** | Reviewer (Tier 2) o Sanity Check local | < 2,000 tokens | **~80% ahorro** |
| **Tier 2 (Estándar)** | **Tier 1 (Pro / Frontier)** | **Tier 2 (Fast / Economical)** | Reviewer (Tier 2 Fast) | 5,000 – 15,000 tokens | **~50% ahorro** |
| **Tier 3 (Epic)** | **Tier 1 (Pro / Frontier)** | Workers múltiples en **Tier 2 (Fast)** | Challenger y Forensic Auditor (**Tier 1 Pro**) | 25,000 – 60,000+ tokens | Gobernanza estricta |

- **Regla 80/20 en Ejecución**: Al menos el **80% del volumen de tokens consumidos en el repositorio debe canalizarse a través de modelos Tier 2 (Fast) y Tier 3 (Local)**. Los modelos Tier 1 (Pro) se reservan exclusivamente para la síntesis de diseño, decisiones arquitectónicas complejas y auditoría adversaria con veto.

---

## 8. Matriz Resumen de Reglas de Invarianza Normativa

La siguiente tabla formaliza las reglas normativas comprobadas por los suites de prueba automatizados ([tests/e2e_test_runner.py](../tests/e2e_test_runner.py)) y sus comportamientos ante desbordamientos:

| Identificador de Regla | Ámbito de Aplicación | Condición de Desbordamiento / Disparo | Comportamiento del Gate y Acción Requerida |
|---|---|---|---|
| `sizing_escalation_enforcement` | Tier 1 (Fast-Track) | Tarea modifica ≥4 archivos, agrega >80 LOC o altera esquemas/APIs | **Escalación Automática Obligatoria**: Detención inmediata del Worker; promoción forzosa a Tier 2 y redacción de spec formal. |
| `dominant_threshold_enforcement` | Clasificación de Tier | Tarea abarca >10 archivos o toca arquitectura core independientemente de LOC | **Dominancia de Métrica Crítica**: Clasificación incondicional en Tier 3 Epic; exige descomposición en sub-specs. |
| `file_presence_enforcement` | Arquitectura SDD | Archivo `harnesses/sizing.md` ausente o no resoluble en el repositorio | **Fallo Estructural Inmediato**: Bloquea la suite de verificación e impide cualquier despacho de agentes. |
| `pr_size_enforcement` | Tier 2 y Slices de Tier 3 | Diff de Pull Request o slice supera las 300 líneas de código netas | **Rechazo de PR**: El Reviewer veta la entrega exigiendo partición en sub-slices atómicos menores a 300 LOC. |
| `line_bound_enforcement` | Tier 1 Micro-Spec | `specs/_template_hotfix.md` excede el límite de 25 líneas de Markdown | **Violación por Sobrecarga**: Rechazo de spec; exige compactar el documento a ≤25 líneas. |

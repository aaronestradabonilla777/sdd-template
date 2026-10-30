# Post-Change Closing Protocol Harness (Phase N: Closing)

Este harness define el protocolo estandarizado, obligatorio y autoritativo de **Fase N (Closing / Cierre Post-Implementación)** dentro del ciclo de vida SDD (Software-Design-Driven). Establece las compuertas de salida indispensables antes de cualquier entrega, commit o merge a ramas protegidas en proyectos nuevos o en producción, garantizando la preservación de la memoria técnica del repositorio y la integridad del software.

Opera de manera agnóstica respecto a la plataforma de ejecución ([Google Antigravity](../docs/adapters/antigravity.md), [Anthropic Claude Code](../docs/adapters/claude_code.md) o entornos locales [OpenCode / Ollama](../docs/adapters/opencode.md)) y se fundamenta en las directrices maestras de [AGENTS.md](../AGENTS.md) y la arquitectura de roles de [multi-agent.md](multi-agent.md).

---

## 1. Principios y Compuertas de la Fase N

La Fase N constituye la última línea de defensa antes del despliegue y la entrega formal. Sus principios rectores son:

1. **Invariante de Cero Fallos en Verificación (`pvs_verification_enforcement`)**: Ningún cambio puede cerrar su ciclo si existe un solo test fallido, un warning no suprimido o un Criterio de Aceptación sin evidencia empírica verificada.
2. **Sincronización de Memoria Innegociable (`engram_sync_enforcement`)**: Todo cambio concluido debe actualizar la memoria viva del repositorio ([Engram.md](../Engram.md)), registrando las decisiones arquitectónicas tomadas y el estado operativo de los módulos.
3. **Cierre de Ciclo de Especificación (`spec_status_sync_enforcement`)**: La especificación técnica en `specs/` debe transicionar formalmente al estado `COMPLETADA`, documentando fecha y agente verificador. Queda terminantemente prohibido cerrar tareas con especificaciones en `DRAFT` o `IN PROGRESS`.
4. **Estandarización Canónica de Changelog (`changelog_format_enforcement`)**: La trazabilidad histórica se documenta exclusivamente bajo las categorías estándar de *Keep a Changelog*, prohibiendo encabezados arbitrarios o ambiguos.
5. **Presupuesto y Eficiencia de Tokens**: La fase de cierre aplica la regla 80/20, ejecutando verificaciones mecánicas en modelos Tier 2 / Tier 3 y reservando la firma del gate de salida para el Orchestrator en Tier 1.

---

## 2. Flujo de las 4 Etapas del Protocolo de Cierre

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       FASE N: PROTOCOLO DE CIERRE                           │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
                                       ▼
 ┌───────────────────────────────────────────────────────────────────────────┐
 │ ETAPA 1: Post-Implementation Verification Sign-Off (PVS)                  │
 │ - Ejecución de Clean Build y Suite Completa de Tests (100% PASS)         │
 │ - Construcción de la Matriz de Evidencia de Criterios de Aceptación       │
 │ - Certificado de Integridad Forense (cero tests debilitados o mockeados)   │
 └─────────────────────────────────────┬─────────────────────────────────────┘
                                       │
                                       ▼
 ┌───────────────────────────────────────────────────────────────────────────┐
 │ ETAPA 2: Sincronización de Memoria de Proyecto (Engram.md)                │
 │ - Registro sintético de Decisiones Técnicas Tomadas (micro-ADR)          │
 │ - Actualización de estado en ## 4. Estado de Módulos                      │
 │ - Aplicación de límite presupuestario (80 - 150 líneas) y poda (pruning)   │
 └─────────────────────────────────────┬─────────────────────────────────────┘
                                       │
                                       ▼
 ┌───────────────────────────────────────────────────────────────────────────┐
 │ ETAPA 3: Sincronización de Especificaciones y Documentación               │
 │ - Actualización de cabecera de spec a COMPLETADA con Fecha de cierre      │
 │ - Identificación del agente verificador y commit/hash de referencia       │
 │ - Sincronización de documentación adyacente (docs/, README.md)             │
 └─────────────────────────────────────┬─────────────────────────────────────┘
                                       │
                                       ▼
 ┌───────────────────────────────────────────────────────────────────────────┐
 │ ETAPA 4: Generación de Registro de Cambios (Keep a Changelog)             │
 │ - Generación de entrada en CHANGELOG.md bajo categorías canónicas:        │
 │   Added, Changed, Deprecated, Removed, Fixed, Security                    │
 │ - Formato de commit convencional estandarizado                            │
 └─────────────────────────────────────┬─────────────────────────────────────┘
                                       │
                                       ▼
 ┌───────────────────────────────────────────────────────────────────────────┐
 │ CHECKLIST DE SALIDA Y GATE DE APROBACIÓN (Orchestrator Tier 1)            │
 │ - Verificación de los 9 puntos de control y firma definitiva de entrega   │
 └───────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Especificación Detallada por Etapa

### Etapa 1: Post-Implementation Verification Sign-Off (PVS)

La **Etapa 1** formaliza la validación empírica irrefutable de que el código implementado cumple con exactitud matemática los requerimientos establecidos en la especificación técnica.

#### 1. Mandato de Clean Build y 100% de Tests Aprobados (`pvs_verification_enforcement`)
- Antes de firmar el PVS, el Worker debe ejecutar una compilación en frío (`clean build`) sin dependencias residuales.
- La suite completa de tests automatizados (unitarios, de integración, de tipos y E2E) debe pasar con código de salida 0.
- **Veto de Test Fallido**: Si un solo test unitario o de integración falla, la tarea es **rechazada de inmediato**. Prohibido marcar entregas con fallos parciales o pruebas omitidas (`skip`/`ignore`) no autorizadas.

#### 2. Matriz de Evidencia PVS (PVS Evidence Matrix)
El Worker o Revisor debe construir y adjuntar la tabla de verificación que correlaciona cada uno de los Criterios de Aceptación (AC) definidos en la spec con su comando de prueba y resultado textual verbatim:

| ID Criterio | Descripción del Criterio de Aceptación | Comando de Verificación Ejecutado | Evidencia Verbatim / Log | Estado | Agente Verificador (Rol / Tier) |
|---|---|---|---|---|---|
| AC-01 | Compilación estática limpia sin errores de tipos | `pnpm build` | `dist/ generated in 1.4s (exit code 0)` | PASS | Worker (Tier 2) |
| AC-02 | Cobertura de tests unitarios de la feature | `pnpm test tests/feature.test.ts` | `Tests: 14 passed, 14 total` | PASS | Worker (Tier 2) |
| AC-03 | Respeto a límites de tamaño de diff (PR <300 LOC) | `git diff --stat origin/main` | `3 files changed, 142 insertions(+), 18 deletions(-)` | PASS | Reviewer (Tier 2) |
| AC-04 | Ausencia de tipos 'any' y cumplimiento tipado | `pnpm typecheck` | `Found 0 errors in 42 files` | PASS | Reviewer (Tier 2) |

#### 3. Inspección de Integridad Forense
En cambios clasificados como Tier 3 (Epic) o módulos de seguridad/autenticación, el Forensic Auditor (Tier 1) verifica que:
- Ninguna aserción de test preexistente haya sido debilitada, eliminada o alterada para enmascarar regresiones.
- No se hayan introducido mocks engañosos que simulen éxito sin validar la frontera real.
- No existan dependencias no autorizadas añadidas en los archivos de bloqueo (`pnpm-lock.yaml`, `package-lock.json`, etc.).
- Tras validar, el Auditor emite su Veredicto de Integridad Forense.

---

### Etapa 2: Sincronización de Memoria de Proyecto (Engram.md)

La **Etapa 2** asegura que el conocimiento adquirido durante la implementación se transfiera de la memoria volátil de la sesión al archivo vivo persistente [Engram.md](../Engram.md), gobernado por las reglas de [engram.md](engram.md).

#### 1. Obligatoriedad Innegociable (`engram_sync_enforcement`)
El cierre de cualquier tarea sin la sincronización de `Engram.md` constituye una violación crítica de gobernanza SDD. Toda entrega debe reflejar de forma determinista el impacto del cambio.

#### 2. Áreas de Sincronización en Engram.md
1. **Decisiones Técnicas Tomadas (micro-ADR)**:
   - Registrar en formato sintético: qué se decidió, qué alternativas se descartaron y la razón arquitectónica.
   - *Ejemplo*: `- **Adopción de SQLite embebido**: Descartado PostgreSQL local para simplificar despliegue en entornos de prueba autónomos.`
2. **Estado de Módulos**:
   - Actualizar la tabla de módulos de `## 4. Estado de Módulos` en `Engram.md`, marcando los módulos intervenidos como `Estable`, `COMPLETADO` o `✅`.
3. **Invariantes y Comandos** (si aplica):
   - Registrar nuevos comandos o scripts de utilidad estándar introducidos por el cambio.

#### 3. Cumplimiento del Presupuesto de Líneas (80 a 150 Líneas)
- El archivo `Engram.md` debe mantenerse en todo momento dentro del rango estricto de **80 a 150 líneas**.
- **Protocolo de Poda (*Pruning*)**: Si al incorporar los nuevos registros `Engram.md` excede las 150 líneas, el agente responsable debe archivar las decisiones históricas consolidadas o superadas en `docs/engram_archive.md`, manteniendo el archivo activo por debajo del límite de 150 líneas.
- **Densidad de Información**: Prohibido insertar código fuente en bloque, transcripciones de chat o logs de compilación.

---

### Etapa 3: Sincronización de Especificaciones y Documentación

La **Etapa 3** formaliza el ciclo de vida documental cerrando la especificación que guió la implementación y sincronizando los manuales y referencias técnicas afectadas.

#### 1. Transición Estricta a Estado COMPLETADA (`spec_status_sync_enforcement`)
- Ninguna especificación técnica puede permanecer en estado `DRAFT`, `BORRADOR` ni `IN PROGRESS` tras concluir el desarrollo.
- El bloque de metadatos o cabecera de la spec (e.g. en `specs/`) debe actualizarse obligatoriamente con el siguiente formato:

```markdown
**Estado**: COMPLETADA
**Fecha de cierre**: 2026-10-29T20:15:00Z
**Verificador**: Reviewer (Tier 2) / Orchestrator (Tier 1)
**Commit de Cierre**: 8a4b2c1f
```

- Si una entrega intenta cerrarse manteniendo el estado `IN PROGRESS` o `DRAFT`, el gate de salida **aborta la operación y rechaza el cierre**.

#### 2. Actualización de Documentación de Soporte
- Si el cambio introduce alteraciones en contratos de interfaz, flags de configuración, variables de entorno o endpoints de API, se deben actualizar los documentos correspondientes en `docs/` y el archivo general `README.md`.
- En cambios de arquitectura mayor (Tier 3), se debe verificar que el Architectural Decision Record correspondiente en `docs/` esté enlazado y referenciado.

---

### Etapa 4: Generación de Registro de Cambios (Keep a Changelog)

La **Etapa 4** garantiza la trazabilidad cronológica estructurada de las modificaciones para desarrolladores humanos, agentes autónomos y sistemas de CI/CD.

#### 1. Adhesión Estricta a Categorías Canónicas (`changelog_format_enforcement`)
El registro de cambios en `CHANGELOG.md` debe apegarse con total rigidez al estándar internacional *Keep a Changelog*. Se prohíbe el uso de encabezados arbitrarios o informales (e.g. `Misc:`, `Otras cosas:`, `Cambios varios:`).

Las únicas categorías autorizadas son:
- `### Added`: Nuevas características, funcionalidades o contratos introducidos.
- `### Changed`: Cambios en comportamientos, firmas o funcionalidades existentes.
- `### Deprecated`: Funcionalidades obsoletas que se mantendrán temporalmente antes de su remoción.
- `### Removed`: Funcionalidades o dependencias retiradas definitivamente del código.
- `### Fixed`: Corrección de errores, defectos funcionales o bugs identificados.
- `### Security`: Mejoras de seguridad, resolución de vulnerabilidades o blindaje de invariantes.

#### 2. Ejemplo Canónico de Entrada de Cierre
```markdown
## [1.2.0] - 2026-10-29

### Added
- Protocolo formal de cierre post-implementación (Phase N) en `harnesses/closing.md`.
- Matriz de evidencia de verificación PVS para correlación de criterios de aceptación.

### Changed
- Sincronización obligatoria de memoria persistente en `Engram.md` con límite de 150 líneas.
- Transición estricta de estado de especificaciones a `COMPLETADA`.

### Fixed
- Prevención de cierres prematuros ante fallos en suites de tests o linters.
```

#### 3. Estandarización de Mensaje de Commit de Cierre
Siguiendo las pautas de [review.md](review.md) y [ai-rules.md](ai-rules.md), el commit de cierre debe estructurarse como:
`tipo(alcance): descripción en infinitivo, minúsculas, sin punto final`
*Ejemplos*:
- `fix(auth): correct token expiration calculation in session handler`
- `feat(closing): implement post-change closing protocol harness in harnesses/closing.md`

---

## 4. Adaptación del Cierre por Tamaño de Cambio (Sizing Matrix)

El nivel de rigor y burocracia documental de la Fase N escala armónicamente según la magnitud del cambio, conforme a las directrices de `harnesses/sizing.md`:

| Dimensión de Cierre | Tier 1: Small / Hotfix (Fast-Track) | Tier 2: Medium / Standard (Standard SDD) | Tier 3: Large / Epic (Epic SDD) |
|---|---|---|---|
| **Plantilla de Spec** | `specs/_template_hotfix.md` (<25 líneas) | [specs/_template.md](../specs/_template.md) | `specs/_template_epic.md` |
| **Verificación PVS** | Comando de verificación unitario ejecutado y superado (PVS light) | Matriz completa de evidencia PVS con cada Criterio de Aceptación verificado | Suite E2E exhaustiva + Verificación por cada hito/slice descomprimido |
| **Sincronización Engram** | Actualización concisa de 1 línea en `## 4. Estado de Módulos` | Nueva decisión micro-ADR en `## 3` + estado de módulo actualizado a `Estable` | Consolidación profunda de decisiones + poda obligatoria a `docs/engram_archive.md` (80-150 líneas) |
| **Estado de Spec** | Micro-spec marcada `COMPLETADA` al pie del archivo | Cabecera formal actualizada a `COMPLETADA` con fecha y firma de Revisor | Spec épica y todos sus sub-slices marcados como `COMPLETADA` |
| **Changelog** | Entrada sintética en `### Fixed` o mensaje de commit convencional | Entrada formal en `CHANGELOG.md` bajo categorías canónicas | Release notes completos con secciones `Changed`, `Added` y `Security` |
| **Rol Verificador** | Worker (Tier 2/3) con supervisión de Revisor | Reviewer (Tier 2) + Orchestrator (Tier 1) | Challenger (Tier 1) + Forensic Auditor (Tier 1) + Orchestrator |

---

## 5. Checklist de Salida y Gate de Aprobación

El **Checklist de Salida** es la barrera final que debe evaluarse en su totalidad antes de considerar concluida la tarea. La omisión o fallo de cualquiera de los puntos bloquea el paso por el Gate de Aprobación.

### Checklist de Salida

- [ ] **1. Clean Build**: La compilación (`Build`) en frío del proyecto se ejecuta sin errores ni advertencias en el entorno destino.
- [ ] **2. Suite de Tests 100% Aprobada**: Todos los tests unitarios, de integración y E2E pasan satisfactoriamente (`exit code 0`). Cero tests fallidos tolerados (`pvs_verification_enforcement`).
- [ ] **3. Matriz de Evidencia PVS Completa**: Cada uno de los Criterios de Aceptación de la spec cuenta con comando ejecutado y evidencia textual registrada.
- [ ] **4. Respeto al Techo de Líneas de PR**: El tamaño del cambio respeta los límites definidos en [review.md](review.md) (Tier 1: ≤80 LOC; Tier 2: ≤300 LOC; Tier 3: dividido en PRs/slices menores a 300 LOC).
- [ ] **5. Invariantes de Código y Tipado Seguro**: Ausencia de tipos evasivos (`any`), cumplimiento de linters y apego al principio Ponytail (cero abstracciones redundantes).
- [ ] **6. Sincronización de Engram.md**: Memoria persistente [Engram.md](../Engram.md) actualizada con las decisiones técnicas y estado de módulos, respetando el límite estricto de **80 a 150 líneas** (`engram_sync_enforcement`).
- [ ] **7. Estado de Spec COMPLETADA**: La especificación técnica está formalmente rotulada como `COMPLETADA` con su Fecha de cierre y verificador registrado (`spec_status_sync_enforcement`).
- [ ] **8. Registro Estandarizado en Changelog**: Entrada generada en `CHANGELOG.md` respetando estrictamente las categorías canónicas de *Keep a Changelog* (`Added`, `Changed`, `Fixed`, etc.) (`changelog_format_enforcement`).
- [ ] **9. Certificado Forense de Integridad**: En cambios Tier 3 o de alta criticidad, aprobación explícita del Forensic Auditor confirmando que ningún test fue alterado y no hay dependencias no autorizadas.

### Protocolo del Gate de Aprobación
1. **Inspección del Gate**: El **Orchestrator (Tier 1)** revisa el Checklist de Salida compilado por el Worker y validado por el Reviewer.
2. **Decisión Binaria**:
   - **APROBADO**: Si los 9 puntos están marcados y verificados, el Orchestrator autoriza el merge a la rama principal y actualiza el estado del proyecto en [state.md](state.md).
   - **VETADO / RECHAZADO**: Si algún punto presenta deficiencias, se rechaza la entrega, se emite una notificación de coordinación (<300 palabras) con la causa exacta del rechazo y se retorna a la fase previa correspondiente.

---

## 6. Asignación de Roles y Presupuesto de Tokens en Fase N

En cumplimiento con la taxonomía de 7 roles y la regla del presupuesto 80/20 de [multi-agent.md](multi-agent.md):

```
Etapa 1 (PVS: Clean Build & Tests)  ──► Worker (Tier 2/3) + Reviewer (Tier 2)
Etapa 2 (Engram.md Sync)            ──► Reviewer (Tier 2) / Worker (Tier 2)
Etapa 3 (Spec Sync: COMPLETADA)     ──► Reviewer (Tier 2)
Etapa 4 (Changelog Generation)      ──► Reviewer (Tier 2) / Worker (Tier 2)
Integrity Audit (si aplica Tier 3)  ──► Forensic Auditor (Tier 1 Pro)
Gate de Aprobación Final            ──► Orchestrator (Tier 1 Pro)
```

- **Economía de Tokens**: El 85% de la carga de ejecución (compilaciones, formateo de logs, redacción de tablas PVS) corre sobre modelos económicos Tier 2/3.
- **Canales de Comunicación**: Todos los reportes detallados y trazas de salida se escriben directamente a archivos (`handoff.md`, `specs/`, `.agents/teamwork/`). Los mensajes entre agentes se limitan a la notificación de cambio de estado y ruta de handoff (<300 palabras).

---

## 7. Manejo de Errores y Disyuntor en Fase de Cierre

Si durante la ejecución de la Fase N se presentan incidencias:

1. **Fallo en Test Unitario durante PVS**:
   - El Worker aplica el **Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)** definido en [isolation.md](isolation.md).
   - Intento 1: Diagnóstico y ajuste quirúrgico del código de producción (sin modificar los tests existentes).
   - Intento 2: Segunda verificación. Si vuelve a fallar, se detiene de inmediato (**CIRCUIT BREAKER TRIPPED**) y escala al Orchestrator. Queda prohibido el Intento 3 desasistido.
2. **Desbordamiento de Presupuesto de Engram (>150 líneas)**:
   - Se pausa la entrega y se ejecuta el archivado de decisiones a `docs/engram_archive.md` antes de conceder la aprobación del gate.
3. **Inconsistencia de Categorías en Changelog**:
   - El Reviewer rechaza automáticamente el changelog y exige reescribir bajo las 6 categorías canónicas autorizadas.

---

## 8. Matriz Resumen de Reglas de Invarianza Normativa

| Identificador de Regla | Ámbito de Aplicación | Condición de Fallo / Violación | Comportamiento del Gate de Salida |
|---|---|---|---|
| `pvs_verification_enforcement` | Etapa 1: PVS | 1 o más tests fallidos, código no compila en limpio o criterios sin evidencia | **Veto Inmediato**: Bloquea el cierre. Exige 100% de tests verdes. |
| `engram_sync_enforcement` | Etapa 2: Engram.md | Cierre ejecutado sin actualizar decisiones o estado de módulos en Engram.md | **Rechazo de Gate**: Actualización de memoria persistente es innegociable. |
| `line_count_range_enforcement` | Etapa 2: Engram.md | Engram.md contiene menos de 80 o más de 150 líneas | **Bloqueo por Presupuesto**: Exige poda o completitud de secciones estándar. |
| `spec_status_sync_enforcement` | Etapa 3: Specs | Spec permanece en `DRAFT` o `IN PROGRESS` al momento de entrega | **Rechazo de Entrega**: Exige actualizar a `COMPLETADA` con fecha y firma. |
| `changelog_format_enforcement` | Etapa 4: Changelog | Uso de encabezados arbitrarios (e.g. `Misc:`) en vez de categorías canónicas | **Rechazo de Formato**: Exige categorías estándar de Keep a Changelog. |
| `file_presence_enforcement` | Arquitectura SDD | Archivo `harnesses/closing.md` ausente o eliminado del repositorio | **Fallo Estructural**: Rompe el marco de gobernanza del ciclo de vida. |

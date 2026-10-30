# Phase Harness — Flujo de Trabajo y Enrutamiento Adaptable SDD

Este harness define el enrutamiento formal del ciclo de vida de desarrollo de software bajo la metodología **SDD (Software-Design-Driven)**. Reemplaza cualquier flujo monolítico o acoplado a herramientas comerciales por una arquitectura de enrutamiento adaptativa y agnóstica de tres ramas (**Tier 1**, **Tier 2** y **Tier 3**), gobernada determinísticamente por la matriz de dimensionamiento de [harnesses/sizing.md](sizing.md).

Opera bajo la premisa rectora de que el proceso gobierna el ciclo de vida: **ningún código se escribe sin una especificación previa aprobada en `specs/`**. Asimismo, se integra armónicamente con la filosofía de eficiencia [ponytail](ai-rules.md) dentro de cada fase y se fundamenta en la taxonomía de roles de [multi-agent.md](multi-agent.md).

---

## 1. Arquitectura de Enrutamiento Tri-Rama

El flujo de trabajo se descompone en tres vías diferenciadas según el tamaño e impacto del cambio, acotadas en sus extremos por la **Fase 0: DISCOVERY** y la **Fase N: CLOSING**:

```
                   ┌───────────────────────────────┐
                   │       FASE 0: DISCOVERY       │  (Auditoría Previa de 6 Pilares:
                   │   (Pre-Change Architecture)   │   Obligatoria en Brownfield o Tier 3)
                   └───────────────┬───────────────┘
                                   │
                                   ▼
                   ┌───────────────────────────────┐
                   │       CLASIFICAR TIER         │  (Determinación objetiva según
                   │    (harnesses/sizing.md)      │   archivos, LOC, esquema y riesgo)
                   └───────────────┬───────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         ▼                         ▼                         ▼
   ┌───────────┐             ┌───────────┐             ┌───────────┐
   │  TIER 1   │             │  TIER 2   │             │  TIER 3   │
   │Fast-Track │             │ Standard  │             │   Epic    │
   │(Hotfix /  │             │   (SDD    │             │  (Macro-  │
   │ Micro-SDD)│             │ Estándar) │             │ Proyectos)│
   └─────┬─────┘             └─────┬─────┘             └─────┬─────┘
         │                         │                         │
         ▼                         ▼                         ▼
   ┌───────────┐             ┌───────────┐             ┌───────────┐
   │Micro-Spec │             │   FASE 1  │             │ EPIC SPEC │
   │(<25 líneas│             │    SPEC   │             │(Desglose  │
   │en specs/) │             │(_template)│             │en Slices) │
   └─────┬─────┘             └─────┬─────┘             └─────┬─────┘
         │                         │                         │
         │                         ▼                         ▼
         │                   ┌───────────┐             ┌───────────┐
         │                   │   FASE 2  │             │ Gate de   │
         │                   │  DISEÑO   │             │ Revisión  │
         │                   │(Contratos)│             │Adversaria │
         │                   └─────┬─────┘             └─────┬─────┘
         │                         │                         │
         ▼                         ▼                         ▼
   ┌───────────┐             ┌───────────┐             ┌───────────┐
   │   FASE 3  │             │   FASE 3  │             │ Slices    │
   │  IMPL     │             │   IMPL    │             │ Tier 2    │
   │(Directa)  │             │(Por Tareas│             │ (DAG      │
   └─────┬─────┘             └─────┬─────┘             │ ≤300 LOC) │
         │                         │                   └─────┬─────┘
         │                         │                         │
         ▼                         ▼                         ▼
   ┌───────────┐             ┌───────────┐             ┌───────────┐
   │   FASE 4  │             │   FASE 4  │             │   FASE 4  │
   │   VERIF   │             │   VERIF   │             │ VERIF E2E │
   │ (Focal)   │             │ (PR <300) │             │(Hardening)│
   └─────┬─────┘             └─────┬─────┘             └─────┬─────┘
         │                         │                         │
         └─────────────────────────┼─────────────────────────┘
                                   │
                                   ▼
                   ┌───────────────────────────────┐
                   │        FASE N: CLOSING        │  (PVS Sign-Off + Engram Sync +
                   │  (Post-Change Verification)   │   Docs/Changelog - OBLIGATORIO)
                   └───────────────────────────────┘
```

---

## 2. Taxonomía de Roles Funcionales y Agnósticos

Las etapas del ciclo de vida no dependen de herramientas comerciales ni marcas específicas de modelos, sino de roles funcionales con capacidades cognitivas definidas en [multi-agent.md](multi-agent.md):

| Rol Funcional | Nivel de Modelo Sugerido | Responsabilidad en el Flujo |
|---|---|---|
| **Orquestador** | Tier 1 (Pro / Alta Capacidad) | Clasificación de Sizing, diseño de SPEC y DISEÑO en Tier 2/3, despacho de tareas y arbitraje de salida. |
| **Discovery Specialist** | Tier 1 (Pro / Síntesis) | Ejecución de la Fase 0 en repositorios en producción brownfield y generación de `ARCH_AUDIT.md`. |
| **Trabajador** | Tier 2 / Tier 3 (Fast / Local SLM) | Implementación de código y tests acotados en Fase 3 según los Result Contracts de la spec. |
| **Revisor** | Tier 2 (Fast / Económico) | Verificación de Fase 4: validación de criterios de aceptación, diffs de PR ≤300 LOC, ausencia de `any` y ponytail. |
| **Challenger** | Tier 1 (Pro / Red Team) | Revisión adversarial en Fase 2 de Epics (Tier 3), búsqueda de casos límite y red teaming. |
| **Forensic Auditor** | Tier 1 (Pro / Gobernanza) | Inspección de integridad en Fase 4 y Fase N con autoridad de **VETO BINARIO** (tests adulterados, deps no autorizadas). |

---

## 3. Enrutamiento Detallado por Ramas de Ejecución

### Rama 1: TIER 1 — Vía Rápida (Fast-Track / Micro-SDD)

- **Ámbito de Aplicación**: Hotfixes, ajustes cosméticos de UI, corrección de typos, bumps de dependencias menores, adición de tests unitarios aislados.
- **Límites Deterministas**: 1 a 3 archivos modificados, ≤80 líneas netas de código (LOC), cero impacto en bases de datos o contratos de APIs públicas.
- **Artefacto de Especificación**: [specs/_template_hotfix.md](../specs/_template_hotfix.md) (archivo en `specs/` o `specs/hotfixes/` de **≤ 25 líneas estrictas**).
- **Flujo de Ejecución**:
  1. **Micro-Spec**: El **Trabajador** o el **Orquestador** redacta la micro-spec de <25 líneas documentando: Problema Detectado, Archivos Afectados (≤3), Solución Propuesta y Verificación.
  2. **Implementación Directa**: El **Trabajador** ejecuta quirúrgicamente el cambio. No se requieren diagramas pesados ni tablas de endpoints.
  3. **Verificación Focalizada**: Ejecución de build local y prueba unitaria sobre los archivos afectados.
  4. **Fase N (Closing Rápido)**: Validación del checklist de cierre rápido, commit convencional (`fix: ...`) y actualización sucinta en [Engram.md](../Engram.md).
- **Disyuntor de Escalación (Circuit Breaker)**: Si durante la implementación se detecta que el cambio requiere modificar un 4º archivo, añadir más de 80 LOC netas, o alterar un esquema/contrato público, el Trabajador **debe suspender la ejecución de inmediato**, escalar la tarea a **Tier 2** y requerir la redacción de una especificación formal completa antes de reanudar el trabajo.

---

### Rama 2: TIER 2 — Flujo Estándar SDD (Standard SDD)

- **Ámbito de Aplicación**: Nuevas funcionalidades medianas, endpoints CRUD, nuevos componentes de interfaz, extensiones de servicios y refactorizaciones modulares.
- **Límites Deterministas**: 4 a 10 archivos modificados, 80 a 300 líneas netas de código, cambios aditivos no destructivos en esquemas de persistencia.
- **Artefacto de Especificación**: [specs/_template.md](../specs/_template.md) (especificación estándar completa).
- **Compuertas Inviolables**: **Queda terminantemente prohibido saltar las fases SPEC o DISEÑO**. Cualquier código escrito sin spec previa aprobada constituye una violación directa de SDD.
- **Flujo de Ejecución de 4 Fases**:
  1. **FASE 1: SPEC**: El **Orquestador** define el *qué* y el *por qué*: descripción, criterios de aceptación formales (Given/When/Then con identificadores unívocos AC-XX), edge cases y límites fuera de scope.
  2. **FASE 2: DISEÑO**: El **Orquestador** modela la arquitectura técnica: esquema de datos, contratos de endpoints/componentes, firmas de tipos (prohibido `any`) y desglose de tareas con Result Contracts.
  3. **FASE 3: IMPLEMENTACIÓN**: El **Trabajador** implementa tarea por tarea. Aplica el **Disyuntor de 2 Intentos**: si tras 2 ejecuciones consecutivas el build o los tests fallan, detiene el trabajo y escala al Orquestador con el log de error textual.
  4. **FASE 4: VERIFICACIÓN**: El **Revisor** comprueba que el PR tenga ≤300 LOC, que no haya archivos modificados fuera del contrato de la spec, y que el 100% de los criterios de aceptación cuente con verificación comprobable.
  5. **FASE N: CLOSING**: Ejecución obligatoria de [harnesses/closing.md](closing.md) (Matriz PVS, actualización de [Engram.md](../Engram.md) dentro de 80-150 líneas, spec marcada como `COMPLETADA` y entrada en `CHANGELOG.md`).

---

### Rama 3: TIER 3 — Macro-Proyectos y Alta Garantía (Epic SDD)

- **Ámbito de Aplicación**: Módulos core nuevos, migraciones de base de datos o frameworks, rediseño de subsistemas, cambios con más de 10 archivos o >300 LOC.
- **Artefacto de Especificación**: [specs/_template_epic.md](../specs/_template_epic.md) con descomposición jerárquica en sub-specs Tier 2.
- **Flujo de Ejecución**:
  1. **FASE 0: DISCOVERY Obligatoria**: Auditoría exhaustiva de 6 pilares según [harnesses/discovery.md](discovery.md), produciendo el informe `ARCH_AUDIT.md`.
  2. **EPIC SPEC**: El **Orquestador** desglosa el macro-proyecto en slices / milestones independientes.
     - *Invariante de Descomposición*: Cada slice debe constituir un ciclo autónomo Tier 2 con PR ≤ 300 LOC. Las dependencias entre slices deben formar estrictamente un Grafo Acíclico Dirigido (DAG).
  3. **Gate de Revisión Adversaria**: El **Challenger** y el **Forensic Auditor** evalúan los riesgos arquitectónicos, contratos de datos, acoplamiento y estrategia de rollback. **La implementación de código está bloqueada hasta la firma formal de aprobación del gate**.
  4. **Implementación por Slices**: Cada slice se ejecuta secuencial o paralelamente por **Trabajadores** dedicados, siguiendo el flujo Tier 2 estándar.
  5. **FASE 4: VERIFICACIÓN E2E**: Ejecución de suites completas de integración, pruebas end-to-end y hardening adversarial sobre la totalidad de la solución integrada.
  6. **FASE N: CLOSING Global**: Protocolo integral de cierre con PVS consolidado, registro de Architectural Decision Records (ADRs) en `docs/`, poda profunda de [Engram.md](../Engram.md) y release notes.

---

## 4. Compuertas de Calidad y Mandatos del Ciclo de Vida

1. **Mandato de Descubrimiento (`discovery_mandate_enforcement`)**: Todo cambio en proyectos existentes en producción (brownfield) o de tamaño Tier 3 exige la ejecución previa de la **Fase 0: DISCOVERY** ([harnesses/discovery.md](discovery.md)). Prohibido iniciar modificaciones a ciegas.
2. **Mandato de Especificación Previa (`sdd_mandate_enforcement`)**: Ningún trabajador puede generar código sin una especificación aprobada en `specs/` (micro-spec para Tier 1, spec estándar para Tier 2, epic spec para Tier 3).
3. **Mandato de Cierre Obligatorio (`closing_mandate_enforcement`)**: Ninguna tarea en ningún tier (Tier 1, Tier 2 o Tier 3) se considera finalizada sin la ejecución exitosa de la **Fase N: CLOSING** ([harnesses/closing.md](closing.md)). La omisión del protocolo de cierre invalida la entrega.
4. **Alineación Terminológica Canónica (`terminology_alignment_enforcement`)**: Los nombres de las fases deben mantenerse idénticos y unificados a través de [AGENTS.md](../AGENTS.md), `phase.md` y [ai-rules.md](ai-rules.md):
   `DISCOVERY` ──► `SPEC` ──► `DISEÑO` ──► `IMPLEMENTACIÓN` ──► `VERIFICACIÓN` ──► `CLOSING`.

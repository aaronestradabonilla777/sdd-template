# Discovery & Architecture Audit Harness (Fase 0)

Este harness define el protocolo autoritativo y obligatorio de auditoría previa (Fase 0: Discovery) para proyectos bajo el framework SDD (Software-Design-Driven). Establece las pautas para inspeccionar repositorios brownfield (en producción o heredados) y greenfield antes de redactar especificaciones en `specs/` o modificar código en `src/`, previniendo colisiones de herramientas, baselines rotos no documentados y violaciones de invariantes arquitectónicas.

---

## 1. Cuándo se Ejecuta y Gobernanza del Ciclo de Vida

El protocolo de descubrimiento es la compuerta de entrada obligatoria al ciclo de vida SDD:

- **Obligatorio** al incorporar un agente por primera vez a un repositorio existente.
- **Obligatorio** antes de iniciar cualquier cambio Tier 2 (Mediano) o Tier 3 (Grande/Epic).
- **Opcional/Abreviado** en cambios Tier 1 (Hotfix), limitándose a verificar comandos de build/test del área afectada.

### Invariante de Ciclo de Vida: `discovery_mandate_enforcement`
En cualquier base de código preexistente en producción (brownfield), está **ESTRICTAMENTE PROHIBIDO** saltarse la Fase 0 de Discovery. Ningún Worker ni Orchestrator puede iniciar la redacción de especificaciones de diseño ni la mutación de código fuente sin haber completado y documentado la auditoría previa correspondiente a su nivel de cambio. Intentar implementar cambios a ciegas constituye una violación crítica de gobernanza SDD.

---

## 2. Roles, Responsabilidades y Fronteras de Seguridad

El proceso de descubrimiento es ejecutado por roles especializados según la taxonomía de `harnesses/multi-agent.md`:

| Rol Asignado | Nivel de Modelo | Responsabilidad en Fase 0 | Herramientas Autorizadas |
|---|---|---|---|
| **Discovery Specialist** | **Tier 1 (Pro / Frontier)** | Auditoría profunda de repositorios brownfield, análisis causal, síntesis arquitectónica en `ARCH_AUDIT.md` y actualización inicial de `Engram.md`. | `view_file`, `list_dir`, `grep_search`, `find_by_name`, escritura exclusiva de dossiers en `docs/discovery/`. |
| **Explorer** | **Tier 2 (Económico / Rápido)** | Mapeo preliminar de archivos, localización de configuraciones y extracción rápida de snippets para cambios Tier 1 y Tier 2. | 100% solo lectura: `view_file`, `list_dir`, `grep_search`, `find_by_name`. |
| **Orchestrator** | **Tier 1 (Pro / Frontier)** | Recepción del reporte de auditoría, evaluación en la matriz de Sizing (`harnesses/sizing.md`) y autorización del paso a Fase 1 (Spec). | Lectura y coordinación multi-agente. |

### Invariante de Seguridad: `read_only_audit_enforcement`
El rol de **Discovery Specialist** y el rol de **Explorer** operan bajo un régimen estricto de **100% SOLO LECTURA (READ-ONLY)** sobre el código de aplicación:
1. Queda **TERMINANTEMENTE PROHIBIDO** editar, crear, sobreescribir o eliminar archivos en `src/`, `lib/`, `packages/`, o configuraciones de producción durante la auditoría.
2. Los únicos artefactos escribibles durante la Fase 0 son el informe de auditoría (`docs/discovery/ARCH_AUDIT.md` o `.agents/teamwork/<agente>/analysis.md`) y la memoria viva del proyecto (`Engram.md`).
3. Cualquier intento de invocar herramientas de mutación sobre código de producción durante Discovery resulta en el **veto inmediato de la compuerta** y aborto de la sesión.

### Manejo de Restricciones de Sandbox: `sandbox_fallback_enforcement`
Si la ejecución de comandos shell para auditar el proyecto es denegada por restricciones del sandbox o del sistema operativo:
1. El agente tiene prohibido insistir o intentar evadir el sandbox.
2. Debe activar el fallback automático a herramientas de inspección estática nativas (`view_file`, `list_dir`, `grep_search`).
3. El análisis de dependencias, scripts y tipos debe completarse mediante inspección directa de manifiestos (`package.json`, `Cargo.toml`, `tsconfig.json`).

---

## 3. Protocolo de Auditoría Canónico en 6 Pasos (Los 6 Pilares)

La ejecución completa de Discovery debe auditar sistemáticamente los seis pilares arquitectónicos. De acuerdo con la regla `checklist_completeness_enforcement`, **los 6 pasos son obligatorios** para auditorías estándar y de Epic (Tier 2 y Tier 3). Omitir cualquiera de ellos invalida el informe de auditoría.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   LOS 6 PILARES DE AUDITORÍA PREVIA                   │
├────────────────────────────────────────────────────────────────────────┤
│  Paso 1: Topografía del Repositorio y Fuentes de Verdad                │
│  Paso 2: Grafo de Dependencias y Runtime Engine                        │
│  Paso 3: Inventario de Verificación y Estado de la Línea Base          │
│  Paso 4: Convenciones de Código y Patrones Arquitectónicos             │
│  Paso 5: Invariantes Arquitectónicas y Fronteras de Seguridad          │
│  Paso 6: Deuda Técnica, Fragilidades y Marcadores Conocidos            │
└────────────────────────────────────────────────────────────────────────┘
```

### Paso 1: Topografía del Repositorio
- **Objetivo**: Mapear la estructura global del repositorio y localizar las fuentes de verdad documentales.
- **Actividades de Inspección**:
  1. Determinar si el proyecto es un repositorio único (polyrepo) o un monorepo multienvase. Buscar manifiestos de workspace: `pnpm-workspace.yaml`, `lerna.json`, `nx.json`, `turbo.json`, `Cargo.toml` con `[workspace]`, o `go.work`.
  2. Localizar las fuentes de verdad autoritativas existentes:
     * Especificaciones previas: `specs/`
     * Documentación y decisiones: `docs/`, `ADR/`, `RFC/`
     * Reglas de agentes y gobernanza: `AGENTS.md`, `CLAUDE.md`, `OPENCODE.md`, `harnesses/`
     * Memoria persistente viva: `Engram.md`
  3. Mapear los directorios estructurales principales (`src/`, `apps/`, `packages/`, `tests/`, `public/`, `scripts/`).

### Paso 2: Grafo de Dependencias y Runtime
- **Objetivo**: Identificar el gestor de paquetes oficial, el runtime requerido y el mapa de librerías base.
- **Actividades de Inspección**:
  1. **Detección Innegociable del Gestor de Paquetes (`package_manager_enforcement`)**:
     * Si existe `pnpm-lock.yaml` → Usar ESTRICTAMENTE `pnpm`. Prohibido ejecutar `npm install` o `yarn`.
     * Si existe `yarn.lock` → Usar ESTRICTAMENTE `yarn`. Prohibido usar `npm`.
     * Si existe `package-lock.json` → Usar ESTRICTAMENTE `npm`.
     * Si existe `bun.lockb` → Usar ESTRICTAMENTE `bun`.
     * Si existe `Cargo.lock` → Usar ESTRICTAMENTE `cargo`.
     * Si existe `poetry.lock` o `Pipfile.lock` → Usar ESTRICTAMENTE el gestor correspondiente.
     * *Regla innegociable:* Nunca cambiar de gestor de paquetes, nunca mezclar comandos de gestores rivales y nunca commitear lockfiles secundarios.
  2. **Inspección de Runtime**:
     * Node / JavaScript: campo `engines` en `package.json`, `.nvmrc`, `.node-version`.
     * Python: `pyproject.toml`, `.python-version`.
     * Rust: `rust-toolchain.toml`, campo `edition` en `Cargo.toml`.
  3. **Mapeo de Dependencias Clave**: Framework web (Astro, Next.js, React, Axum, FastAPI), librerías de estado, ORM/DB drivers, frameworks de testing y dependencias pesadas.

### Paso 3: Inventario de Verificación y Estado de la Línea Base
- **Objetivo**: Extraer los comandos canónicos de verificación y certificar la salud de la línea base preexistente antes de realizar modificaciones.
- **Actividades de Inspección**:
  1. **Catálogo de Comandos Nativos de Verificación**:
     * Verificación de tipos (Typecheck): ej. `pnpm tsc --noEmit`, `cargo check`, `mypy`.
     * Linter y formato: ej. `pnpm lint`, `biome check`, `cargo clippy`, `flake8`.
     * Pruebas automatizadas (Tests): ej. `pnpm test`, `vitest run`, `cargo test`, `pytest`.
     * Compilación y empaquetado (Build): ej. `pnpm build`, `cargo build --release`.
  2. **Auditoría de Línea Base Preexistente (`baseline_audit_enforcement`)**:
     * Ejecutar la suite de tests y el comando de build ANTES de tocar una sola línea de código.
     * Si se detectan pruebas ya fallidas, errores de tipos preexistentes o advertencias en la línea base, **DOCUMENTARLOS DE INMEDIATO** en el informe de auditoría con sus nombres y trazas textuales exactas.
     * Ningún agente podrá ser responsabilizado por roturas preexistentes documentadas, pero tiene estrictamente prohibido agravarlas o ignorarlas sin reportar.
  3. **Protocolo de Fallback sin Tests Automatizados (`verification_fallback_enforcement`)**:
     * Si el repositorio legado no contiene suites de pruebas automatizadas o scripts en `package.json`:
     * Es **MANDATORIO** que la especificación SDD incorpore una sección de **Checklist de Verificación Manual** con pasos paso a paso reproducibles.
     * Es **MANDATORIO** aportar **Evidencia Visual o de Integración** (logs de terminal limpios, validaciones HTTP `curl`, o capturas de renderizado en UI) como sustituto formal de la suite automatizada.

### Paso 4: Convenciones de Código y Patrones Arquitectónicos
- **Objetivo**: Extraer los patrones de diseño implícitos y explícitos para que el nuevo código sea indistinguible del código base original.
- **Actividades de Inspección**:
  1. **Patrón Arquitectónico**:
     * Identificar la arquitectura del código: Feature-Sliced Design (FSD), Clean Architecture / Puertos y Adaptadores, MVC en capas, o Modular Monolith.
     * Respetar la jerarquía de capas (ej. componentes UI no invocan directamente la base de datos).
  2. **Sistema de Diseño y Tokens de Estilo**:
     * Localizar archivos de tokens CSS (`tokens.css`, CSS variables, tokens de color/espaciado).
     * Configuración de estilos (`tailwind.config.mjs`, `@apply` rules, CSS Modules).
     * Regla de no-duplicación de activos y reutilización de componentes base.
  3. **Convenciones de Nombrado y Tipado**:
     * Convenciones de archivos (`kebab-case.ts`, `PascalCase.tsx`).
     * Tipado estricto: prohibido el uso de `any`; verificar tipos compartidos en `src/types/` o `src/shared/`.
     * Co-ubicación de tests unitarios junto al código fuente vs carpeta centralizada `tests/`.

### Paso 5: Invariantes Arquitectónicas y Fronteras de Seguridad
- **Objetivo**: Descubrir las reglas sagradas del sistema que nunca deben romperse y los perímetros de seguridad.
- **Actividades de Inspección**:
  1. **Fronteras de Aislamiento Modular**:
     * Módulos que tienen prohibido comunicarse directamente (ej. módulo de Pagos no importa directamente de módulo de Reportes).
     * Puntos únicos de entrada autorizados (index exports, facades, servicios de dominio).
  2. **Fronteras de Validación de Entradas**:
     * Validación estricta en el borde (schemas Zod, DTOs, serializadores, validación de parámetros de URL y cuerpos de peticiones HTTP).
     * Sanitización de datos para prevención de inyecciones (SQLi, XSS).
  3. **Límites de Persistencia y Acceso a Datos**:
     * Prohibición de queries crudas en controladores o vistas.
     * Manejo de transacciones y pools de conexión.
  4. **Seguridad, Secretos y Variables de Entorno**:
     * Detección de `.env.example`, `.env.local` y variables requeridas.
     * Invariante innegociable: Ningún secreto ni credencial puede residir en código fuente bajo control de versiones.

### Paso 6: Deuda Técnica, Fragilidades y Marcadores Conocidos
- **Objetivo**: Localizar fragilidades latentes para evitar activar minas terrestres durante la implementación.
- **Actividades de Inspección**:
  1. **Escaneo de Marcadores de Deuda Técnica**:
     * Búsqueda global (`grep_search`) de etiquetas: `TODO`, `FIXME`, `HACK`, `BUG`, `DEPRECATED`, `ponytail:`.
     * Identificar qué simplificaciones deliberadas previas existen en el área de trabajo.
  2. **Fragilidades y Pruebas Intermitentes**:
     * Identificar tests marcados como omitidos (`test.skip`, `xit`, `#[ignore]`) o tests con sleeps arbitrarios.
     * Advertencias recurrentes de compilación o linteo.
  3. **Dependencias Obsoletas o Vulnerables**:
     * Paquetes deprecados conocidos en el stack.

---

## 4. Modos de Ejecución Adaptativos según Sizing

El rigor y alcance de Discovery se adaptan determinísticamente al tamaño del cambio según `harnesses/sizing.md`:

```
┌────────────────────────────────────────────────────────────────────────┐
│                   MODOS DE EJECUCIÓN DE DISCOVERY                      │
├────────────────────────────────────────────────────────────────────────┤
│  MODO A: Light Discovery (Tier 1 - Hotfix / Pequeño)                   │
│  - Alcance: Pasos 1 a 3 focalizados en el componente afectado.         │
│  - Artefacto: Sección breve en micro-spec (specs/_template_hotfix.md). │
│  - Presupuesto de Tokens: < 1,500 tokens.                              │
├────────────────────────────────────────────────────────────────────────┤
│  MODO B: Standard Discovery (Tier 2 - Mediano / Estándar)              │
│  - Alcance: Pasos 1 a 5 completos en el subsistema relevante.          │
│  - Artefacto: Sección en la spec formal (specs/_template.md).          │
│  - Presupuesto de Tokens: ~3,000 - 5,000 tokens.                       │
├────────────────────────────────────────────────────────────────────────┤
│  MODO C: Full 6-Pillar Discovery (Tier 3 - Grande / Epic)              │
│  - Alcance: Todos los 6 pasos obligatorios a nivel de repositorio.     │
│  - Artefacto: Dossier formal docs/discovery/ARCH_AUDIT.md y Engram.md. │
│  - Presupuesto de Tokens: ~8,000 - 15,000 tokens (Discovery Specialist)│
└────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Salida de la Auditoría y Sincronización de Memoria

Los hallazgos del protocolo de descubrimiento deben persistirse obligatoriamente según el tipo de intervención:

1. **Para Cambios Tier 3 (Epics o Onboarding Inicial en Brownfield)**:
   - Crear el dossier maestro en `docs/discovery/ARCH_AUDIT.md` utilizando la plantilla canónica descrita en la Sección 6.
   - Si no existe `Engram.md`, inicializarlo con la información recolectada respetando el presupuesto de 80–150 líneas (`harnesses/engram.md`).
   - Si ya existe `Engram.md`, actualizar las secciones `## 1. Stack e Infraestructura` y `## 2. Invariantes y Comandos`.
2. **Para Cambios Tier 2 (Estándar)**:
   - Consignar los hallazgos en la sección `## Contexto y Descubrimiento Previo` de la especificación SDD (`specs/<id>-feature.md`).
3. **Para Cambios Tier 1 (Hotfix)**:
   - Registrar la verificación de lockfile y comando de prueba en el micro-spec (`specs/hotfixes/<id>.md`).

---

## 6. Plantilla Maestra Canónica para `ARCH_AUDIT.md`

Todo dossier formal de auditoría de arquitectura brownfield generado por el Discovery Specialist debe estructurarse con la siguiente plantilla estandarizada:

```markdown
# Architectural Audit Dossier (ARCH_AUDIT.md)

- **Fecha de Auditoría**: YYYY-MM-DD
- **Auditor**: [Discovery Specialist (Tier 1 Pro) / Explorer]
- **Repositorio**: [URL o nombre del repositorio]
- **Línea Base Git Hash**: [commit SHA inicial]
- **Modo de Descubrimiento**: [Full 6-Pillar Brownfield / Subsystem Audit]

---

## 1. Topografía del Repositorio y Fuentes de Verdad
- **Tipo de Repositorio**: [Monorepo (pnpm workspaces / turborepo / cargo workspace) | Repo único]
- **Workspace Roots**: [Rutas de paquetes, ej. apps/web, packages/ui, packages/core]
- **Fuentes de Verdad Detectadas**:
  * Especificaciones: [specs/ o N/A]
  * Documentación: [docs/ o README.md]
  * Gobernanza / Agentes: [AGENTS.md, harnesses/, CLAUDE.md]
  * Memoria Persistente: [Engram.md (Presente / Ausente)]

## 2. Runtime, Gestor de Paquetes y Grafo de Dependencias
- **Gestor de Paquetes Innegociable**: [pnpm | npm | yarn | cargo | bun]
- **Lockfile Oficial**: [pnpm-lock.yaml | package-lock.json | yarn.lock | Cargo.lock]
- **Runtime y Versiones**: [ej. Node v20.x, Rust 1.78, Python 3.11]
- **Dependencias Críticas de Producción**:
  * [Dependencia 1] (versión, rol arquitectónico)
  * [Dependencia 2] (versión, rol arquitectónico)
- **Dependencias de Desarrollo Clave**:
  * [Linter / Test Runner / Compilador]

## 3. Matriz de Verificación y Estado de la Línea Base
- **Comandos Oficiales de Verificación**:
  * Typecheck: `[comando]`
  * Lint / Formato: `[comando]`
  * Tests Unitarios / Integración: `[comando]`
  * Build de Producción: `[comando]`
- **Estado de la Línea Base Previa (Baseline Health)**:
  * Build Inicial: [PASS / FAIL / WARN]
  * Suite de Tests Inicial: [PASS: X/Y tests | FAIL: N tests fallan previamente]
  * Errores Preexistentes Documentados:
    - `[Nombre del test o archivo]`: [Error textual detectado]
- **Mecanismo de Fallback (si no hay tests automatizados)**:
  * [Checklist manual requerido y evidencia visual obligatoria]

## 4. Convenciones de Código y Patrones de Arquitectura
- **Patrón Arquitectónico Identificado**: [Feature-Sliced Design / Clean Architecture / MVC / Modular]
- **Sistema de Diseño y Tokens UI**:
  * Ubicación de tokens: [ej. src/styles/tokens.css]
  * Configuración CSS: [Tailwind / Vanilla CSS variables / CSS Modules]
- **Convenciones de Nombres y Estilo**:
  * Archivos: [kebab-case / PascalCase]
  * Tipado: [TypeScript Strict / Rust Types / Cero any]
  * Co-ubicación de Tests: [co-ubicados src/**/*.test.ts | directorio tests/]

## 5. Invariantes Arquitectónicas y Límites de Seguridad
- **Reglas de Aislamiento Modular**:
  * [Regla 1: ej. features no pueden importar directamente de otras features]
- **Validación de Entradas y Fronteras de Confianza**:
  * [Schemas Zod / DTOs / Sanitización en controladores]
- **Límites de Persistencia y Capas de Datos**:
  * [ORM / Clientes DB / Repositorios obligatorios]
- **Gestión de Secretos**:
  * [Variables en .env / Variables no versionadas obligatorias]

## 6. Inventario de Deuda Técnica y Fragilidades
- **Marcadores de Deuda Detectados (TODO / FIXME / ponytail)**:
  * `[archivo:línea]`: [Contenido del marcador]
- **Tests Intermitentes (Flaky) o Omitidos**:
  * [Lista de tests omitidos o identificados como inestables]
- **Advertencias Relevantes de Build**:
  * [Warnings ignorados o dependencias deprecadas]

## 7. Dictamen de Clasificación SDD y Recomendaciones
- **Clasificación Determinista del Cambio**: [Tier 1 (Hotfix) | Tier 2 (Mediano) | Tier 3 (Epic)]
- **Riesgos Arquitectónicos Identificados**:
  * [Riesgo 1 y mitigación sugerida]
- **Próximo Paso Inmediato**: [Redacción de Spec en specs/ bajo plantilla correspondiente]
```

---

## 7. Checklist de Cumplimiento de Discovery (Gate de Salida de Fase 0)

Antes de que el Orchestrator autorice la transición a la Fase 1 (Spec) del ciclo SDD, debe verificarse el siguiente checklist:

- [ ] **Topografía**: Estructura de repositorio y monorepo catalogada; fuentes de verdad localizadas.
- [ ] **Lockfile**: Gestor de paquetes oficial detectado; prohibición de gestores rivales confirmada (`package_manager_enforcement`).
- [ ] **Línea Base**: Build y tests ejecutados antes de modificar código; fallos preexistentes documentados (`baseline_audit_enforcement`).
- [ ] **Fallback**: Si no hay tests automáticos, checklist manual y evidencia visual definidos (`verification_fallback_enforcement`).
- [ ] **Solo Lectura**: Cero archivos de producción modificados durante la auditoría (`read_only_audit_enforcement`).
- [ ] **Completitud**: Los 6 pilares de auditoría debidamente evaluados (`checklist_completeness_enforcement`).
- [ ] **Memoria**: `Engram.md` inicializado o actualizado con el stack y comandos detectados.

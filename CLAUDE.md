# CLAUDE.md — Contexto y Entry Point para Claude Code CLI

Este archivo es el punto de entrada automático para **Anthropic Claude Code CLI** (`claude`).
Configura las reglas de ejecución, optimización de cache y prompt prefix caching, y gobernanza SDD para modelos Claude 3.7 Sonnet, Claude 3.5 Sonnet y Claude 3.5 Haiku.

---

## 1. Reglas Maestras Inmutables

1. **Gobernanza SDD Obligatoria**: Ningún código se escribe sin una especificación previa en `specs/`. Consulta `harnesses/phase.md` y `harnesses/sizing.md`.
2. **Filosofía Ponytail Activa**: Modo "lazy senior dev". No sobre-ingeniería; la solución más simple y robusta con la librería estándar siempre gana. Ver `AGENTS.md`.
3. **Límite Estricto de Cambios**: Ningún PR o entrega puede superar las **300 líneas modificadas**. Cambios mayores deben descomponerse en slices atómicos (`specs/_template_epic.md`).
4. **Tipado Estricto**: Cero uso de `any`. Tipos explícitos y verificados.
5. **Disciplina de Commits**:
   - Formato convencional: `feat:`, `fix:`, `chore:`, `docs:`, `test:`, `refactor:`.
   - Mensajes concisos en imperativo, minúsculas, sin punto final.

---

## 2. Asignación de Roles y Model Tiering

Claude Code opera en dos modos principales según el tier de modelo seleccionado:

- **Modo Orquestador / Auditor (Tier 1 — Claude 3.7 Sonnet / Claude 3.5 Sonnet)**:
  - Asignado a: **Orchestrator**, **Discovery Specialist**, **Challenger** (revisión adversarial), y **Forensic Auditor**.
  - Responsable de redactar specs (`specs/`), diseñar arquitecturas, evaluar sizing de tareas y realizar verificaciones adversariales.
  - **Invariante**: No escribe código de implementación directamente cuando coordina un flujo multi-agente; delega al Worker.
- **Modo Implementador / Revisor (Tier 2 — Claude 3.5 Haiku)**:
  - Invocado con `--model claude-3-5-haiku` para reducir consumo de tokens en un ~80%.
  - Asignado a: **Worker** (implementación de Result Contracts) y **Reviewer** (inspección de linters, contratos y estilo).
  - Sujeto al **Disyuntor de 2 Intentos**: Si los tests locales fallan 2 veces tras su primer intento, deténte y reporta.

---

## 3. Disciplina de Prompt Caching (Ahorro de hasta 90%)

Para aprovechar el sistema de **Prompt Prefix Caching** de Anthropic:
- Las directrices de este archivo y de `AGENTS.md` se mantienen estáticas en la cabecera del contexto.
- No agregues marcas de tiempo o variables volátiles en `CLAUDE.md`.
- El estado mutable del proyecto se lee bajo demanda desde `Engram.md` y `specs/` utilizando las herramientas de lectura de archivos (`View`, `Grep`).
- Emplea el comando interactivo `/compact` periódicamente para condensar sesiones extensas sin perder directivas del sistema.

---

## 4. Estructura de Directorios Clave

```
specs/            ← Fuente de verdad (especificaciones antes que código)
├── _template_hotfix.md  # Micro-spec para cambios pequeños (<25 líneas)
├── _template.md         # Plantilla estándar para cambios medianos
└── _template_epic.md    # Plantilla para hitos grandes y epics
harnesses/        ← Reglas de proceso SDD, gobernanza multi-agente y aislamiento
docs/             ← Decisiones de arquitectura (ADRs) y guías de adaptadores
├── adapters/     ← Guías para Antigravity, Claude Code y OpenCode
└── prompts/      ← Prompts estandarizados para los 7 roles
Engram.md         ← Memoria viva del proyecto (<150 líneas)
```

---

## 5. Configuración Modular del Proyecto

> *Nota para el adoptante del template*: Declara aquí el stack y comandos específicos de tu proyecto.

### Stack Tecnológico
| Capa | Tecnología | Notas |
|---|---|---|
| Lenguaje / Runtime | [Node.js / TypeScript / Python / Rust / Go] | Versión LTS recomendada |
| Framework | [Astro / Next.js / FastAPI / Actix / etc.] | Arquitectura del proyecto |
| Base de Datos | [PostgreSQL / SQLite / SurrealDB / etc.] | Esquemas versionados |
| Gestor de Paquetes | [pnpm / cargo / uv / poetry] | Uso consistente |

### Comandos de Ejecución
```bash
# Instalación de dependencias
pnpm install

# Servidor de desarrollo
pnpm dev

# Verificación de tipos y linter
pnpm lint

# Ejecución de pruebas unitarias y de integración
pnpm test

# Build de producción
pnpm build
```

---

## 6. Referencias
- Guía completa de uso de Claude Code: `docs/adapters/claude_code.md`
- Manual multi-agente universal: `AGENTS.md`
- Taxonomía de roles: `harnesses/multi-agent.md`
- Aislamiento y circuit breaker: `harnesses/isolation.md`

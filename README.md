# SDD Template

Template para proyectos con **Spec-Driven Development** usando Claude Code + OpenCode + Ollama local.

## ¿Qué es SDD?

Spec-Driven Development es un flujo donde **ningún código existe sin una spec previa**. Antes de escribir una línea, defines qué se construye, por qué, y cuáles son los criterios para considerarlo terminado.

La spec es la fuente de verdad — no el código. Esto significa que puedes llevar la misma spec a Claude, Cursor, Gemini o cualquier otro agente y reconstruir el proyecto desde la intención. El código es una consecuencia de la spec, no al revés.

Esto evita el problema más común al trabajar con IA: que el modelo empiece a implementar antes de entender el problema, genere código fuera de scope, o invente features que nadie pidió.

## ¿Por qué Ollama?

OpenCode usa modelos locales vía Ollama — sin suscripciones, sin datos en la nube, sin costos por token de implementación. Claude Code se reserva para las decisiones que requieren razonamiento más profundo: diseño, arquitectura y revisión.

## Flujo de trabajo

```
SPEC (Claude) → DISEÑO (Claude) → IMPLEMENTACIÓN (OpenCode) → VERIFICACIÓN (Claude)
```

No se salta ninguna fase. Sin spec, no hay código.

| Fase | Quién | Output |
|------|-------|--------|
| SPEC | Claude | Archivo en `specs/` |
| DISEÑO | Claude | Esquema de datos, endpoints, componentes |
| IMPLEMENTACIÓN | OpenCode | Código en `src/` |
| VERIFICACIÓN | Claude + humano | PR aprobado |

## Archivos del template

### `CLAUDE.md`
Contexto que Claude lee al inicio de cada sesión: nombre del proyecto, stack, reglas específicas, comandos clave. Es la fuente de verdad para Claude sobre cómo trabajar en este proyecto.

### `Engram.md`
Memoria viva del proyecto. Claude la lee al inicio de cada sesión y la actualiza con decisiones tomadas, convenciones y contexto descubierto durante el desarrollo.

### `docs/`
Architecture Decision Records (ADRs) — decisiones técnicas importantes documentadas con su contexto y trade-offs. Usa `docs/ADR_template.md` como base.

### `specs/`
Una spec por feature. Cada spec define qué se construye, criterios de aceptación, edge cases, esquema de datos y endpoints antes de tocar el código.

### `harnesses/`
Reglas de proceso que Claude y OpenCode deben seguir. Son el "sistema operativo" del flujo de trabajo.

| Harness | Qué define |
|---------|-----------|
| `phase.md` | El flujo SPEC → DISEÑO → IMPLEMENTACIÓN → VERIFICACIÓN |
| `isolation.md` | Qué hace Claude vs qué hace OpenCode |
| `structure.md` | Estructura de carpetas: Feature Sliced Design + Clean Architecture |
| `ai-rules.md` | Reglas de comportamiento: no over-engineer, convención de commits, límite de PR |
| `state.md` | Estado de sesión — dónde se quedó el proyecto al retomar |
| `review.md` | Checklist de PR: tamaño, criterios, descripción |
| `contract.md` | Definition of Done — cuándo un feature está realmente terminado |

## Estructura de código

El template sigue **Feature Sliced Design** en frontend y **Clean Architecture por módulos** en backend. Es agnóstico al stack — adapta las extensiones a tu lenguaje. Ver `harnesses/structure.md` para la guía completa.

```
# Frontend (React / Vue / Svelte)
src/features/[nombre]/
  [Nombre].{tsx,vue,svelte}   ← vista
  [Nombre].viewModel.ts       ← lógica y estado
  [Nombre].types.ts           ← tipos e interfaces

# Backend (Rust / Node / Python / Go)
src/[nombre]/
  handler.{rs,ts,py}         ← rutas HTTP
  service.{rs,ts,py}         ← lógica de negocio
  model.{rs,ts,py}           ← tipos y structs
```

## Harnesses compatibles

Este template funciona con los tres harnesses principales. Usa el que prefieras — el flujo SDD es el mismo en todos.

| Harness | Archivo de contexto | Para qué |
|---------|-------------------|---------|
| [Claude Code](https://claude.ai/code) | `CLAUDE.md` | Diseño, arquitectura, revisión profunda |
| [Pi](https://pi.dev) | `AGENTS.md` | Terminal ligero, multi-modelo, extensible |
| [OpenCode](https://opencode.ai) | `CLAUDE.md` | Implementación con modelos locales vía Ollama |

## Inicio rápido por harness

### Claude Code
```bash
# CLAUDE.md ya está configurado
claude
```

### Pi
```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
pi install npm:pi-hermes-memory npm:pi-subagents npm:context-mode
pi
```

### OpenCode + Ollama
```bash
ollama pull qwen2.5-coder
opencode
```

## Inicio rápido general

1. Usa este repo como template en GitHub
2. Clona tu nuevo repo
3. Edita `Engram.md` con tu stack, puertos y decisiones iniciales
4. Edita `CLAUDE.md` / `AGENTS.md` con el contexto del proyecto
5. Si tu stack es soportado, instala skills automáticas: `npx autoskills`
6. Crea tu primera spec en `specs/` copiando `specs/_template.md`
7. El harness diseña → el agente local implementa → el harness verifica

## Paquetes Pi recomendados

Si usas Pi, estos paquetes están preconfigurados en `.pi/settings.json`:

| Paquete | Para qué |
|---------|---------|
| `pi-hermes-memory` | Memoria persistente entre sesiones (complementa `Engram.md`) |
| `pi-subagents` | Delega fases a sub-agentes, encaja con el modelo Claude/OpenCode |
| `context-mode` | Ahorra hasta 98% del context window en specs largas |

## Herramientas opcionales recomendadas

- [autoskills](https://github.com/midudev/autoskills) — detecta tu stack e instala skills de IA curadas automáticamente (`npx autoskills`). Soporta React, Next.js, Vue, TypeScript, Supabase, Tailwind y más.
- [caveman](https://github.com/JuliusBrussee/caveman) — reduce ~75% los tokens de output de Claude manteniendo precisión técnica (`/caveman` en Claude Code).

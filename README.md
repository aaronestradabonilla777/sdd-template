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

## Inicio rápido

1. Usa este repo como template en GitHub
2. Clona tu nuevo repo
3. Edita `Engram.md` con tu stack, puertos y decisiones iniciales
4. Edita `CLAUDE.md` con el contexto del proyecto
5. Si tu stack es soportado, instala skills automáticas: `npx autoskills`
6. Crea tu primera spec en `specs/` copiando `specs/_template.md`
7. Claude diseña → OpenCode implementa → Claude verifica

## Archivos del template

### `CLAUDE.md`
Contexto que Claude lee al inicio de cada sesión: nombre del proyecto, stack, reglas específicas, comandos clave. Es la fuente de verdad para Claude sobre cómo trabajar en este proyecto.

### `Engram.md`
Decisiones técnicas, stack, puertos, y arquitectura. Referencia rápida para cualquier dev (o IA) que entre al proyecto.

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

## Herramientas requeridas

- [Claude Code](https://claude.ai/code) — diseño, arquitectura, revisión
- [OpenCode](https://opencode.ai) — implementación con modelos locales
- [Ollama](https://ollama.ai) — runtime de modelos local (sin costo, sin cloud)

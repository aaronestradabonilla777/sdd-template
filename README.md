# SDD Template

Template para proyectos con **Spec-Driven Development** + Claude Code + OpenCode (Ollama local).

Cada feature nace como spec, se diseña antes de tocarse el código, y se implementa con IA local sin suscripciones.

## Inicio rápido

1. Usa este repo como template en GitHub
2. Clona tu nuevo repo
3. Edita `Engram.md` con tu stack y puertos
4. Edita `CLAUDE.md` con el contexto de tu proyecto
5. Crea tu primera spec en `specs/` usando `specs/_template.md`
6. Claude diseña → OpenCode implementa → Claude verifica

## Flujo de trabajo

```
SPEC (Claude) → DISEÑO (Claude) → IMPLEMENTACIÓN (OpenCode) → VERIFICACIÓN (Claude)
```

No se salta ninguna fase. Sin spec, no hay código.

## Estructura del template

```
harnesses/
├── phase.md       ← flujo de fases obligatorio
├── isolation.md   ← qué hace Claude vs OpenCode
├── structure.md   ← Feature Sliced Design + Clean Architecture
└── contract.md    ← Definition of Done

specs/
└── _template.md   ← plantilla para cada feature

CLAUDE.md          ← contexto del proyecto para Claude
Engram.md          ← stack, puertos, decisiones técnicas
```

## Estructura de código (FSD)

El template sigue **Feature Sliced Design** en frontend y **Clean Architecture por módulos** en backend. Ver `harnesses/structure.md` para la guía completa.

```
# Frontend
src/features/[nombre]/
  [Nombre].tsx
  [Nombre].viewModel.ts
  [Nombre].types.ts

# Backend (Rust / Axum)
src/[nombre]/
  handler.rs
  service.rs
  model.rs
  mod.rs
```

## Herramientas requeridas

- [Claude Code](https://claude.ai/code) — diseño, arquitectura, revisión
- [OpenCode](https://opencode.ai) — implementación con Ollama local
- [Ollama](https://ollama.ai) — IA local (sin suscripciones)

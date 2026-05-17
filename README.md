# SDD Template

Template para proyectos con Spec-Driven Development + Claude + OpenCode (Ollama local).

## Uso
1. Usa este repo como template en GitHub
2. Clona tu nuevo repo
3. Edita `Engram.md` con tu stack y puertos
4. Edita `CLAUDE.md` con el contexto de tu proyecto
5. Crea tu primera spec en `specs/` usando `specs/_template.md`
6. Abre Claude para diseñar, OpenCode para implementar

## Flujo de trabajo
```
SPEC (Claude) → DISEÑO (Claude) → IMPLEMENTACIÓN (OpenCode) → VERIFICACIÓN (Claude)
```

## Estructura
```
harnesses/
├── phase.md       ← flujo de fases obligatorio
├── isolation.md   ← qué hace Claude vs OpenCode
├── structure.md   ← Feature Sliced Design + Clean Architecture
└── contract.md    ← Definition of Done

specs/
└── _template.md   ← plantilla para cada feature

CLAUDE.md          ← contexto del proyecto
Engram.md          ← stack, puertos, decisiones
```

## Herramientas requeridas
- [Claude Code](https://claude.ai/code)
- [OpenCode](https://opencode.ai)
- [Ollama](https://ollama.ai)

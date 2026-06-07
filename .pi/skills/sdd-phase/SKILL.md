---
name: sdd-phase
description: Enforces the mandatory SDD phase flow (SPEC → DESIGN → IMPLEMENTATION → VERIFICATION) and blocks any coding before a spec exists
license: MIT
allowed-tools: ["read", "write", "bash"]
---

# SDD Phase Enforcement

Este skill enforza el flujo de fases obligatorio de Spec-Driven Development. No se salta ninguna fase. Sin spec, no hay código.

## Flujo obligatorio

```
SPEC → DISEÑO → IMPLEMENTACIÓN → VERIFICACIÓN
```

## Reglas que debes seguir

1. **Antes de implementar cualquier feature**, verifica que existe un archivo en `specs/` que lo describe.
2. Si no existe la spec, **no escribas código**. Crea la spec primero usando `specs/_template.md` como base.
3. Si la spec existe pero no tiene criterios de aceptación definidos, **completa la spec antes de implementar**.
4. Cada fase tiene un output claro:

| Fase | Output esperado |
|------|----------------|
| SPEC | Archivo en `specs/[nombre-feature].md` |
| DISEÑO | Sección de esquema/endpoints completada en la spec |
| IMPLEMENTACIÓN | Código en `src/` que cumple la spec |
| VERIFICACIÓN | Checklist de `harnesses/review.md` completo |

## Cómo empezar una sesión

Al iniciar, lee:
1. `Engram.md` — estado actual del proyecto
2. `harnesses/state.md` — dónde se quedó el trabajo
3. La spec del feature en curso (si hay uno activo)

## Cuándo usar este skill

Siempre. Está activo en todas las sesiones de trabajo en este proyecto.

## Escalado entre agentes

Ver `harnesses/isolation.md` para saber qué tareas corresponden a Pi vs agentes de implementación.
Si un agente de implementación no puede resolver algo en 2 intentos, escala a Pi.

---
name: sdd-spec
description: Creates measurable, risk-aware SDD specs and requires explicit approval before implementation
license: MIT
allowed-tools: ["read", "write"]
---

# SDD Spec Creation

## Preguntas obligatorias

- ¿Qué problema observable se resuelve?
- ¿Qué queda dentro y fuera del alcance?
- ¿Cómo se demostrará cada criterio?
- ¿Qué edge cases e invariantes existen?
- ¿Qué datos, permisos, contratos u operaciones sensibles se afectan?
- ¿Qué evidencia no puede automatizarse?

## Lista para aprobación

- [ ] ID estable.
- [ ] Alcance y fuera de scope.
- [ ] Al menos dos criterios identificados y medibles.
- [ ] Método de verificación por criterio.
- [ ] Edge cases e invariantes.
- [ ] Riesgos relevantes.

Crear desde `specs/_template.md`. El agente puede proponer la spec, pero no debe
marcarla aprobada sin una decisión explícita del usuario o autoridad definida
por el proyecto. El diseño se completa después de aprobar el comportamiento.

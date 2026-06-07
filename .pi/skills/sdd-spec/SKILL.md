---
name: sdd-spec
description: Guides the creation of SDD specs — asks the right questions before writing any code, creates spec files from the template, and validates completeness before moving to implementation
license: MIT
allowed-tools: ["read", "write"]
---

# SDD Spec Creation

Este skill guía la creación de specs antes de cualquier implementación. Una spec incompleta es peor que no tener spec — define el scope, los criterios de aceptación y los edge cases antes de tocar el código.

## Cómo crear una spec

1. Copia `specs/_template.md` y nómbralo `specs/[nombre-feature].md`
2. Responde estas preguntas en orden antes de llenar el template:

**Preguntas obligatorias:**
- ¿Qué problema resuelve este feature para el usuario?
- ¿Cómo sabes que está terminado? (criterios de aceptación medibles)
- ¿Qué pasa si el input está vacío o es inválido?
- ¿Qué pasa si falla la conexión o la DB?
- ¿Qué queda explícitamente fuera de scope?

3. Si no puedes responder alguna de estas preguntas, **no crees la spec todavía** — aclara primero con el usuario.

## Validación antes de pasar a DISEÑO

Una spec está lista para diseño cuando tiene:
- [ ] Descripción clara de qué se construye (no cómo)
- [ ] Al menos 2 criterios de aceptación medibles
- [ ] Al menos 1 edge case identificado
- [ ] Sección "Fuera de scope" completada

## Assets

El template de spec está en `assets/_template.md` y en `specs/_template.md`.

## Regla clave

**La spec describe comportamiento, no implementación.** No menciones lenguajes, librerías ni arquitectura en la spec — eso va en la fase de DISEÑO.

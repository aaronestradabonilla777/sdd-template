# AI Rules Harness

Reglas para Claude y OpenCode en cualquier proyecto que use este template.

## Reglas de código

- **No agregar features no especificadas** — si no está en la spec, no se implementa
- **No over-engineer** — tres líneas similares son mejores que una abstracción prematura
- **No comentar lo obvio** — solo comentar el *por qué*, nunca el *qué*
- **No manejar errores imposibles** — solo validar en los bordes del sistema (input del usuario, APIs externas)
- **No backwards-compatibility hacks** — si algo no se usa, se elimina
- **No `any`** — tipos explícitos siempre

## Reglas de respuesta (Claude)

- Respuestas cortas y directas — sin introducción ni resumen al final
- No explicar lo que ya es obvio en el código
- Si algo es ambiguo en la spec, preguntar antes de asumir
- Proponer una solución con el trade-off principal, no tres opciones

## Reglas de implementación (OpenCode)

- Leer la spec completa antes de escribir una línea
- Una tarea a la vez — no anticipar el siguiente paso
- Si algo no está claro después de 2 intentos, escalar a Claude
- No modificar archivos fuera del scope de la spec

## Reglas de commits

```
feat:  nueva funcionalidad
fix:   corrección de bug
chore: mantenimiento (deps, config)
docs:  documentación
test:  tests
refactor: cambio sin nueva funcionalidad ni fix
```

Mensaje en infinitivo, minúsculas, sin punto final.
Ejemplo: `feat: add user authentication endpoint`

## Reglas de PR

- PRs de más de 300 líneas cambiadas deben dividirse en partes más pequeñas
- Cada PR referencia la spec que implementa
- Sin descripción = no se aprueba

## Lo que nunca se hace

- Saltar la fase SPEC
- Escribir código sin criterios de aceptación definidos
- Hacer un PR sin descripción
- Pushear directo a `main`

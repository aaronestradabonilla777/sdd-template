# Phase Harness

## Flujo obligatorio (no saltar etapas)

```
SPEC → DISEÑO → IMPLEMENTACIÓN → VERIFICACIÓN
```

### SPEC (Claude)
- Qué se construye, no cómo
- Criterios de aceptación
- Edge cases
- Output: archivo en `specs/`

### DISEÑO (Claude)
- Esquema de datos
- Endpoints / componentes
- Dependencias
- Output: sección en la spec

### IMPLEMENTACIÓN (OpenCode)
- Lee la spec completa antes de escribir código
- Una tarea a la vez
- Output: código en `src/`

### VERIFICACIÓN (Claude + humano)
- ¿Cumple los criterios de aceptación?
- ¿Hay edge cases sin cubrir?
- Output: PR aprobado

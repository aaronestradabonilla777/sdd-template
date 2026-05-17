# Engram Memory Harness

## Propósito
Mantener contexto persistente entre sesiones de IA. Tanto Claude como OpenCode deben leer el Engram al inicio de cada sesión.

## Reglas
1. **Al iniciar sesión:** lee `Engram.md` antes de cualquier tarea
2. **Al tomar una decisión técnica importante:** actualiza `Engram.md`
3. **Al terminar una feature:** actualiza el status en `Engram.md`

## Qué va en el Engram
- Stack y puertos (no cambia casi nunca)
- Decisiones técnicas y por qué se tomaron
- Estado actual del proyecto por fase
- Convenciones del proyecto (naming, estructura)

## Qué NO va en el Engram
- Código (eso va en `src/`)
- Specs de features (eso va en `specs/`)
- Historial de bugs (eso va en commits)

## Prompt para iniciar sesión con OpenCode
```
Lee los siguientes archivos antes de empezar:
1. Engram.md — stack y decisiones del proyecto
2. CLAUDE.md — reglas y comandos
3. specs/[feature].md — la tarea a implementar

No escribas código hasta haber leído los tres.
```

## Prompt para iniciar sesión con Claude
```
Contexto del proyecto en Engram.md y CLAUDE.md.
La spec a trabajar está en specs/[feature].md.
```

# [Nombre del Proyecto] — Agent Context

## Proyecto

[Descripción breve del proyecto]

## Stack y comandos

| Área | Tecnología | Comando de verificación |
|---|---|---|
| Frontend | | |
| Backend | | |
| Database | | |

## Reglas obligatorias

- Aplicar `ANÁLISIS → SPEC → DISEÑO → IMPLEMENTACIÓN → RDD → VERIFICACIÓN → ENTREGA`.
- Ninguna implementación nueva sin una spec aprobada en `specs/`.
- Leer `harnesses/` antes de actuar y respetar reglas más específicas del repo.
- SDD define intención; RDD autoriza únicamente un candidato exacto.
- No fabricar recibos, resultados de comandos ni evidencia.
- El implementador no puede autodeclarar aprobado su candidato.
- Declarar comprobaciones omitidas, fallidas o inconclusas.
- Una modificación posterior a revisión requiere nueva autorización RDD.
- Un recibo no concede permiso implícito para commit, push, PR o deploy.

## Inicio de sesión

1. Leer `Engram.md`.
2. Leer `harnesses/state.md`.
3. Leer la spec activa y confirmar su aprobación.
4. Identificar fase actual, permisos y próximo paso seguro.

## Skills

- `sdd-phase`: aplica la máquina de fases.
- `sdd-spec`: crea y valida specs.

## Estructura

```text
specs/       intención y criterios
harnesses/   políticas ejecutables por agentes
docs/        ADRs y migraciones
Engram.md    memoria durable
```

# [Nombre del Proyecto] — Pi Context

> Este archivo es leído por Pi al inicio de cada sesión.
> Es el equivalente de `CLAUDE.md` para el harness Pi.

## Proyecto
[Descripción breve del proyecto]

## Stack
| Layer    | Tech | Puerto |
|----------|------|--------|
| Backend  |      |        |
| Database |      |        |
| Frontend |      |        |

## Reglas del proyecto
- **SDD obligatorio:** ningún código sin spec previa en `specs/`
- **Harnesses:** ver `harnesses/` para el flujo de trabajo
- **Pi:** diseña y revisa — implementación según el agente asignado en `harnesses/isolation.md`
- **No saltarse fases:** SPEC → DISEÑO → IMPLEMENTACIÓN → VERIFICACIÓN

## Flujo de trabajo
```
SPEC (Pi) → DISEÑO (Pi) → IMPLEMENTACIÓN (agente local) → VERIFICACIÓN (Pi)
```

## Comandos clave
```bash
# Agrega tus comandos aquí
```

## Estructura
```
specs/          ← fuente de verdad de cada feature
harnesses/      ← reglas de proceso
docs/           ← arquitectura y decisiones
Engram.md       ← memoria viva del proyecto
```

## Skills activas
- `sdd-phase` — enforza el flujo de fases obligatorio
- `sdd-spec` — guía de creación de specs

## Paquetes Pi instalados
Ver `.pi/settings.json` para la configuración completa.

# Structure Harness — Feature Sliced Design

Todo proyecto que use este template sigue **Feature Sliced Design** en el frontend y **Clean Architecture por módulos** en el backend.

Adapta los nombres de archivos al lenguaje del proyecto. Los principios son los mismos.

## Frontend

```
src/
  features/
    [nombre-feature]/
      [Nombre].{tsx,vue,svelte}   ← View (componente principal)
      [Nombre].viewModel.{ts,js}  ← ViewModel (lógica y estado)
      [Nombre].types.ts           ← tipos e interfaces
      [Nombre].permissions.ts     ← guards/permisos (si aplica)
  shared/
    components/                   ← UI reutilizable entre features
    hooks/                        ← lógica genérica reutilizable
    types/                        ← tipos globales
  pages/                          ← ensamblado de features por ruta
  App.{tsx,vue,svelte}
```

### Reglas
- Cada feature es **autocontenido** — no importa directamente desde otro feature
- Si dos features comparten algo → muévelo a `shared/`
- `viewModel` concentra toda la lógica — el componente solo renderiza

## Backend

```
src/
  [nombre-modulo]/
    handler.{rs,ts,py}   ← rutas HTTP (entrada/salida)
    service.{rs,ts,py}   ← lógica de negocio
    model.{rs,ts,py}     ← tipos y structs
    repo.{rs,ts,py}      ← queries a la DB (si el módulo es grande)
  main.{rs,ts,py}        ← router, DB connection, middlewares
```

### Reglas
- `handler` solo serializa/deserializa — sin lógica de negocio
- `service` no conoce HTTP — recibe datos, devuelve resultados
- `model` define tipos — sin lógica
- Si el módulo es simple, `service` y `repo` pueden omitirse

## Naming

| Elemento | Convención |
|----------|-----------|
| Feature folder | `kebab-case` |
| Componente | `PascalCase` |
| ViewModel | `camelCase.viewModel.ts` |
| Tipos | `PascalCase.types.ts` |
| Módulo backend | `snake_case` o `kebab-case` según el lenguaje |

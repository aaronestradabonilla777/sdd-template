# Structure Harness — Feature Sliced Design

Todo proyecto que use este template sigue **Feature Sliced Design** en el frontend y **Clean Architecture por módulos** en el backend.

## Frontend (React + TypeScript)

```
src/
  features/
    [nombre-feature]/
      [Nombre].tsx              ← View (componente principal)
      [Nombre].viewModel.ts     ← ViewModel (hook con lógica y estado)
      [Nombre].types.ts         ← tipos e interfaces del feature
      [Nombre].permissions.ts   ← guards/permisos (si aplica)
  shared/
    components/                 ← UI reutilizable entre features
    hooks/                      ← hooks genéricos
    types/                      ← tipos globales
  pages/                        ← ensamblado de features por ruta
  App.tsx
```

### Reglas
- Cada feature es **autocontenido** — no importa desde otro feature directamente
- Si dos features necesitan algo en común → muévelo a `shared/`
- `viewModel` = hook de React, nunca lógica en el componente

## Backend (Rust / Axum)

```
src/
  [nombre-modulo]/
    handler.rs    ← rutas HTTP (entrada/salida)
    service.rs    ← lógica de negocio
    model.rs      ← tipos y structs
    repo.rs       ← queries a la DB (si el módulo es grande)
    mod.rs        ← re-exports
  main.rs         ← router, DB connection, middlewares
```

### Reglas
- `handler.rs` solo serializa/deserializa — sin lógica de negocio
- `service.rs` no conoce HTTP — solo recibe datos, devuelve resultados
- `model.rs` define los tipos — sin lógica
- Si el módulo es simple, `service.rs` y `repo.rs` pueden omitirse

## Naming

| Elemento | Convención |
|----------|-----------|
| Feature folder | `kebab-case` |
| Componente | `PascalCase.tsx` |
| ViewModel | `camelCase.viewModel.ts` |
| Tipos | `PascalCase.types.ts` |
| Módulo Rust | `snake_case/` |

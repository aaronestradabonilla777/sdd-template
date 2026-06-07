# Spec: [Nombre del Feature]

## ¿Qué se construye?
Descripción en lenguaje simple de qué hace este feature.

## ¿Por qué?
Problema que resuelve o valor que aporta al usuario.

## Ubicación en el proyecto
- **Frontend:** `src/features/[nombre]/` (si aplica)
  - `[Nombre].{tsx,vue,svelte}` — vista
  - `[Nombre].viewModel.{ts,js}` — lógica y estado
  - `[Nombre].types.ts` — tipos e interfaces
- **Backend:** `src/[nombre]/` (si aplica)
  - `handler.{rs,ts,py,go}` — rutas HTTP
  - `service.{rs,ts,py,go}` — lógica de negocio
  - `model.{rs,ts,py,go}` — tipos y structs

## Dependencias
- Specs que deben completarse antes de esta:
  - [ ] 

## Criterios de aceptación
- [ ] 
- [ ] 
- [ ] 

## Edge cases
- 
- 

## Esquema de datos (si aplica)
```
-- Define aquí las entidades y sus campos
-- Usa el lenguaje de query de tu DB (SQL, SurrealQL, Prisma schema, etc.)
```

## Endpoints (si aplica)
| Método | Ruta | Descripción |
|--------|------|-------------|
| GET    |      |             |
| POST   |      |             |
| PUT    |      |             |
| PATCH  |      |             |
| DELETE |      |             |

## Componentes frontend (si aplica)
| Componente | Responsabilidad |
|------------|----------------|
|            |                |

## División de trabajo
| Tarea | Agente |
|-------|--------|
| Spec y criterios de aceptación | Pi / Claude |
| Diseño de tipos y esquema | Pi / Claude |
| Implementación de handlers/componentes | Agente local (OpenCode / otro) |
| Queries a la base de datos | Agente local (OpenCode / otro) |
| Verificación y revisión | Pi / Claude + humano |

## Fuera de scope
- Lo que explícitamente NO incluye este feature

## Definition of Done
- [ ] Compila sin errores ni warnings
- [ ] Criterios de aceptación cumplidos
- [ ] Edge cases cubiertos
- [ ] Tipos definidos (no `any`)
- [ ] PR con descripción del cambio

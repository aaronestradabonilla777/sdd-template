# Spec: [Nombre del Feature]

## ¿Qué se construye?
Descripción en lenguaje simple de qué hace este feature.

## ¿Por qué?
Problema que resuelve o valor que aporta al usuario.

## Ubicación en el proyecto
- **Feature slice:** `src/features/[nombre]/`
- **Archivos esperados:**
  - `[Nombre].tsx` — vista
  - `[Nombre].viewModel.ts` — lógica / hook
  - `[Nombre].types.ts` — tipos e interfaces
- **Backend:** `src/[nombre]/handler.rs`, `model.rs`, `service.rs` (si aplica)

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
```sql
-- SurrealDB
```

## Endpoints (si aplica)
| Método | Ruta | Descripción | Quién implementa |
|--------|------|-------------|-----------------|
| GET    |      |             | OpenCode        |
| POST   |      |             | OpenCode        |
| PUT    |      |             | OpenCode        |
| PATCH  |      |             | OpenCode        |
| DELETE |      |             | OpenCode        |

## Componentes frontend (si aplica)
| Componente | Responsabilidad |
|------------|----------------|
|            |                |

## División Claude / OpenCode
| Tarea | Quién |
|-------|-------|
| Spec y criterios de aceptación | Claude |
| Diseño de tipos y esquema | Claude |
| Implementación de handlers/componentes | OpenCode |
| Queries SurrealDB | OpenCode |
| Verificación y revisión | Claude |

## Fuera de scope
- Lo que explícitamente NO incluye este feature

## Definition of Done
- [ ] Compila sin errores ni warnings
- [ ] Criterios de aceptación cumplidos
- [ ] Edge cases cubiertos
- [ ] Tipos definidos (no `any`)
- [ ] PR con descripción del cambio

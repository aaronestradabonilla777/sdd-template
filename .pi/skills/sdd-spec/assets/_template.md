# Spec: [Nombre del Feature]

## ¿Qué se construye?
Descripción en lenguaje simple de qué hace este feature. Sin mencionar tecnologías.

## ¿Por qué?
Problema que resuelve o valor que aporta al usuario.

## Dependencias
- Specs que deben completarse antes de esta:
  - [ ] 

## Criterios de aceptación
- [ ] 
- [ ] 
- [ ] 

## Edge cases
- ¿Qué pasa si el input está vacío o es inválido?
- ¿Qué pasa si falla la conexión o la base de datos?
- ¿Qué pasa si el usuario no tiene permisos?

## Fuera de scope
- Lo que explícitamente NO incluye este feature

---
<!-- Las secciones siguientes se completan en la fase de DISEÑO -->

## Ubicación en el proyecto
- **Frontend:** `src/features/[nombre]/` (si aplica)
- **Backend:** `src/[nombre]/` (si aplica)

## Esquema de datos (si aplica)
```
-- define aquí las entidades y sus campos
-- sin SQL específico de ningún motor, o con el motor de tu proyecto
```

## Endpoints (si aplica)
| Método | Ruta | Descripción |
|--------|------|-------------|
| GET    |      |             |
| POST   |      |             |

## Componentes / pantallas (si aplica)
| Nombre | Responsabilidad |
|--------|----------------|
|        |                |

## División de trabajo
| Tarea | Agente |
|-------|--------|
| Spec y criterios | Pi / Claude |
| Diseño de tipos y esquema | Pi / Claude |
| Implementación | Agente local (OpenCode / otro) |
| Verificación | Pi / Claude + humano |

## Definition of Done
- [ ] Compila sin errores ni warnings
- [ ] Criterios de aceptación cumplidos
- [ ] Edge cases cubiertos
- [ ] Sin `any` ni tipos implícitos
- [ ] PR con descripción y link a esta spec

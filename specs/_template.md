# Spec: [Nombre del Feature]

**Tier:** 2 (Medium / Standard SDD)
**Fecha:** YYYY-MM-DD
**Estado:** DRAFT / EN REVISIÓN / EN IMPLEMENTACIÓN / COMPLETADA
**Orquestador (Tier 1 Pro):** [ID o Nombre del Agente]
**Trabajador (Tier 2/3 Fast/Local):** [ID o Nombre del Agente]
**Revisor (Tier 2 Fast):** [ID o Nombre del Agente]

---

## 1. ¿Qué se construye?
Descripción en lenguaje simple y directo de qué hace esta funcionalidad, qué capacidades añade al sistema y cómo interactúa con el usuario o con otros servicios.

## 2. ¿Por qué?
Problema concreto que resuelve, caso de uso justificado o valor de negocio que aporta. Alineado con el principio [ponytail](../harnesses/ai-rules.md): justificación estricta de por qué este código debe existir.

## 3. Criterios de Aceptación
Escenarios formales de comportamiento verificable (formato Given / When / Then):

- **AC-01: [Nombre o Escenario Principal]**
  - **Given** [Estado inicial o precondición del sistema]
  - **When** [Acción ejecutada por el usuario o cliente de API]
  - **Then** [Resultado esperado, mutación de estado o respuesta HTTP]

- **AC-02: [Nombre de Escenario Secundario / Validación]**
  - **Given** [Precondición del sistema con entrada inválida o estado no autorizado]
  - **When** [Se intenta ejecutar la acción]
  - **Then** [El sistema rechaza la operación con código y mensaje de error específico]

- **AC-03: [Nombre de Escenario Adicional]**
  - **Given** [...]
  - **When** [...]
  - **Then** [...]

## 4. Edge Cases y Condiciones Límite
- **Validación de entradas:** Manejo de datos nulos, strings vacíos, payloads excesivos o tipos incompatibles.
- **Concurrencia y estado:** Comportamiento ante doble click, peticiones simultáneas o desconexión de red.
- **Fronteras del sistema:** Respuestas ante caídas de dependencias externas o timeouts.

## 5. Ubicación y Arquitectura Técnica
- **Frontend:** `src/features/[nombre]/` (si aplica)
  - `[Nombre].tsx` — vista de interfaz de usuario
  - `[Nombre].viewModel.ts` — lógica de presentación y estado
  - `[Nombre].types.ts` — tipos de datos e interfaces públicas (prohibido `any`)
- **Backend:** `src/[nombre]/` (si aplica)
  - `handler.ts` (o `.rs`, `.py`, `.go`) — rutas y controladores HTTP
  - `service.ts` — lógica de negocio y casos de uso
  - `model.ts` — entidades de dominio y esquemas de persistencia

### Esquema de Datos (si aplica)
```sql
-- Define aquí las entidades y sus campos con tipos estrictos
-- Únicamente modificaciones aditivas no destructivas para Tier 2
CREATE TABLE IF NOT EXISTS [tabla] (
    id UUID PRIMARY KEY,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### Endpoints (si aplica)
| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| GET | `/api/[recurso]` | Listado o consulta de recurso | 200, 401, 500 |
| POST | `/api/[recurso]` | Creación de nuevo recurso | 201, 400, 401, 409 |
| PUT / PATCH | `/api/[recurso]/:id` | Modificación de recurso existente | 200, 400, 404 |
| DELETE | `/api/[recurso]/:id` | Eliminación segura de recurso | 204, 404 |

### Componentes Frontend (si aplica)
| Componente | Responsabilidad | Props / Contrato |
|---|---|---|
| `[NombreView]` | Renderizado de interfaz y binding de eventos | `viewModel: [NombreViewModel]` |

---

## 6. Desglose de Tareas de Implementación (Result Contracts)
Plan atómico de implementación quirúrgica asignado al Trabajador:

1. **Tarea 1 — Contratos y Tipos Base:**
   - **Archivos:** `src/features/[nombre]/types.ts` (o `model.ts`).
   - **Entregable:** Interfaces tipadas completas sin uso de `any`.
   - **Verificación:** Typecheck sin errores (`tsc --noEmit` o `cargo check`).

2. **Tarea 2 — Lógica de Servicio / Handlers:**
   - **Archivos:** `src/features/[nombre]/service.ts`, `src/api/[nombre].ts`.
   - **Entregable:** Implementación del caso de uso y endpoints respetando criterios AC-01 y AC-02.
   - **Verificación:** Tests unitarios asociados (`pnpm test` o `cargo test`).

3. **Tarea 3 — Interfaz de Usuario / Vistas (si aplica):**
   - **Archivos:** `src/features/[nombre]/[Nombre].tsx`.
   - **Entregable:** Componente visual integrado con diseño y tokens oficiales.
   - **Verificación:** Renderizado y pruebas de integración visual.

4. **Tarea 4 — Cobertura de Tests y Verificación:**
   - **Archivos:** `tests/features/[nombre].test.ts`.
   - **Entregable:** Suite de pruebas automatizadas que cubra AC-01..AC-03 y edge cases.
   - **Verificación:** Ejecución limpia con 100% PASS.

---

## 7. División de Trabajo por Roles Agnósticos

| Tarea / Fase | Rol Funcional | Nivel de Modelo | Responsabilidad Principal |
|---|---|---|---|
| Redacción de Spec y Criterios | **Orquestador** | Tier 1 (Pro) | Definición formal de ACs, arquitectura y contratos técnicos. |
| Modelado de Interfaces y Tipos | **Orquestador** | Tier 1 (Pro) | Diseño de esquemas de datos y contratos sin `any`. |
| Implementación de Código | **Trabajador** | Tier 2/3 (Fast / Local SLM) | Ejecución de tareas con límite de 2 intentos (Circuit Breaker). |
| Creación de Pruebas Unitarias | **Trabajador** | Tier 2/3 (Fast / Local SLM) | Cobertura de criterios de aceptación y edge cases. |
| Revisión de PR y Calidad | **Revisor** | Tier 2 (Fast) | Validación de límite ≤300 LOC, contratos y principio ponytail. |
| Sign-Off de Fase N (Closing) | **Orquestador** | Tier 1 (Pro) | Validación final de PVS, actualización de [Engram.md](../Engram.md) y cierre. |

---

## 8. Fuera de Scope (Out of Scope)
- Lo que explícitamente **NO** incluye esta especificación para evitar sobre-ingeniería (YAGNI).
- Funcionalidades futuras que corresponden a specs posteriores.

---

## 9. Definition of Done & Cierre de Fase N
Checklist obligatorio según [harnesses/closing.md](../harnesses/closing.md):

- [ ] **Clean Build**: Compila sin errores ni warnings en el entorno oficial.
- [ ] **100% Tests Pass**: Todos los Criterios de Aceptación (AC-01..AC-03) verificados con tests automatizados.
- [ ] **Edge cases cubiertos**: Casos límite y fallos de validación comprobados con tests.
- [ ] **Tipado estricto**: Cero uso de `any` o evasiones de tipado.
- [ ] **Límite de PR respetado**: Menos de 300 líneas netas cambiadas (≤300 LOC).
- [ ] **Principio Ponytail**: Cero código muerto, cero librerías innecesarias añadidas.
- [ ] **Sincronización de Memoria**: [Engram.md](../Engram.md) actualizado respetando el presupuesto de 80 a 150 líneas.
- [ ] **Spec marcada como COMPLETADA**: Estado del encabezado actualizado con fecha y revisor.
- [ ] **Changelog**: Entrada registrada en `CHANGELOG.md` siguiendo el formato *Keep a Changelog*.

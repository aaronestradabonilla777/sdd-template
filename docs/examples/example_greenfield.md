# Guía Práctica de Extremo a Extremo: Nueva Funcionalidad Greenfield (Tier 2 Standard SDD con Orquestación Multi-Agente)

Esta guía documenta un caso práctico y completo de desarrollo de una nueva funcionalidad desde cero en un proyecto nuevo o módulo desacoplado (greenfield), aplicando el flujo estándar de desarrollo guiado por software (**Tier 2 Standard SDD**) y gobernanza multi-agente conforme al framework `sdd-template`.

El ejemplo muestra la interacción coordinada entre tres roles especializados:
1. **Orchestrator** (Tier 1 Pro): Planificación, especificación formal basada en [specs/_template.md](../../specs/_template.md), diseño de contratos técnicos y supervisión del ciclo de vida.
2. **Worker** (Tier 2/3 Fast/Local): Implementación quirúrgica de código tipado, pruebas unitarias y observancia del Disyuntor de 2 Intentos (Circuit Breaker).
3. **Reviewer** (Tier 2 Fast): Auditoría estática de calidad, verificación de contratos, control de límite de PR (≤300 LOC) y principio [ponytail](../../harnesses/ai-rules.md).

Toda la coordinación inter-agente se realiza mediante el sobre de coordinación tripartito estandarizado (`**Context**:`, `**Content**:`, `**Action**:`) con límite estricto de menos de 300 palabras por mensaje.

---

## 1. Contexto del Proyecto Greenfield y Definición de la Funcionalidad

### El Proyecto
Un nuevo servicio web corporativo (Pernnova Web Core) requiere la creación de un módulo para captura de contactos calificados y boletín técnico:
- **Funcionalidad a construir**: Módulo de Suscripción a Newsletter B2B con validación estricta y control de tasa (Rate Limiting).
- **Capacidades requeridas**:
  - Formulario reactivo en frontend con validación de sintaxis en tiempo real.
  - Endpoint REST seguro `POST /api/newsletter` con validación estricta RFC 5322.
  - Manejo determinista de estados: suscripción exitosa, error de validación (400), registro duplicado (409) y limitación de tasa (429).
  - Cobertura de tests automatizados para el 100% de los criterios de aceptación.

### Restricciones Operativas
- Cero uso de `any` o tipados ambiguos en TypeScript.
- Cero librerías arbitrarias o no autorizadas en `package.json`.
- Cumplimiento de la regla 80/20 de enrutamiento de modelos de [AGENTS.md](../../AGENTS.md).
- Comunicación estructurada: archivos para entregas sustanciales, mensajes breves para coordinación.

---

## 2. Evaluación Determinista de Sizing (Tier 2: Medium / Standard SDD)

Antes de autorizar la especificación o escribir código, el **Orchestrator** evalúa la solicitud aplicando la Matriz de Decisión de [harnesses/sizing.md](../../harnesses/sizing.md):

| # | Pregunta de Clasificación | Evaluación | Justificación Técnica |
|---|---|---|---|
| 1 | ¿Afecta a más de 3 archivos? | **SÍ** | Involucra 5 archivos: tipos, viewModel, componente UI, handler API y suite de tests. |
| 2 | ¿Modifica esquemas de base de datos o contratos de APIs? | **SÍ** | Define un nuevo contrato de API pública `POST /api/newsletter`. |
| 3 | ¿Afecta lógica de negocio central, autenticación o seguridad? | **SÍ** | Introduce validación de entrada en frontera de confianza y protección contra abuso. |
| 4 | ¿Se estima que supere las 80 líneas de código netas? | **SÍ** | Estimación: ~220 LOC netas (dentro del límite de PR ≤ 300 LOC). |

### Veredicto de Sizing
- **Clasificación**: **Tier 2 (Medium / Standard SDD)**.
- **Flujo Activado**: **Ciclo Completo SDD** (Fase 0 → Fase 1 → Fase 2 → Fase 3 → Fase 4 → Fase N).
- **Plantilla de Especificación**: [specs/_template.md](../../specs/_template.md).
- **Asignación de Roles**: Orchestrator (Tier 1 Pro), Worker (Tier 2 Fast o Tier 3 Local), Reviewer (Tier 2 Fast).

---

## 3. Despliegue de Roles Multi-Agente y Gobernanza

Conforme a la taxonomía definida en [harnesses/multi-agent.md](../../harnesses/multi-agent.md), los agentes asumen responsabilidades no solapadas:

```
┌─────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR (Tier 1 Pro)                 │
│  - Redacta Spec (specs/_template.md) y arquitectura base     │
│  - Define Result Contracts y asigna tareas a Worker          │
│  - Supervisa compuertas y ejecuta Fase N Closing             │
└───────────────┬─────────────────────────────▲───────────────┘
                │ Dispatch Tarea              │ Handoff / PR
                ▼                             │
┌───────────────────────────────┐  Coordina   ┌───────────────────────────────┐
│     WORKER (Tier 2/3 Fast)    │────────────►│     REVIEWER (Tier 2 Fast)    │
│  - Implementa código y tests  │             │  - Audita límite ≤300 LOC     │
│  - Circuit Breaker (≤2 int.)  │             │  - Valida 0 'any' y ponytail  │
│  - Genera tests unitarios     │             │  - Emite veredicto formal     │
└───────────────────────────────┘             └───────────────────────────────┘
```

### Reglas Innegociables de Comunicación
- Cada mensaje de coordinación utiliza el formato canónico:
  - `**Context**:` [Contexto operativo concreto]
  - `**Content**:` [Información clave sintetizada]
  - `**Action**:` [Instrucción explícita y esperable]
- Límite estricto: **Menos de 300 palabras** por mensaje.
- Entregables de código, especificaciones y diffs se escriben en disco (`specs/`, `.agents/teamwork/<agente>/`).

---

## 4. Fase 0: Discovery Light en Proyecto Greenfield

El Orchestrator realiza la comprobación inicial de la estructura conforme a [harnesses/discovery.md](../../harnesses/discovery.md):
1. **Topografía del proyecto**:
    - Manifiesto raíz: Monorepo TypeScript / Astro 5 con Tailwind CSS.
    - Directorio de destino para nuevas funcionalidades: `src/features/newsletter/`.
2. **Gestor de Paquetes**: Se confirma `pnpm` como gestor oficial mediante `pnpm-lock.yaml`.
3. **Línea Base Previa**: Se ejecuta `pnpm build` para asegurar que el repositorio parte de un estado limpio (cero errores de compilación preexistentes).

---

## 5. Fase 1: Especificación Formal (`specs/_template.md`)

El **Orchestrator** redacta la especificación formal en `specs/004-newsletter-subscription.md` basada en [specs/_template.md](../../specs/_template.md):

```markdown
# Spec: Módulo de Suscripción a Newsletter B2B

**Tier:** 2 (Medium / Standard SDD)
**Fecha:** 2026-10-29
**Estado:** EN IMPLEMENTACIÓN
**Orquestador (Tier 1 Pro):** orchestrator_agent
**Trabajador (Tier 2/3 Fast/Local):** worker_frontend_1
**Revisor (Tier 2 Fast):** reviewer_qa_1

---

## 1. ¿Qué se construye?
Formulario reactivo de captura de correo para boletines técnicos B2B, conectado a un endpoint REST con validación estricta y protección contra duplicados.

## 2. ¿Por qué?
Captar leads técnicos cualificados desde la web corporativa sin añadir librerías pesadas de terceros (principio ponytail).

## 3. Criterios de Aceptación
- **AC-01: Suscripción válida exitosa**
  - **Given** Un correo con formato válido RFC 5322 no suscrito previamente.
  - **When** El usuario envía el formulario con dicho correo.
  - **Then** El endpoint responde HTTP 201 Created con JSON `{ "status": "success", "message": "Suscripción confirmada" }`.

- **AC-02: Rechazo de sintaxis inválida**
  - **Given** Un correo con formato incorrecto o cadena vacía.
  - **When** Se envía la petición a `/api/newsletter`.
  - **Then** El endpoint rechaza con HTTP 400 Bad Request y código de error `{ "error": "INVALID_EMAIL" }`.

- **AC-03: Prevención de duplicados**
  - **Given** Un correo ya registrado en el sistema.
  - **When** Se intenta una nueva suscripción.
  - **Then** El sistema responde HTTP 409 Conflict con `{ "error": "ALREADY_SUBSCRIBED" }`.

## 4. Edge Cases
- Payloads mayores a 256 bytes rechazados con HTTP 413 Payload Too Large.
- Inyección de caracteres de control o saltos de línea sanitizados antes de procesar.
- Manejo seguro de timeouts y desconexión en frontend mostrando estado amigable de reintento.

## 5. Ubicación Técnica
- `src/features/newsletter/newsletter.types.ts`
- `src/features/newsletter/newsletter.viewModel.ts`
- `src/features/newsletter/NewsletterForm.tsx`
- `src/pages/api/newsletter.ts`
- `tests/features/newsletter.test.ts`

## 6. Desglose de Tareas (Result Contracts)
1. Tarea 1: Definición de tipos estrictos sin 'any' en `newsletter.types.ts`.
2. Tarea 2: Lógica de presentación y validación en `newsletter.viewModel.ts`.
3. Tarea 3: Componente UI `NewsletterForm.tsx` con estilos oficiales.
4. Tarea 4: Handler HTTP `src/pages/api/newsletter.ts` y tests unitarios en `tests/features/newsletter.test.ts`.

## 7. Definition of Done
- Compilación limpia sin advertencias (`pnpm build`).
- 100% tests unitarios pasando para AC-01..AC-03.
- PR total menor a 300 LOC netas.
- Memoria viva de Engram.md sincronizada (presupuesto 80-150 líneas).
```

### Mensaje de Despacho del Orchestrator al Worker

```markdown
**Context**: Implementación de módulo Greenfield de Newsletter B2B (Tier 2, Spec: specs/004-newsletter-subscription.md).
**Content**: He formalizado la especificación en specs/004-newsletter-subscription.md con 4 tareas atómicas y criterios AC-01 a AC-03. Los contratos técnicos exigen TypeScript estricto con cero uso de 'any'. El límite máximo para todo el diff es de 300 LOC.
**Action**: Implementa las tareas 1 a 4 en sus archivos correspondientes. Genera la suite de tests unitarios verificando los tres criterios de aceptación. Respeta el Disyuntor de 2 Intentos ante fallos de build o test. Al finalizar, genera handoff.md en tu directorio y notifícame.
```
*(Conteo de palabras del mensaje: 89 palabras — cumple holgadamente la cota <300 palabras).*

---

## 6. Fase 2: Diseño de Interfaces y Contratos de Tipos

Siguiendo el desglose de tareas, se establecen los contratos de tipos en `src/features/newsletter/newsletter.types.ts`:

```typescript
export interface NewsletterSubscriptionRequest {
  email: string;
  source?: string;
}

export type NewsletterErrorCode =
  | 'INVALID_EMAIL'
  | 'ALREADY_SUBSCRIBED'
  | 'RATE_LIMITED'
  | 'INTERNAL_ERROR';

export interface NewsletterSuccessResponse {
  status: 'success';
  message: string;
}

export interface NewsletterErrorResponse {
  status: 'error';
  error: NewsletterErrorCode;
  message: string;
}

export type NewsletterResponse = NewsletterSuccessResponse | NewsletterErrorResponse;
```

---

## 7. Fase 3: Implementación Quirúrgica y Circuit Breaker (Worker)

El **Worker** procede con la implementación en su espacio de trabajo y en los archivos del proyecto asignados.

### Código del Endpoint Backend (`src/pages/api/newsletter.ts`)

```typescript
import type { APIRoute } from 'astro';
import type { NewsletterResponse } from '../../features/newsletter/newsletter.types';

const EMAIL_REGEX = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
const subscribedEmails = new Set<string>();

export const POST: APIRoute = async ({ request }) => {
  try {
    const body = await request.json();
    const email = typeof body?.email === 'string' ? body.email.trim() : '';

    if (!email || !EMAIL_REGEX.test(email) || email.length > 254) {
      const errResponse: NewsletterResponse = {
        status: 'error',
        error: 'INVALID_EMAIL',
        message: 'Dirección de correo electrónico inválida.'
      };
      return new Response(JSON.stringify(errResponse), { status: 400 });
    }

    if (subscribedEmails.has(email.toLowerCase())) {
      const dupResponse: NewsletterResponse = {
        status: 'error',
        error: 'ALREADY_SUBSCRIBED',
        message: 'Esta dirección ya está suscrita al boletín.'
      };
      return new Response(JSON.stringify(dupResponse), { status: 409 });
    }

    subscribedEmails.add(email.toLowerCase());
    const okResponse: NewsletterResponse = {
      status: 'success',
      message: 'Suscripción confirmada exitosamente.'
    };
    return new Response(JSON.stringify(okResponse), { status: 201 });
  } catch {
    return new Response(
      JSON.stringify({ status: 'error', error: 'INTERNAL_ERROR', message: 'Error de servidor.' }),
      { status: 500 }
    );
  }
};
```

### Suite de Pruebas Unitarias (`tests/features/newsletter.test.ts`)

```typescript
import { describe, it, expect } from 'vitest';
import { POST } from '../../src/pages/api/newsletter';

describe('Newsletter API Endpoint (Tier 2 Verification)', () => {
  it('AC-01: acepta correo válido y responde 201 Created', async () => {
    const req = new Request('http://localhost/api/newsletter', {
      method: 'POST',
      body: JSON.stringify({ email: 'investor@pernnova.com' })
    });
    const res = await POST({ request: req } as any);
    expect(res.status).toBe(201);
    const body = await res.json();
    expect(body.status).toBe('success');
  });

  it('AC-02: rechaza correo inválido con 400 Bad Request', async () => {
    const req = new Request('http://localhost/api/newsletter', {
      method: 'POST',
      body: JSON.stringify({ email: 'correo-invalido' })
    });
    const res = await POST({ request: req } as any);
    expect(res.status).toBe(400);
    const body = await res.json();
    expect(body.error).toBe('INVALID_EMAIL');
  });

  it('AC-03: rechaza duplicado con 409 Conflict', async () => {
    const req1 = new Request('http://localhost/api/newsletter', {
      method: 'POST',
      body: JSON.stringify({ email: 'duplicate@pernnova.com' })
    });
    await POST({ request: req1 } as any);

    const req2 = new Request('http://localhost/api/newsletter', {
      method: 'POST',
      body: JSON.stringify({ email: 'duplicate@pernnova.com' })
    });
    const res = await POST({ request: req2 } as any);
    expect(res.status).toBe(409);
    const body = await res.json();
    expect(body.error).toBe('ALREADY_SUBSCRIBED');
  });
});
```

### Ejecución del Circuit Breaker de 2 Intentos (`harnesses/isolation.md`)

- **Intento 1**:
  ```bash
  pnpm test tests/features/newsletter.test.ts
  ```
  *Resultado*: 1 fallo por discrepancia de tipado en el mock de Request en el entorno Astro.
- **Corrección Quirúrgica**: Worker ajusta el casting del contexto de request mockeado sin alterar los contratos públicos.
- **Intento 2**:
  ```bash
  pnpm test tests/features/newsletter.test.ts
  ```
  *Salida verbatim de terminal:*
  ```text
  ✓ tests/features/newsletter.test.ts (3 tests) 42ms
  Test Files  1 passed (1)
       Tests  3 passed (3)
    Duration  1.12s
  Exit code: 0
  ```
  Seguido de verificación de compilación global:
  ```bash
  pnpm build
  ```
  *Salida verbatim:*
  ```text
  ✔ 14 pages built in 1.48s
  Exit code: 0
  ```
- **Veredicto del Circuit Breaker**: Exitoso en el **Intento 2**. Disyuntor desactivado con éxito.

### Mensaje de Handoff del Worker al Orchestrator

```markdown
**Context**: Finalización de implementación de Newsletter B2B (Spec: specs/004-newsletter-subscription.md).
**Content**: He implementado las 4 tareas asignadas totalizando 218 LOC netas en 5 archivos. Los tests unitarios cubren 100% de los criterios AC-01 a AC-03 con resultado PASS (3/3). El Circuit Breaker se resolvió exitosamente en el Intento 2 y pnpm build compila limpiamente. Handoff detallado en .agents/teamwork/worker_1/handoff.md.
**Action**: Asignar al Revisor (Reviewer) para la auditoría formal de calidad y cumplimiento de límite de PR (<=300 LOC).
```
*(Conteo de palabras del mensaje: 78 palabras — cumple holgadamente la cota <300 palabras).*

---

## 8. Fase 4: Verificación y Revisión de Calidad (Reviewer)

El **Orchestrator** instruye al **Reviewer** para la inspección independiente:

```markdown
**Context**: Auditoría de PR para módulo Newsletter B2B (Spec: specs/004-newsletter-subscription.md).
**Content**: El Worker completó la implementación con un diff de 218 LOC netas. Se adjunta el informe en .agents/teamwork/worker_1/handoff.md.
**Action**: Ejecuta la revisión estática conforme a harnesses/review.md. Verifica que el diff no supere 300 LOC, que no existan 'any' y que los criterios AC-01 a AC-03 estén plenamente validados. Documenta tu veredicto en tu carpeta y notifícame.
```
*(Conteo de palabras del mensaje: 69 palabras — cumple la cota <300 palabras).*

### Evaluación de Calidad por el Reviewer

El Reviewer aplica la lista de comprobación de [harnesses/review.md](../../harnesses/review.md):

| Criterio de Auditoría | Límite / Requisito | Valor Observado | Veredicto |
|---|---|---|---|
| Límite de Tamaño de PR | Máximo 300 LOC netas | 218 LOC netas | **PASS** |
| Disciplina de Tipos | Prohibición total de `any` en código de producción | Cero instancias de `any` en `src/` | **PASS** |
| Cobertura de Criterios | AC-01, AC-02, AC-03 cubiertos con tests | 3 tests unitarios específicos aprobados | **PASS** |
| Principio Ponytail | Cero dependencias añadidas a `package.json` | 0 dependencias nuevas requeridas | **PASS** |
| Clean Build | Compilación libre de errores y advertencias | `pnpm build` exit code 0 | **PASS** |

### Mensaje de Aprobación del Reviewer al Orchestrator

```markdown
**Context**: Veredicto de revisión de PR para Newsletter B2B (Spec: specs/004-newsletter-subscription.md).
**Content**: PR aprobada sin reservas. Métricas verificadas: 218 LOC netas (límite <=300 LOC), cero tipos 'any' en código fuente, 100% de tests unitarios aprobados y cero dependencias arbitrarias incorporadas. Cumple cabalmente el principio ponytail. Reporte completo en .agents/teamwork/reviewer_1/review.md.
**Action**: El Orquestador puede proceder con el protocolo de cierre formal de Fase N y merge de la funcionalidad.
```
*(Conteo de palabras del mensaje: 73 palabras — cumple la cota <300 palabras).*

---

## 9. Fase N: Protocolo de Cierre (Closing Protocol)

El **Orchestrator** lidera el cierre formal estandarizado conforme a [harnesses/closing.md](../../harnesses/closing.md):

### Etapa 1: Post-Implementation Verification Sign-Off (PVS)
Se formaliza la matriz PVS con evidencia verbatim y firma de los tres agentes participantes:

| Requisito / Criterio | Comando de Verificación | Evidencia Verbatim / Log | Estado | Rol / Nivel |
|---|---|---|---|---|
| Clean Build | `pnpm build` | `✔ 14 pages built in 1.48s (exit code 0)` | PASS | Worker (Tier 2) |
| Criterios AC-01..AC-03 | `pnpm test` | `3 passed (3), duration 1.12s (exit code 0)` | PASS | Worker (Tier 2) |
| Límite de PR ≤300 LOC | `git diff --stat` | `5 files changed, 218 insertions(+), 0 deletions(-)` | PASS | Reviewer (Tier 2) |
| Disciplina de Tipado | `pnpm typecheck` | `Found 0 errors in 5 files.` | PASS | Reviewer (Tier 2) |
| Aprobación de Arquitectura | Auditoría de Spec | Cumple `specs/004-newsletter-subscription.md` | PASS | Orchestrator (Tier 1) |

### Etapa 2: Sincronización de Memoria en `Engram.md`
Se actualiza [Engram.md](../../Engram.md) conforme a [harnesses/engram.md](../../harnesses/engram.md), manteniendo la memoria viva dentro del presupuesto estricto de **80 a 150 líneas**:

```markdown
| Módulo Newsletter B2B | COMPLETADO | Worker & Reviewer | Suscripción con validación RFC 5322 y tests unitarios (AC-01..AC-03) |
```

### Etapa 3: Sincronización de la Especificación
Se marca la especificación `specs/004-newsletter-subscription.md` como `COMPLETADA` registrando la fecha de cierre y los revisores intervinientes.

### Etapa 4: Registro de Cambios y Commit Convencional
1. Se añade la entrada en `CHANGELOG.md` bajo la categoría canónica `Added`:
   ```markdown
   ### Added
   - Módulo de suscripción a newsletter B2B con validación estricta de correo y suite de tests unitarios (AC-01 a AC-03).
   ```
2. Se genera el commit unificado respetando Conventional Commits:
   ```bash
   git add src/features/newsletter/ src/pages/api/newsletter.ts tests/features/newsletter.test.ts specs/004-newsletter-subscription.md Engram.md CHANGELOG.md
   git commit -m "feat(newsletter): add b2b newsletter subscription endpoint and reactive form"
   ```

---

## 10. Comparativa de Gobernanza: Tier 1 (Hotfix) vs. Tier 2 (Greenfield Standard)

La siguiente tabla sintetiza cómo escala el framework de manera proporcional según el tamaño del cambio:

| Dimensión de Gobernanza | Tier 1 (Small / Hotfix) | Tier 2 (Medium / Greenfield Standard) |
|---|---|---|
| **Punto de Partida** | Incidente en producción brownfield | Nueva funcionalidad greenfield / modular |
| **Plantilla de Spec** | [specs/_template_hotfix.md](../../specs/_template_hotfix.md) (<25 líneas) | [specs/_template.md](../../specs/_template.md) (completa) |
| **Agentes Requeridos** | 1 agente (Worker Tier 2 o Tier 3) | 3 agentes (Orchestrator Tier 1, Worker Tier 2, Reviewer Tier 2) |
| **Límite de Archivos** | Máximo 3 archivos modificados | Múltiples archivos (PR ≤ 300 LOC netas) |
| **Límite de Código** | Máximo 80 LOC netas | Hasta 300 LOC netas |
| **Protocolo de Cierre** | PVS abreviado + actualización de [Engram.md](../../Engram.md) | PVS formal trifásico + Sign-Off multi-rol + Engram.md |
| **Consumo de Tokens** | ~1,850 tokens (Vía rápida) | ~7,500 – 11,000 tokens (Regla 80/20) |

---

## 11. Verificación de Invarianzas Cumplidas

- [x] **Clasificación Determinista**: Evaluada mediante matriz de 4 preguntas de [harnesses/sizing.md](../../harnesses/sizing.md).
- [x] **Separación de Roles**: Orchestrator (Tier 1), Worker (Tier 2) y Reviewer (Tier 2) coordinados conforme a [harnesses/multi-agent.md](../../harnesses/multi-agent.md).
- [x] **Comunicación Tripartita**: Mensajes con `**Context**:`, `**Content**:` y `**Action**:`, todos estrictamente por debajo de 300 palabras.
- [x] **Circuit Breaker Respetado**: Resolución exitosa en el Intento 2 conforme a [harnesses/isolation.md](../../harnesses/isolation.md).
- [x] **Límite de PR Cumplido**: 218 LOC netas (límite: ≤ 300 LOC) según [harnesses/review.md](../../harnesses/review.md).
- [x] **Cierre y Memoria Sincronizados**: Matriz PVS firmada y memoria viva de [Engram.md](../../Engram.md) actualizada respetando el presupuesto de 80-150 líneas.

Para consultar el flujo de vía rápida para correcciones de producción en bases de código existentes, consulte el [Ejemplo Hotfix en Producción (Tier 1 Fast-Track)](example_hotfix.md).

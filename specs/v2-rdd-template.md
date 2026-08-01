---
id: SPEC-2026-001
status: approved
approved_by: repository-owner
approved_at: 2026-08-01
---

# Spec: SDD Template v2 con RDD

## ¿Qué se construye?

Una versión 2 agnóstica al agente que conserve SDD como obligatorio e incorpore
RDD como capa opcional de revisión y autorización del candidato exacto.

## Alcance

- Añadir un harness RDD con estados, autoridad, findings y revisión 4R.
- Ampliar el flujo a análisis, spec, diseño, implementación, RDD, verificación y
  entrega.
- Reemplazar asignaciones fijas Claude/OpenCode/Ollama por roles.
- Reemplazar tamaño como proxy de riesgo por superficies y evidencia.
- Añadir criterios identificables, invariantes, riesgos y métodos de
  verificación al template de specs.
- Actualizar skills, contextos, memoria, estado, README y migración.
- Mantener Gentle-AI como integración opcional, sin reimplementar sus recibos.

## Fuera de scope

- Modificar Gentle-AI.
- Instalar herramientas automáticamente.
- Fabricar recibos Markdown.
- Modificar o publicar DonaSF-Web.

## Criterios de aceptación y evidencia

| ID | Comportamiento observable | Método de verificación |
|---|---|---|
| AC-001 | SDD y RDD tienen responsabilidades distintas | Inspeccionar README y harnesses |
| AC-002 | El flujo contiene siete fases explícitas | Buscar la secuencia canónica |
| AC-003 | Ningún agente puede fabricar o autootorgarse aprobación | Buscar reglas de autoridad |
| AC-004 | La autorización queda ligada al candidato exacto | Inspeccionar `harnesses/rdd.md` |
| AC-005 | La validación del fix está definida como read-only | Inspeccionar phase/RDD |
| AC-006 | El riesgo no se deriva solo del tamaño | Inspeccionar review/RDD |
| AC-007 | El template no exige Ollama ni un agente específico | Buscar asignaciones obligatorias |
| AC-008 | Sin autoridad nativa se declara `disabled/unmanaged` | Buscar estados y fallback |
| AC-009 | Existe una guía de migración desde v1 | Validar enlace local desde README |
| AC-010 | JSON y enlaces Markdown locales son válidos | Parsear JSON y resolver enlaces |

## Riesgos e invariantes

- Un checklist manual nunca sustituye un recibo nativo.
- RDD no concede permisos de commit, push, PR, merge o deploy.
- Cambiar el candidato invalida la autoridad anterior para el contenido nuevo.
- La v2 debe seguir funcionando como SDD puro cuando RDD esté deshabilitado.

## Definition of Done

- [ ] Todos los criterios cuentan con evidencia.
- [ ] `git diff --check` no reporta errores.
- [ ] Los enlaces locales y JSON son válidos.
- [ ] El diff está limitado al repositorio `sdd-template`.

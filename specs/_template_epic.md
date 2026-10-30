# Epic Spec: [Nombre del Gran Proyecto o Refactor Mayor]

**Tier:** 3 (Large / Epic)
**Fecha:** YYYY-MM-DD
**Estado:** PROPUESTO / EN AUDITORÍA / EN EJECUCIÓN / COMPLETADO
**Orquestador (Tier 1 Pro):** [ID o Nombre del Agente]
**Adversarial Challenger (Tier 1 Pro):** [ID o Nombre del Agente]
**Forensic Auditor (Tier 1 Pro):** [ID o Nombre del Agente]

---

## 1. Visión y Objetivos Estratégicos
- **Objetivo Principal:** [Meta arquitectónica o de negocio del Epic]
- **Impacto en el Sistema:** [Módulos, subsistemas, dependencias y persistencia afectada]
- **Riesgos Principales:** [Seguridad, rendimiento, compatibilidad hacia atrás, migración de datos]

## 2. Auditoría Previa (Pre-Change Discovery Summary)
Grounded en la auditoría de 6 pilares de `harnesses/discovery.md` y documentada en `ARCH_AUDIT.md`:
- **Topografía y Runtime:** [Resumen de topografía, gestor de paquetes e invariantes]
- **Deuda Técnica y Puntos Críticos:** [Deuda técnica previa que interactúa con este Epic]
- **Invariantes Arquitectónicas:** [Reglas y contratos que deben preservarse sin alteración]

## 3. Desglose en Slices / Milestones (Tier 2 Desacoplados)
Cada slice debe ser un PR/Spec independiente de menos de 300 líneas (≤300 LOC netas por slice).
Las dependencias deben formar un grafo acíclico dirigido (DAG) sin dependencias circulares:

| Milestone | Spec ID | Descripción del Slice | Dependencias (DAG) | Líneas Estimadas | Estado |
|---|---|---|---|---|---|
| M1: Contratos & Schemas | `specs/00X-a-[nombre].md` | Tipos, contratos e interfaces base | Ninguna | < 250 LOC | PENDIENTE |
| M2: Core Logic & Servicios | `specs/00X-b-[nombre].md` | Implementación de servicios y lógica | M1 | < 280 LOC | PENDIENTE |
| M3: Integración & UI/APIs | `specs/00X-c-[nombre].md` | Handlers HTTP, endpoints o vistas UI | M2 | < 250 LOC | PENDIENTE |
| M4: Verificación E2E & Hardening | `specs/00X-d-[nombre].md` | Tests E2E y validación de regresión | M3 | < 200 LOC | PENDIENTE |

*Regla de descomposición:* Ningún slice puede exceder 300 líneas. Si un slice crece por encima de este límite, debe subdividirse antes de iniciar implementación.

## 4. Gate de Revisión Adversaria (Aprobación Obligatoria)
> **INVARIANTE DE BLOQUEO:** La implementación de código está estrictamente bloqueada hasta que este Gate de Revisión Adversaria cuente con la firma formal de aprobación del Challenger y Auditor.

El Challenger y el Forensic Auditor evalúan los riesgos de la arquitectura antes de escribir código:
- [ ] ¿El diseño evita introducir acoplamiento innecesario o dependencias circulares? (SÍ)
- [ ] ¿Los contratos de datos previenen inyecciones, fallos de tipado y pérdida de datos? (SÍ)
- [ ] ¿Los slices cumplen la regla de DAG con menos de 300 líneas cada uno? (SÍ)
- [ ] ¿Existe una estrategia de rollback ejecutable y probada ante fallos críticos? (SÍ)
- [ ] ¿El Forensic Auditor conserva su potestad de BINARY VETO sobre cualquier alteración de tests? (SÍ)

- **Estado del Gate:** `PENDIENTE / APROBADO / RECHAZADO`
- **Firma del Challenger (Tier 1):** `[APROBADO / RECHAZADO]` — *Fecha:* `YYYY-MM-DD`
- **Firma del Auditor (Tier 1):** `[APROBADO / RECHAZADO]` — *Fecha:* `YYYY-MM-DD`

## 5. Estrategia de Rollback
Pasos concretos e idempotentes para revertir el cambio sin pérdida de datos en caso de fallo crítico en producción o activación de BINARY VETO:
1. **Detección del Disparador:** Identificación de fallo crítico (ej. caída de servicio, corrupción de esquema, o veto de integridad).
2. **Procedimiento de Despliegue Inverso:** Instrucciones para revertir commits (`git revert`) o restaurar backups de datos.
3. **Plan de Contingencia de Datos:** Scripts de migración inversa (`down migrations`) sin pérdida ni truncamiento de datos existentes.
4. **Validación Post-Rollback:** Comandos para confirmar que el repositorio y el sistema regresan al baseline operativo previo.

## 6. Cierre Global del Epic
Checklist integral de cierre según `harnesses/closing.md`:
- [ ] Todos los Milestones (M1..Mn) completados con PVS firmado en cada sub-spec.
- [ ] Suite completa de pruebas E2E y suites de regresión aprobadas al 100%.
- [ ] Certificado de integridad emitido por el Forensic Auditor (cero tests adulterados, cero deps no autorizadas).
- [ ] Architectural Decision Record (ADR) registrado en `docs/` si hubo cambios arquitectónicos.
- [ ] Documentación global del sistema y guías de uso actualizadas.
- [ ] `Engram.md` consolidado y podado dentro del presupuesto estricto (80 a 150 líneas).
- [ ] Entradas de changelog consolidadas siguiendo Keep a Changelog.

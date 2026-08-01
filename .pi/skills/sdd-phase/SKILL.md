---
name: sdd-phase
description: Enforces ANALYSIS → SPEC → DESIGN → IMPLEMENTATION → RDD → VERIFICATION → DELIVERY and blocks implementation before spec approval
license: MIT
allowed-tools: ["read", "write", "bash"]
---

# SDD + RDD Phase Enforcement

## Flujo

```text
ANÁLISIS → SPEC → DISEÑO → IMPLEMENTACIÓN → RDD → VERIFICACIÓN → ENTREGA
```

## Reglas

1. Leer `Engram.md`, `harnesses/state.md` y la spec activa.
2. No implementar sin una spec explícitamente aprobada.
3. No confundir tests o narración con autorización RDD.
4. No fabricar recibos; usar la autoridad nativa o declarar
   `disabled/unmanaged`.
5. Si cambia un candidato revisado, volver a RDD antes de entrega.
6. Durante validación read-only, no escribir en el candidato.
7. Antes de entregar, validar permisos del repo y el recibo aplicable.

Consultar `harnesses/phase.md`, `harnesses/rdd.md` y
`harnesses/isolation.md` para los contratos completos.

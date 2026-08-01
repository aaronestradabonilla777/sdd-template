# Phase Harness

## Flujo obligatorio

```text
ANÁLISIS → SPEC → DISEÑO → IMPLEMENTACIÓN → RDD → VERIFICACIÓN → ENTREGA
```

No se omiten fases. RDD puede estar `disabled/unmanaged`, pero ese estado no
equivale a aprobación.

## ANÁLISIS

- Investigar contexto, restricciones y riesgos sin modificar el producto.
- Determinar si la solicitud es una feature nueva o mantenimiento cubierto por
  una spec existente.
- Output: alcance entendido o preguntas bloqueantes.

## SPEC

- Describir intención y comportamiento, no decisiones de implementación.
- Definir criterios identificables, edge cases, invariantes y fuera de scope.
- Obtener aprobación explícita antes de implementar.
- Output: `specs/[feature].md` aprobada.

## DISEÑO

- Definir arquitectura, contratos, componentes y dependencias.
- Vincular cada decisión con criterios de la spec.
- Output: diseño dentro de la spec o ADR cuando la decisión sea durable.

## IMPLEMENTACIÓN

- Leer spec y diseño completos antes de escribir código.
- Implementar una tarea acotada a la vez.
- Ejecutar tests y comprobaciones aplicables; declarar las omitidas.
- Output: candidato completo y verificable.

## RDD

- Congelar el candidato exacto antes de revisarlo.
- Derivar el nivel de revisión de riesgos y evidencia, nunca solo del tamaño.
- Mantener separados implementador, revisores y validador cuando el runtime lo
  permita.
- Permitir únicamente la corrección acotada autorizada por la transacción RDD.
- Validar la corrección en modo read-only.
- Output: recibo nativo ligado a la huella, escalación con evidencia,
  `inconclusive` o `disabled/unmanaged`.

## VERIFICACIÓN

- Contrastar de forma independiente el candidato contra spec, diseño y tareas.
- No modificar el candidato durante la verificación.
- Si se requieren cambios, volver a IMPLEMENTACIÓN; el contenido nuevo requiere
  una nueva autorización RDD.
- Output: evidencia de cumplimiento y riesgos residuales.

## ENTREGA

- Validar que el recibo gobierna el candidato actual cuando RDD esté activo.
- Aplicar permisos y políticas propios del repositorio.
- Nunca interpretar un recibo como permiso implícito para commit, push, PR,
  release o deploy.
- Output: entrega autorizada o bloqueo explícito.

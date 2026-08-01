# AI Rules Harness

## Implementación

- No agregar comportamiento fuera de la spec aprobada.
- No asumir cuando la ambigüedad cambia alcance, seguridad o arquitectura.
- Reutilizar patrones existentes antes de crear abstracciones.
- No modificar archivos ajenos al alcance sin justificarlo.
- Leer el contexto relevante antes de escribir.
- Mantener tipos, estilo, logging y comandos propios del repositorio.

## Evidencia

- No afirmar que un comando pasó si no se ejecutó y observó su salida.
- Diferenciar `passed`, `failed`, `not-run` e `inconclusive`.
- Un test verde solo demuestra las propiedades que alcanza.
- Un finding debe demostrar causalidad respecto del candidato.
- Un resumen o checklist escrito por un agente no es un recibo RDD.

## Autoridad

- No autodeclarar aprobado el trabajo propio.
- No modificar el candidato durante revisión o validación read-only.
- No reutilizar autoridad después de modificar el contenido.
- No interpretar RDD como permiso para commit, push, PR, merge o deploy.

## Entrega

- Referenciar la spec y la evidencia.
- Declarar riesgos residuales y comprobaciones omitidas.
- Aplicar las políticas específicas del repositorio y del usuario.

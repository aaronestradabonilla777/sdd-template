# Receipt-Driven Development Harness

## Propósito

RDD convierte la revisión en una transacción verificable sobre un candidato
inmutable. SDD define la intención; RDD decide si existe evidencia suficiente
para autorizar los bytes inspeccionados.

## Autoridad

- El agente no puede declarar aprobado su propio trabajo.
- Narraciones, checklists y archivos Markdown no son recibos nativos.
- Solo la autoridad RDD configurada puede emitir o validar un recibo.
- El recibo pertenece a la huella exacta del candidato revisado.
- Si cambia el contenido, la autorización anterior no gobierna el nuevo
  candidato.
- Un recibo nunca amplía los permisos establecidos por el repositorio o usuario.

## Estados

- `not-started`
- `candidate-frozen`
- `under-review`
- `approved`
- `escalated`
- `inconclusive`
- `disabled/unmanaged`

## Secuencia

1. Completar implementación y comprobaciones ordinarias.
2. Congelar el candidato mediante la autoridad nativa.
3. Clasificar riesgo usando evidencia y superficies afectadas.
4. Ejecutar los lentes requeridos por esa clasificación.
5. Registrar findings causados por el candidato con evidencia reproducible.
6. Si existen findings severos, permitir una sola corrección dentro del
   presupuesto congelado por la autoridad.
7. Validar la corrección en read-only y contra árboles inmutables.
8. Emitir un recibo exacto o escalar con evidencia.
9. Validar el mismo recibo en cada puerta de entrega aplicable.

## Findings

Cada finding debe contener:

- identificador estable;
- lente o categoría de riesgo;
- severidad justificada;
- ubicación concreta;
- condición causada por el candidato;
- evidencia o pasos reproducibles;
- impacto;
- condición observable para considerar válida la corrección.

`fail` sin evidencia no es un veredicto suficiente. Un problema preexistente no
debe atribuirse al candidato sin demostrar causalidad.

## Revisión 4R

Para riesgo alto, la revisión puede aplicar cuatro lentes independientes:

- **Risk:** seguridad, privacidad, datos, permisos y blast radius.
- **Readability:** claridad, mantenibilidad y coherencia con el repositorio.
- **Reliability:** comportamiento, errores, contratos, tests y observabilidad.
- **Resilience:** degradación, recuperación, concurrencia y dependencias.

El runtime RDD determina cuántos lentes corresponden. El número de archivos o
líneas no sustituye esa evaluación.

## Ausencia de autoridad nativa

Si Gentle-AI u otra autoridad compatible no está instalada o RDD fue
deshabilitado, registrar `disabled/unmanaged`. Continuar o no bajo la política
ordinaria corresponde al repositorio y al usuario. Está prohibido crear un
archivo que simule `approved`.

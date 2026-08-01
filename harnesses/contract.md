# Result and Evidence Contract Harness

## Contrato previo a implementación

Toda feature debe tener una spec aprobada con:

- criterios de aceptación identificables y observables;
- edge cases e invariantes;
- fuera de scope;
- riesgos conocidos;
- método de verificación para cada criterio.

## Matriz de evidencia

| Criterio | Método | Evidencia esperada | Resultado |
|---|---|---|---|
| AC-001 | test, comando o inspección | señal observable | pendiente |

La evidencia debe distinguir `passed`, `failed`, `not-run` e `inconclusive`.
Nunca presentar una comprobación omitida como exitosa.

## Definition of Done

- [ ] La spec fue aprobada antes de implementar.
- [ ] La implementación permanece dentro del alcance aprobado.
- [ ] Cada criterio tiene evidencia verificable.
- [ ] Build, lint, tests y comprobaciones aplicables fueron ejecutados.
- [ ] Las comprobaciones omitidas o inconclusas están declaradas.
- [ ] Los findings tienen causalidad y evidencia reproducible.
- [ ] RDD emitió un recibo para el candidato exacto, o su estado
      `disabled/unmanaged` fue declarado sin simular aprobación.
- [ ] La verificación SDD contrastó candidato y spec.
- [ ] Los riesgos residuales están documentados.
- [ ] La entrega cumple las políticas propias del repositorio.

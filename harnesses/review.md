# Review Harness

## Principio

El tamaño controla legibilidad y alcance; no determina autorización. Un cambio
de dos líneas en autenticación puede ser crítico y un diff generado de cientos
de líneas puede ser rutinario.

## Superficies de riesgo

- autenticación, autorización y permisos;
- credenciales, secretos y supply chain;
- privacidad y datos personales;
- pérdida, corrupción o migración de datos;
- pagos y operaciones irreversibles;
- concurrencia y consistencia;
- red, infraestructura y despliegue;
- compatibilidad de APIs y contratos públicos;
- observabilidad, recuperación y degradación;
- código muerto o rutas no alcanzables que invaliden evidencia.

## Revisión

- [ ] La spec y su aprobación son identificables.
- [ ] El candidato revisado está congelado por la autoridad RDD si está activa.
- [ ] Los criterios tienen evidencia observable.
- [ ] El diff no contiene trabajo fuera de scope.
- [ ] Las superficies de riesgo afectadas están declaradas.
- [ ] Los findings identifican causalidad, severidad e impacto.
- [ ] Las comprobaciones no ejecutadas están declaradas.
- [ ] El recibo, si existe, corresponde al contenido actual.
- [ ] La política del repositorio permite la acción de entrega solicitada.

## Tamaño

Dividir cambios grandes cuando sea posible sin romper coherencia, trazabilidad o
capacidad de prueba. No omitir revisión por ser pequeño ni dividir mecánicamente
para rebajar una clasificación de riesgo.

## Entrega

La descripción debe incluir la spec, alcance, método de prueba, evidencia y
riesgos residuales. La existencia de un recibo no autoriza por sí misma commit,
push, PR, merge, release o deploy.

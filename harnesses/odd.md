# Observability-Driven Development Harness

## Propósito y relación con SDD/RDD

ODD define qué señales operativas permiten entender un flujo en ejecución.
SDD expresa la intención y sus criterios; ODD determina qué conviene observar
cuando hay comportamiento operativo; RDD revisa evidencia del candidato exacto.
ODD no es una fase adicional ni una puerta universal de aprobación.

## Cuándo aplica

Completa los criterios ODD de la spec cuando exista una integración remota,
una operación crítica, un proceso asíncrono de varios pasos o un requisito
explícito de disponibilidad, latencia o soporte. Para trabajo local, prototipos
o mocks sin operación real, registra “No aplica” con una frase de contexto.

## Señales que puede definir una spec

Elige señales según preguntas concretas de soporte; no instrumentes por rutina:

- **Resultado:** éxito, rechazo, cancelación, timeout o clase de error.
- **Duración:** latencia observada en el límite de la operación y umbral aprobado
  por el propietario. No inventes SLOs.
- **Contexto:** operación y dependencia afectadas; método, ruta y estado HTTP
  solo cuando estén disponibles y sean seguros.
- **Correlación:** request/trace ID si existe un contrato acordado. Un ID local
  de diagnóstico no implica propagación al backend.
- **Diagnóstico y respuesta:** destino de consulta, responsable y acción segura
  ante degradación.

## Errores, recuperación y límites de operación

- Distingue fallas de red, timeout, validación, autorización, respuesta inválida
  y errores del servidor cuando haya evidencia para hacerlo.
- Expón a la persona un mensaje seguro y accionable; conserva detalles técnicos
  solo en el canal de diagnóstico aprobado.
- Define reintentos solo con garantías de idempotencia. No reintentes escrituras
  financieras o no idempotentes por defecto.
- No uses capturas vacías ni logs sueltos como estrategia de manejo de errores.
- El frontend describe lo que observó; no afirma causa raíz ni salud interna del
  backend. Health checks, métricas de servidor y alertas de infraestructura
  pertenecen al equipo que opera esos servicios.

## Privacidad y dependencias

- No registres credenciales, tokens, secretos, cookies, datos personales o
  financieros ni cuerpos completos de petición/respuesta por defecto.
- Minimiza y redacta campos sensibles antes de emitir señales.
- No agregues proveedor, SDK, colector, endpoint ni dependencia sin decisión
  arquitectónica explícita y aprobación del propietario.
- Documenta acceso, retención y destino cuando una señal salga del proceso.

## Verificación de cierre

Cuando ODD aplique, comprueba que las señales responden a una pregunta
operativa, que los fallos tienen una respuesta definida, que la correlación
coincide con el contrato y que no se exponen datos sensibles. RDD puede revisar
la evidencia de estas decisiones, pero no sustituye el diseño ODD.

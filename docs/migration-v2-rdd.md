# Migración de v1 a v2 RDD

## Cambios incompatibles de proceso

- El flujo pasa de cuatro a siete fases explícitas.
- Claude/OpenCode/Ollama dejan de ser asignaciones obligatorias.
- Los roles se asignan por responsabilidad y capacidad.
- El tamaño del diff deja de decidir el nivel de revisión.
- La verificación exige una matriz de evidencia.
- `approved` solo puede provenir de una autoridad RDD nativa.

## Migración

1. Conserva reglas específicas de tu proyecto antes de copiar archivos.
2. Fusiona `AGENTS.md` o `CLAUDE.md`; no los reemplaces ciegamente.
3. Añade `harnesses/rdd.md`.
4. Actualiza `phase.md`, `contract.md`, `review.md` e `isolation.md`.
5. Migra specs activas añadiendo IDs, métodos de verificación, invariantes y
   riesgos. No reescribas specs históricas solo por formato.
6. Decide si RDD estará habilitado. Sin runtime compatible, utiliza
   `disabled/unmanaged`.
7. Prueba el flujo en un cambio desechable antes de proteger puertas de entrega.

## Gentle-AI

Gentle-AI es una integración opcional y se configura por separado. Consulta su
documentación vigente, fija una versión cuando necesites reproducibilidad y
evita instaladores globales sin consentimiento. Este template nunca sustituye
sus recibos nativos con archivos escritos por agentes.

## Rollback

Deshabilitar RDD no convierte recibos inexistentes en aprobaciones. El proyecto
vuelve a su política ordinaria de entrega y debe registrar el estado como
`disabled/unmanaged`.

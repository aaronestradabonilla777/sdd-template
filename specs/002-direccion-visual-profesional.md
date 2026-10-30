# Spec: Retirar retrato del autor

## Qué se construye
Se conserva el diseño del commit `ac38d12` (videos, escena 3D, fondo animado) y solo se retira el retrato del autor de la sección "Sobre".

## Criterios de aceptación
- La sección `#about` no muestra ninguna imagen; mantiene etiqueta, título, textos y cita.
- `founder.png` no existe en `public/` ni se copia al sitio estático.
- El resto de la página (ES y EN) queda igual que en `ac38d12`.

## Diseño
- La sección `#about` pasa de dos columnas a una sola columna centrada (`max-w-3xl`).

## Casos límite
- Sin imagen, la sección sigue legible en móvil y escritorio.

## Pendiente
- `public/robotarm/robot.glb` tiene licencia no comercial: revisar o sustituir antes de uso comercial.

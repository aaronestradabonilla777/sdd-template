# Spec: Sitio multipágina y páginas individuales de servicios

## Qué se construye
1. Dividir el sitio en páginas principales (Inicio, Servicios, Trabajo, Nosotros, Contacto) y páginas dedicadas individuales para cada una de las 4 capacidades técnicas:
   - `/services/software` (y `/es/services/software`)
   - `/services/mechatronics` (y `/es/services/mechatronics`)
   - `/services/robotics` (y `/es/services/robotics`)
   - `/services/consulting` (y `/es/services/consulting`)
2. Retirar la marquesina/ticker de tecnologías (`marquee`), por redundante y decorativo.
3. Asegurar reproducción automática nativa de los videos en bucle (`autoplay muted loop playsinline preload="auto"`) en todas las tarjetas y secciones para evitar cuadros negros congelados en el frame inicial.

## Rutas
| Página | EN | ES |
|---|---|---|
| Inicio | `/` | `/es` |
| Servicios (Hub) | `/services` | `/es/services` |
| Software Industrial | `/services/software` | `/es/services/software` |
| Mecatrónica y 3D | `/services/mechatronics` | `/es/services/mechatronics` |
| Control y Robótica | `/services/robotics` | `/es/services/robotics` |
| Consultoría Técnica | `/services/consulting` | `/es/services/consulting` |
| Trabajo | `/work` | `/es/work` |
| Nosotros | `/about` | `/es/about` |
| Contacto | `/contact` | `/es/contact` |

## Contenido por página de servicio individual
- Encabezado con código (`01/04`), título, lead y metadatos técnicos (enfoque, entorno, herramientas).
- Video demostrativo relevante en bucle con pie de figura técnico (`Fig. 01`).
- Problema industrial resuelto y enfoque de ingeniería de Pernnova.
- Desglose de 4 entregables/módulos clave.
- Pila tecnológica y hardware aplicado.
- Navegación al siguiente servicio y CTA directo a contacto.

## Criterios de aceptación
- Las 18 rutas compilan sin errores (`pnpm run build`).
- Los videos en tarjetas y páginas no quedan en negro; reproducen fluidamente en silencio.
- La marquesina de tecnologías queda retirada de `/services`.
- Enlaces de navbar, mega-menú y tarjetas de la home conducen directamente a las páginas individuales de cada servicio.
- El selector EN/ES preserva la ruta actual tanto en páginas generales como en servicios individuales.

# Spec: Sitio Corporativo Pernnova (MVP)

## ¿Qué se construye?
El sitio web corporativo de Pernnova: una web estática rápida que presenta la firma, sus
servicios/expertise, proyectos y un camino claro a contacto/partnership.

## ¿Por qué?
Pernnova vende ingeniería de alto rendimiento (software, mecatrónica, robótica). La web es
la primera demo de su calidad: debe transmitir precisión y cargar rapidísimo. Hoy no existe
presencia web; esto la crea y habilita la captación de clientes industriales B2B.

## Stack / ubicación
- **Astro + Tailwind + TypeScript**, estático (SSG). React solo en islas con interactividad.
- Tokens: `design-tokens.css` (ya creado). Estructura simple de páginas Astro, sin
  Clean Architecture de backend (no hay backend). [ponytail: sin sobre-ingeniería]
- Carpetas previstas:
  - `src/pages/` — una ruta por página (index, services, projects, about, contact)
  - `src/components/` — Navbar, Footer, Button, CapabilityCard, ProjectCard, TechChip
  - `src/styles/` — tokens globales + base

## Dependencias
- [x] Design tokens definidos (`design-tokens.css`)
- [ ] Logo en SVG/PNG sin fondo metálico (para navbar/footer)
- [ ] Decisión idioma UI (ES / EN / bilingüe)
- [ ] Contenido real de proyectos/casos

## Páginas y secciones (alcance MVP)
1. **Home** — Hero (headline + CTA + hueco visual orbital), Overview, 4 Capabilities,
   Tech Stack strip, Featured Projects (preview), CTA final.
2. **Services / Expertise** — desglose de las 4 capacidades.
3. **Projects** — grid de casos + plantilla de detalle.
4. **About** — filosofía de ingeniería, fundador/lead.
5. **Contact** — formulario (nombre, empresa, email, mensaje) + email directo.

Las 4 capabilities: Industrial Software Architecture · Advanced Mechatronics & 3D ·
Control & Robotics · Technical Consulting.

## Criterios de aceptación
- [ ] Las 5 páginas existen y son navegables (navbar + footer en todas)
- [ ] Navbar sticky: transparente sobre el hero, sólida al hacer scroll; menú hamburguesa en móvil
- [ ] Responsive real en 375px y 1440px usando los tokens (clamp en tipografía)
- [ ] Formulario de contacto funcional (Web3Forms/Formspree) con validación de inputs
- [ ] Lighthouse: Performance y Accessibility ≥ 95 en Home
- [ ] SEO básico: title/description por página, Open Graph, sitemap

## Edge cases
- Envío de formulario fallido / sin red → mensaje de error claro, no se pierde lo escrito
- Sin JavaScript → contenido y navegación principales siguen visibles (Astro estático)
- Móvil sin soporte 3D (fase 2) → fallback a imagen

## Componentes frontend
| Componente      | Responsabilidad |
|-----------------|----------------|
| Navbar          | Navegación + CTA; estado transparente/sólido; drawer móvil |
| Footer          | Contacto, enlaces, copyright |
| Button          | CTA primario/secundario con estados hover |
| CapabilityCard  | Tarjeta de una de las 4 capacidades |
| ProjectCard     | Preview de un proyecto/caso |
| TechChip        | Chip de tecnología (Rust, KUKA, etc.) |
| ContactForm     | Isla (React o form nativo) con validación |

## Fuera de scope (MVP)
- Modelo/escena 3D (fase 2)
- Blog/CMS
- Multi-idioma completo (se decide después; MVP en un solo idioma)
- Backend propio / panel de clientes

## Definition of Done
- [ ] `npm run build` sin errores ni warnings
- [ ] Criterios de aceptación cumplidos
- [ ] Edge cases cubiertos
- [ ] Sin `any` en TS
- [ ] Desplegado en preview (Cloudflare Pages / Vercel)

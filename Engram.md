# Engram — Pernnova Web

> Memoria técnica persistente del repositorio Pernnova Web. Debe leerse al inicio de cada sesión y actualizarse en Phase N Closing.
> Presupuesto estricto: 80 a 150 líneas. Poda de elementos obsoletos hacia `docs/engram_archive.md`.

## 1. Stack e Infraestructura
- **Runtime & Motor**: Node.js >= 20.0.0, arquitectura ESM estricta (`"type": "module"` en `package.json`).
- **Framework Frontend**: Astro 5.18.2 configurado en modo estático (SSG) con arquitectura de islas de hidratación mínima.
- **Tipado & Compilación**: TypeScript con configuración estricta (`astro/tsconfigs/strict`, tipados en `.astro/types.d.ts`).
- **Sistema de Estilos**: Tailwind CSS v4.1.0 integrado mediante el plugin oficial `@tailwindcss/vite` en `astro.config.mjs`.
- **Motor de Animación**: GSAP 3.15.0 + ScrollTrigger para interacciones visuales a 60fps y efectos reactivos al scroll.
- **Optimización de Imágenes**: Sharp 0.35.1 para compresión determinista de fotografías de alta resolución en build time.
- **Tipografía Web**: Google Fonts cargadas asíncronamente (Space Grotesk 700/600, Rajdhani 600/500, Inter 400/500).
- **Métricas de Calidad**: 100/100 en Google Lighthouse (Performance, Accesibilidad, Mejores Prácticas y SEO).
- **Infraestructura de Hosting**: Cloudflare Pages / Vercel Pro (distribución perimetral global mediante CDN Edge).
- **Entorno Local & Puertos**: `http://localhost:4321` (Inglés por defecto en `/`, Español localizado en `/es/`).
- **Puntos de Contacto Oficiales**: WhatsApp directo (`https://wa.me/526391218535`), LinkedIn, Instagram, Facebook y `pernnovaofficial@gmail.com`.

## 2. Invariantes y Comandos
- **Gestor de Paquetes Exclusivo**: Uso innegociable de `pnpm` (prohibido ejecutar `npm` o `yarn` para evitar deriva de dependencias).
- **Enrutamiento i18n Inviolable**: Configuración `prefixDefaultLocale: false` (`en` en raíz `/`, `es` en `/es/`).
- **Disciplina Cromática 85/12/3**: 85% Obsidiana Carbono (`#05070B`, `#0B0E14`), 12% Titanio y Plata (`#F8FAFC`, `#94A3B8`), 3% Oro Champán Imperial (`#D4AF37`), Cian Hielo (`#5FD0F5`) y Láser Carmesí (`#F43F5E`).
- **Jerarquía Tipográfica**: Space Grotesk (encabezados heroicos), Rajdhani (subtítulos y telemetría técnica), Inter (lectura y cuerpo).
- **Geometría Monolítica**: Cortes a 45° (`clip-path`), paneles `.arasaka-panel`, retícula matricial con cruces `+` y ondas láser `.cyber-wave-stage`.
- **Cero Repetición de Activos**: 13 imágenes 16:9 de estudio únicas en `public/images/` para servicios, catálogo y portafolio.
- **Convención de Estructura**: Páginas en `src/pages/[...lang]/`, componentes en `src/components/`, tokens en `src/styles/`.
- **Gobernanza Dual**: El proceso SDD manda el ciclo de vida (cero código sin spec); Ponytail rige la eficiencia interna (`// ponytail:`).
- **Comandos Operativos de Terminal**:
  - `pnpm install` — Instalación exacta y determinista de dependencias contra `pnpm-lock.yaml`.
  - `pnpm dev` — Servidor de desarrollo reactivo local en `http://localhost:4321`.
  - `pnpm build` — Compilación estática de producción hacia la carpeta `dist/`.
  - `pnpm preview` — Previsualización local del artefacto final compilado.
  - `python tests/e2e_test_runner.py` — Ejecución de la suite de validación arquitectónica E2E (Tiers 1 a 4).

## 3. Decisiones Técnicas Tomadas
- **ADR-01: Framework SSG con Astro 5 y Tailwind CSS v4**
  - **Decisión**: Adoptar Astro 5 en modo estático combinado con Tailwind CSS v4 via `@tailwindcss/vite`.
  - **Motivo**: Generación de HTML/CSS puro sin sobrecarga de JavaScript en cliente, logrando puntuaciones Lighthouse de 100/100.
  - **Alternativas Descartadas**: Next.js / Nuxt SPA (descartados por sobrecarga de hidratación en cliente y costo innecesario de servidor).
- **ADR-02: Sistema de Diseño Arasaka Imperial y Disciplina Cromática 85/12/3**
  - **Decisión**: Geometría monolítica con biseles a 45° (`tokens.css`, `global.css`) y paleta de contraste 85% obsidiana, 12% plata, 3% acentos.
  - **Motivo**: Comunicar posicionamiento industrial-militar de máxima ingeniería para servicios de automatización, mecatrónica y robótica.
  - **Alternativas Descartadas**: Componentes estándar de Shadcn o Tailwind UI (descartados por apariencia SaaS genérica sin diferenciación).
- **ADR-03: Motor de Animación Liviano con GSAP 3 y ScrollTrigger**
  - **Decisión**: Emplear GSAP 3 y ScrollTrigger para ondas luminosas `.cyber-wave-stage` y revelado de tarjetas de cristal ahumado.
  - **Motivo**: Animaciones fluidas a 60fps con control determinista de timelines sin bloquear el hilo de ejecución principal.
  - **Alternativas Descartadas**: Framer Motion (descartado por requerir envoltorios React pesados en un sitio predominantemente estático).
- **ADR-04: Internacionalización Bilingüe Nativa sin Prefijo en Idioma Primario**
  - **Decisión**: Configurar Astro i18n nativo con `locales: ['en', 'es']`, default `en` y `prefixDefaultLocale: false`.
  - **Motivo**: Maximizar captación internacional directa en inglés en `/` manteniendo navegación hispana natural en `/es/`.
  - **Alternativas Descartadas**: Detección obligatoria por geo-IP o subdominios separados (descartados por complejidad en Edge CDN).
- **ADR-05: Coexistencia de Gobernanza SDD y Filosofía Ponytail**
  - **Decisión**: Subordinar toda implementación a especificaciones previas en `specs/`, permitiendo minimalismo radical dentro de cada módulo.
  - **Motivo**: Prevenir deuda técnica y alucinaciones de agentes sin sobrecargar la landing estática con capas Clean Architecture innecesarias.
  - **Alternativas Descartadas**: Desarrollo ágil no estructurado (falta de control) o Clean Architecture hexagonal rígida (YAGNI en landing SSG).
- **ADR-06: Catálogo de Activos Visuales Únicos 16:9 sin Redundancia**
  - **Decisión**: Generar y estandarizar 13 fotografías de estudio 16:9 dedicadas en `public/images/` para hero, servicios, catálogo y proyectos.
  - **Motivo**: Garantizar credibilidad industrial de alto nivel y eliminar la fatiga visual generada por duplicación de imágenes genéricas.
  - **Alternativas Descartadas**: Uso de placeholders abstractos o repetición de fotografías entre subpáginas (descartado por degradación de marca).
- **ADR-07: Protocolo de Contacto Directo con Fallback Omnicanal**
  - **Decisión**: Integrar enlace directo a API de WhatsApp corporativo, redes sociales oficiales y respaldo `mailto:` en formulario y footer.
  - **Motivo**: Facilitar conversión ejecutiva inmediata sin fricciones de autenticación ni dependencias de backend transaccional costoso.
  - **Alternativas Descartadas**: Integración de pasarelas de pago o servicios de mensajería propietarios con costos recurrentes de suscripción.
- **ADR-08: Pipeline Determinista de Procesamiento Gráfico con Sharp**
  - **Decisión**: Compilar imágenes estáticas mediante Sharp 0.35.1 en tiempo de compilación.
  - **Motivo**: Reducción drástica del Largest Contentful Paint (LCP < 1.2s) en conexiones móviles sin requerir servidores dinámicos.
  - **Alternativas Descartadas**: Servicios SaaS externos de procesamiento de imágenes al vuelo (descartados por latencia y costo operativo).
- **ADR-09: Aislamiento Modular de Efectos Visuales y Fondos Dinámicos**
  - **Decisión**: Encapsular fondos de red (`NodeBackground.astro`) y bandas de video (`VideoBand.astro`) como componentes independientes.
  - **Motivo**: Prevenir bloqueos de renderizado en el hilo principal y garantizar degradación fluida en dispositivos móviles de gama baja.
  - **Alternativas Descartadas**: Inyección de scripts globales en `Base.astro` (descartado por acoplamiento y penalización de TBT).

## 4. Estado de Módulos
| Módulo | Estado | Responsable | Notas de Implementación |
|---|---|---|---|
| Tooling & Runtime Base | COMPLETADO | Core Setup | Astro 5.18.2, TypeScript strict, Tailwind v4 y pnpm configurados |
| Design Tokens & Variables CSS | COMPLETADO | Frontend Specialist | Paleta 85/12/3, cortes a 45°, fuentes Space Grotesk/Rajdhani/Inter |
| Layout Base & Metadata SEO | COMPLETADO | Worker | Estructura HTML5 unificada en `Base.astro` con canonicals i18n |
| Componentes UI Estructurales | COMPLETADO | Worker | Navbar monolítica, Footer institucional, PageHeader reutilizable |
| Componentes Gráficos Avanzados | COMPLETADO | Frontend Specialist | NodeBackground, VideoBand industrial y ScrollFX interactivo |
| Páginas Principales Bilingües | COMPLETADO | Worker | Rutas `index`, `about`, `services`, `work`, `contact` en `/` y `/es/` |
| Subpáginas de Detalle Técnico | COMPLETADO | Worker | Detalle técnico dinámico en `/services/[service].astro` |
| Sistema i18n Bilingüe | COMPLETADO | Worker | Diccionario de cadenas tipadas en `src/i18n/ui.ts` |
| Catálogo Fotográfico 16:9 | COMPLETADO | Design Team | 13 fotografías de estudio únicas en `public/images/` sin duplicación |
| Formulario de Contacto Omnicanal | COMPLETADO | Worker | Integración de WhatsApp directo, redes oficiales y respaldo mailto |
| Especificaciones SDD (Specs) | COMPLETADO | Systems Architect | Specs 001, 002 y 003 aprobadas y versionadas en `specs/` |
| Harnesses Multi-Agente SDD | COMPLETADO | Teamwork Agents | M1 a M4 completados (Taxonomía, Discovery, Closing, Sizing, E2E) |
| Pipeline de Verificación E2E | COMPLETADO | QA Specialist | Validador `tests/e2e_test_runner.py` con cobertura Tiers 1 a 4 |

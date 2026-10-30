# Spec 002: Rediseño "Precision Industrial Minimalism" + 3D Propio (Blender) + Simuladores por Servicio

## 1. ¿Qué se construye y por qué?
Transformar la percepción visual de **Pernnova Web** para eliminar el aspecto de "plantilla generada por IA" y posicionarla como una firma de ingeniería B2B de alto nivel (referentes: *Anduril, Linear, Palantir, Boston Dynamics*), conservando intacta toda la información técnica, rutas bilingües (`/`, `/es/`) y el **Navbar con glassmorphism** que ya funciona bien.

### Problemas actuales que resuelve:
1. **Videos IA con zoom forzado (`scale(1.45)`):** Se reemplazan por modelos 3D `.glb` propios generados programáticamente en **Blender 5.1** (100% libres de restricciones no-comerciales) y renders/simulaciones técnicas limpias sin marcas de agua.
2. **Ruido visual ("cliché IA"):** Se retira la red de partículas flotantes (`NodeBackground.astro`) y se reducen los resplandores neón difuminados (`glow-accent`), adoptando una estética de **Plano Industrial / Swiss Grid** con paleta **85% Grafito Mate / 12% Blanco-Plata / 3% Acentos de Precisión (Cian + Ámbar + Verde Telemetría)**.
3. **Subpáginas de servicios idénticas:** Cada una de las 4 subpáginas (`/services/software`, `/services/mechatronics`, `/services/robotics`, `/services/consulting`) recibe su propio **componente interactivo en React + TypeScript** en lugar de repetir el mismo reproductor de video.
4. **Modelo 3D en `/work`:** Se reemplaza el modelo `robot-no-comercial.glb` (CC-BY-NC) por un modelo `.glb` propio generado en Blender.
5. **Footer corporativo con redes oficiales:** Se integran los enlaces oficiales de LinkedIn, Instagram y Facebook junto con acceso directo a las 4 disciplinas.

---

## 2. Regla de Estilo y Paleta (85 / 12 / 3)
- **Glassmorphism (10%):** Exclusivo para `Navbar.astro` (`.nav-island`, `.mega-panel`) y los paneles flotantes de control (HUD) sobre los visores 3D.
- **Minimalismo Industrial / Swiss Grid (90%):** Superficies mate sólidas, bordes continuos de `1px`, marcas de corte tipo plano CAD (`+` en intersecciones) y etiquetas monoespaciadas (`FIG. 01`, `REV 2.4`).
- **Tokens semánticos actualizados (`src/styles/tokens.css`):**
  - `--bg-base`: `#090B0E` (Dark) / `#F4F6F8` (Light)
  - `--bg-surface`: `#0F1217` (Dark) / `#FFFFFF` (Light)
  - `--bg-card`: `#141820` (Dark) / `#FFFFFF` (Light)
  - `--border`: `#222834` (Dark) / `#DCE2EA` (Light)
  - `--accent`: `#5FD0F5` (Cian Pernnova — foco, trazos activos, marca)
  - `--signal-amber`: `#F59E0B` (Ámbar Industrial — cotas mecánicas, ejes `J1–J6`, alertas)
  - `--signal-emerald`: `#10B981` (Verde Telemetría — estados `ONLINE`, latencia `0.38 ms`)

---

## 3. Pipeline 3D con Blender 5.1 (MCP `localhost:9876` / Headless `bpy`)
Generación de assets 3D 100% propios y comerciales:
1. **`public/models/pernnova-robot-6axis.glb`:**
   - Brazo robótico industrial articulado de 6 ejes (`Base`, `J1_Turret`, `J2_LowerArm`, `J3_UpperArm`, `J4_Wrist`, `J5_Flange`, `J6_Gripper`) con jerarquía padre-hijo real para permitir rotación cinemática en vivo desde React Three Fiber.
2. **`public/models/pernnova-mecha-assembly.glb`:**
   - Actuador mecatrónico lineal de precisión (chasis estructural, husillo de bolas, guías paralelas, carro portante, servomotor y brida GD&T) con nodos separados para permitir **Vista Explosionada (Exploded View)** y sombreado **FEA Stress Map**.
3. **Videos de previsualización limpios (sin marcas de agua ni recorte `.media-zoom`):**
   - Loops limpios generados desde Blender / Canvas técnico si se requieren en el mega-menú.

---

## 4. Componentes Interactivos por Página (React + TypeScript + R3F)

| Ubicación | Componente (`.tsx`) | Funcionalidad |
|-----------|---------------------|---------------|
| **Home Hero** (`index.astro`) | `HeroRobotStage.tsx` | Visor 3D del brazo robótico industrial con HUD glassmorphism: selector de modo (`Studio PBR`, `CAD Wireframe`, `Blueprint`), telemetría de ejes `J1–J6` en vivo y rotación suave con mouse. |
| **Home Capacidades** (`index.astro`) | Bento Grid Técnico | Layout asimétrico (2 columnas/filas combinadas) con esquemas técnicos vectoriales limpios, métricas de ingeniería y acceso a cada subpágina, sin correr 4 videos pesados a la vez. |
| **01 · Software** (`/services/software`) | `SoftwareTelemetrySim.tsx` | Simulador interactivo de pipeline `PLC (OPC-UA) → Edge Buffer (Rust) → TimescaleDB` con gráfica de throughput en tiempo real y botón **"Simular Corte de Red WAN"** que demuestra cero pérdida de paquetes gracias al buffer local. |
| **02 · Mecatrónica** (`/services/mechatronics`) | `MechaCadViewer.tsx` | Visor 3D CAD del ensamble mecatrónico con slider de **Vista Explosionada (0%–100%)**, modo **Análisis FEA (mapa térmico de esfuerzos)** y vista **Alámbrica (Wireframe)**. |
| **03 · Robótica** (`/services/robotics`) | `RoboticsWorkcellSim.tsx` | Celda robótica 3D donde el usuario puede mover los ejes `J1–J6` con sliders o elegir rutinas (`Pick & Place`, `Continuous Weld`, `Home Calibration`) viendo el estado **PackML** (`EXECUTE`, `IDLE`, `HOLD`). |
| **04 · Consultoría** (`/services/consulting`) | `ConsultingAuditMatrix.tsx` | Comparador interactivo **Arquitectura Legacy vs. Retrofit Pernnova** (topología de red de planta, reducción de paros no programados, retorno de inversión y fases de migración en paro de fin de semana). |
| **Work** (`/work`) | `RobotModel.jsx` (actualizado) | Usa `pernnova-robot-6axis.glb` con controles interactivos y HUD técnico. |
| **Footer** (`Footer.astro`) | Footer 4 columnas + Redes | Incluye enlaces a los 4 servicios, páginas clave, email directo y redes oficiales: [LinkedIn](https://www.linkedin.com/company/pernnova/), [Instagram](https://www.instagram.com/pern.nova/) y [Facebook](https://www.facebook.com/Pernnova/). |

---

## 5. Criterios de Aceptación y Verificación
- [ ] `Navbar.astro` mantiene su diseño actual (glassmorphism + mega-menú + selector EN/ES + toggle de tema).
- [ ] Toda la información existente en `src/i18n/ui.ts` y `src/data/services.ts` (EN y ES) se respeta íntegramente.
- [ ] Los modelos 3D `.glb` son 100% generados en Blender para Pernnova (uso comercial libre) y cargan con `Suspense` y fallback limpio sin bloquear el hilo principal.
- [ ] `prefers-reduced-motion: reduce` pausa animaciones automáticas en 3D y simuladores.
- [ ] `pnpm build` compila todas las rutas estáticas EN y ES sin errores ni warnings de TypeScript.


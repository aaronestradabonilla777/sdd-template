# Frontend Design & Anti-Slop Harness — Estándar Visual skill-issue.in

> **Gobernanza de Diseño UI/UX para proyectos con Frontend en SDD Multi-Agente.**
> Elimina por completo el código visual genérico de IA (botones nativos de sistema operativo, formularios grises y tipografías planas) imponiendo estándares estéticos de nivel producción inspirados en **[skill-issue.in](https://skill-issue.in/)**, diseño tecno-industrial y micro-interacciones de vanguardia.

---

## 1. El Problema: "El Look IA Genérico" (AI Slop)

Cuando un modelo de lenguaje programa interfaces sin directrices de arte, invariablemente comete estos errores:
- Botones nativos `<button>` grises o con gradientes toscos de Bootstrap.
- Inputs con bordes negros duros de navegador y outline azul estándar.
- Fuentes por defecto (Arial, Times New Roman, Roboto sin peso tipográfico).
- Bordes redondeados gigantescos (`rounded-full`) sin jerarquía estructural.
- Cero retroalimentación visual al hacer click o hover.

**Regla de Oro:** Todo agente con rol `Worker` que genere código visual (HTML, CSS, React, Astro, Vue, Svelte, Tailwind) tiene prohibido utilizar componentes nativos del navegador sin estilizar. El agente `Reviewer` rechazará automáticamente cualquier PR que viole este principio.

---

## 2. Las 5 Invariantes Visuales Obligatorias

### Invariante 1: Cero Componentes Crudos del Navegador (Reset Estricto)
Todo elemento interactivo debe resetear los estilos del sistema operativo:
```css
/* Obligatorio en todo botón e input */
appearance: none;
-webkit-appearance: none;
background: transparent;
border: none;
outline: none;
```
Cada control interactivo debe implementar **4 estados obligatorios**:
1. **Reposo (Default):** Superficie limpia con jerarquía visible.
2. **Hover (`:hover`):** Elevación de brillo (`brightness(1.15)`), micro-resplandor (*subtle glow*) o iluminación perimetral.
3. **Active (`:active`):** Feedback táctil de pulsación inmediata (`transform: scale(0.98)` o `translateY(1px)`).
4. **Focus Accesible (`:focus-visible`):** Anillo de enfoque de alto contraste (`outline: 2px solid var(--accent); outline-offset: 2px`).

---

### Invariante 2: Bordes de Cristal y Profundidad (Glass & Borders)
- **Prohibido:** Bordes sólidos gruesos (`border: 2px solid #555`).
- **Obligatorio:** Bordes sutiles semitransparentes que reaccionan a la luz de fondo:
  ```css
  border: 1px solid rgba(255, 255, 255, 0.08);
  background: rgba(12, 16, 24, 0.75);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  ```
- **Profundidad mediante capas:** Los elementos no flotan en el vacío. Tienen sombras direccionales suaves combinadas con resplandor neón tenue en los acentos (`box-shadow: 0 4px 20px -2px rgba(0, 0, 0, 0.5), 0 0 15px -3px var(--accent-glow)`).

---

### Invariante 3: Geometría Arquitectónica y Esquinas Técnicas
- **Inspiración skill-issue.in:** Evita las cápsulas redondas blandas (`rounded-3xl` / `rounded-full`) en paneles, cards y cuadros de mando.
- **Geometría recomendada:**
  - **Esquinas técnicas:** Radio sutil entre `4px` y `8px` (`rounded-sm` / `rounded-md`).
  - **Cortes a 45° (*Chamfer*):** Cortes angulares usando `clip-path` para estética tecno-industrial o militar:
    ```css
    /* Corte biselado en esquina superior derecha */
    clip-path: polygon(0 0, calc(100% - 12px) 0, 100% 12px, 100% 100%, 0 100%);
    ```

---

### Invariante 4: Disciplina Cromática (Regla 85 / 12 / 3)
La paleta debe ser estricta y predecible:
- **85% Superficies Base (Fondos Oscuros / Monolíticos):**
  - Negro Carbono (`#06080C`), Pizarra Profunda (`#0A0D14`), Superficie Tarjeta (`#101520`).
- **12% Texto y Estructura (Jerarquía Limpia):**
  - Titulares: Blanco Titanio (`#F8FAFC`).
  - Cuerpo / Párrafos: Plata Neutro (`#94A3B8`).
  - Metadatos / Etiquetas: Gris Técnico (`#64748B`).
- **3% Acento Primario Único (Luz & Acción):**
  - Un color vibrante (Cian Eléctrico `#00E5FF`, Oro Champán `#D4AF37`, Carmesí Láser `#FF1E42` o Verde Matrix `#00FF88`).
  - El acento se reserva exclusivamente para CTAs primarios, estados activos, indicadores de pulso y links.

---

### Invariante 5: Tipografía con Identidad y Curvas de Aceleración
- **Tipografía:**
  - **Títulos (`h1`, `h2`, `h3`):** Tipografía con peso geométrico o display técnico (ej. *Space Grotesk*, *Rajdhani*, *Syne*, *Clash Display*).
  - **Datos / Código / Badges:** Tipografía monoespaciada para coordenadas, fechas y estados (ej. *JetBrains Mono*, *Fira Code*).
  - **Lectura:** Sans-serif neutral y ultra-legible (ej. *Inter*, *Geist Sans*).
- **Curvas de Transición:**
  - Prohibido `transition: all 0.5s linear`.
  - Usar curvas refinadas de alta respuesta:
    ```css
    transition: all 0.2s cubic-bezier(0.16, 1, 0.3, 1);
    ```

---

## 3. Snippets de Componentes Canónicos (Copiar y Usar)

### A. Botón Primario "High-End Cyber" (CSS Puro)
```css
.btn-primary {
  appearance: none;
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 10px 22px;
  font-family: var(--font-mono, monospace);
  font-size: 0.85rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #06080C;
  background: var(--accent, #00E5FF);
  border: 1px solid var(--accent, #00E5FF);
  border-radius: 4px;
  cursor: pointer;
  box-shadow: 0 0 20px -5px rgba(0, 229, 255, 0.4);
  transition: transform 0.18s cubic-bezier(0.16, 1, 0.3, 1),
              box-shadow 0.18s cubic-bezier(0.16, 1, 0.3, 1),
              filter 0.18s ease;
}

.btn-primary:hover {
  filter: brightness(1.1);
  box-shadow: 0 0 28px 0px rgba(0, 229, 255, 0.65);
  transform: translateY(-1px);
}

.btn-primary:active {
  transform: translateY(1px) scale(0.98);
  box-shadow: 0 0 10px rgba(0, 229, 255, 0.3);
}

.btn-primary:focus-visible {
  outline: 2px solid #FFFFFF;
  outline-offset: 3px;
}
```

### B. Botón Secundario / Ghost con Borde de Cristal
```css
.btn-secondary {
  appearance: none;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  font-family: var(--font-mono, monospace);
  font-size: 0.85rem;
  font-weight: 500;
  color: #E2E8F0;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 4px;
  cursor: pointer;
  backdrop-filter: blur(10px);
  transition: all 0.2s cubic-bezier(0.16, 1, 0.3, 1);
}

.btn-secondary:hover {
  background: rgba(255, 255, 255, 0.08);
  border-color: rgba(255, 255, 255, 0.3);
  color: #FFFFFF;
  transform: translateY(-1px);
}

.btn-secondary:active {
  transform: translateY(1px) scale(0.98);
}
```

### C. Input de Formulario con Iluminación Focal
```css
.input-tech {
  appearance: none;
  width: 100%;
  padding: 12px 16px;
  font-family: var(--font-sans, sans-serif);
  font-size: 0.9rem;
  color: #F8FAFC;
  background: rgba(10, 14, 22, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  outline: none;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.input-tech::placeholder {
  color: #64748B;
}

.input-tech:focus {
  border-color: var(--accent, #00E5FF);
  box-shadow: 0 0 0 1px var(--accent, #00E5FF),
              0 0 18px -4px rgba(0, 229, 255, 0.3);
}
```

### D. Tarjeta Monolítica con Borde Activo
```css
.card-panel {
  background: rgba(12, 17, 26, 0.75);
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 6px;
  padding: 24px;
  backdrop-filter: blur(16px);
  position: relative;
  overflow: hidden;
  transition: border-color 0.25s ease, transform 0.25s cubic-bezier(0.16, 1, 0.3, 1);
}

.card-panel:hover {
  border-color: rgba(255, 255, 255, 0.18);
  transform: translateY(-2px);
}
```

### E. Badge de Estado con Indicador LED
```css
.badge-status {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 3px 10px;
  font-family: var(--font-mono, monospace);
  font-size: 0.72rem;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  background: rgba(0, 229, 255, 0.08);
  border: 1px solid rgba(0, 229, 255, 0.25);
  border-radius: 3px;
  color: var(--accent, #00E5FF);
}

.badge-status::before {
  content: "";
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--accent, #00E5FF);
  box-shadow: 0 0 8px var(--accent, #00E5FF);
}
```

---

## 4. Checklist para el Agente Reviewer
Ante cualquier PR con cambios de interfaz visual, el Reviewer debe verificar:
- [ ] ¿Hay algún elemento `<button>`, `<input>` o `<select>` con estilos crudos del navegador? **(Rechazo inmediato si existe)**.
- [ ] ¿Los botones e inputs tienen definidos los 4 estados (`default`, `:hover`, `:active`, `:focus-visible`)?
- [ ] ¿Los bordes utilizan transparencias sutiles (`rgba(...)`) en lugar de bordes negros o grises sólidos de más de 1px?
- [ ] ¿La paleta respeta la regla 85/12/3 con un solo color de acento principal?
- [ ] ¿Las transiciones utilizan una curva de aceleración moderna (`cubic-bezier`) en lugar de `linear`?


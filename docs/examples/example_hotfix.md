# Guía Práctica de Extremo a Extremo: Hotfix en Producción (Brownfield Fast-Track)

Esta guía documenta un caso práctico y realista de extremo a extremo para la resolución de un incidente en un repositorio en producción (brownfield) utilizando la vía rápida (**Fast-Track SDD**) del framework `sdd-template`.

El ejemplo ilustra cómo un único agente implementador (**Worker**) clasifica la tarea como **Tier 1 (Small / Hotfix)**, ejecuta una auditoría ligera previa (Discovery Light), redacta una micro-especificación basada en [specs/_template_hotfix.md](../../specs/_template_hotfix.md), implementa el cambio mínimo bajo la filosofía [ponytail](../../harnesses/ai-rules.md) y ejecuta el cierre formal abreviado conforme a [harnesses/closing.md](../../harnesses/closing.md).

---

## 1. Contexto del Incidente en Producción

### El Incidente Reportado
En una aplicación web en producción (Pernnova Web), los usuarios y el equipo de diseño detectan un defecto visual crítico en la página de contacto (`/contacto`):
- Los botones principales de llamada a la acción (CTA) pierden legibilidad al pasar el cursor por encima (`:hover`), debido a que el color de texto y el fondo convergen en un contraste insuficiente.
- Causa raíz identificada: En la actualización de tokens de diseño, la variable CSS `--color-accent-hover` en `src/styles/tokens.css` se configuró erróneamente con el valor hexadecimal `#0B0E14` (fondo oscuro) en lugar del valor dorado champán de alta visibilidad `#E5C158`.

### Restricciones Operativas
- **Entorno**: Base de código viva en producción (brownfield).
- **Criticidad**: Requiere despliegue urgente sin introducir fricción burocrática innecesaria ni sobrecarga de tokens.
- **Gobernanza**: Debe cumplir estrictamente las reglas de [AGENTS.md](../../AGENTS.md) y [harnesses/sizing.md](../../harnesses/sizing.md).

---

## 2. Evaluación Determinista de Sizing (Tier 1: Small / Hotfix)

Antes de alterar cualquier archivo de producción, se aplica la **Matriz de Decisión Rápida (4 Preguntas Binarias)** definida en [harnesses/sizing.md](../../harnesses/sizing.md):

| # | Pregunta de Clasificación | Evaluación | Justificación Técnica |
|---|---|---|---|
| 1 | ¿Afecta a más de 3 archivos? | **NO** | Confinado a 1 solo archivo: `src/styles/tokens.css`. |
| 2 | ¿Modifica esquemas de base de datos o contratos de APIs? | **NO** | Cero alteraciones de esquema, cero mutaciones de endpoints REST o interfaces públicas. |
| 3 | ¿Afecta lógica de negocio central, autenticación o seguridad? | **NO** | Ajuste exclusivamente cosmético/CSS sin impacto en reglas de negocio ni flujos de datos. |
| 4 | ¿Se estima que supere las 80 líneas de código netas? | **NO** | Modificación neta de 6 líneas de código CSS. |

### Veredicto de Sizing
**Resultado**: Respuesta "NO" a las 4 preguntas.  
**Clasificación**: **Tier 1 (Small / Hotfix)**.  
**Flujo Activado**: **Vía Rápida (Fast-Track SDD)**.  
**Límites Mandatorios**:
- Archivos afectados: Máximo 3 (real: 1 archivo).
- Líneas netas cambiadas: Máximo 80 LOC (real: 2 LOC).
- Especificación técnica: Micro-spec en `specs/hotfixes/` basada en `specs/_template_hotfix.md` con límite estricto de **< 25 líneas**.
- Asignación de Modelo: **Tier 2 (Fast)** o **Tier 3 (Local SLM)** para ejecución ágil y económica.

---

## 3. Fase 0: Discovery Light (Verificación de Línea Base)

De acuerdo con el protocolo de auditoría previa de [harnesses/discovery.md](../../harnesses/discovery.md), las tareas catalogadas como Tier 1 no requieren la auditoría profunda de 6 pilares (`ARCH_AUDIT.md`), sino una **Fase 0 Discovery Light (Pasos 1 a 3 abreviados)**:

### Paso 1: Topografía del Repositorio
- Manifiesto raíz: Se confirma proyecto monorepo web basado en Astro 5 y Tailwind CSS v4.
- Fuentes de verdad: Reglas maestras en [AGENTS.md](../../AGENTS.md), memoria viva en [Engram.md](../../Engram.md).

### Paso 2: Grafo de Dependencias y Gestor de Paquetes
- **Detección Innegociable (`package_manager_enforcement`)**: Se detecta la presencia de `pnpm-lock.yaml`.
- **Regla Mandatoria**: Se debe utilizar estrictamente `pnpm`. Prohibido invocar `npm` o `yarn`.

### Paso 3: Auditoría de Línea Base Preexistente (`baseline_audit_enforcement`)
Antes de redactar la micro-spec o editar código, el agente verifica que la línea base compile limpiamente:

```bash
pnpm build
```
*Salida verbatim de terminal:*
```text
✔ 13 pages built in 1.42s
✔ Static output generated in dist/
Exit code: 0
```
La línea base está sana. Cero roturas previas documentadas.

---

## 4. Fase 1: Redacción de la Micro-Spec (<25 líneas)

El Worker redacta la micro-especificación en `specs/hotfixes/20261029-cta-hover-token.md` utilizando la plantilla oficial [specs/_template_hotfix.md](../../specs/_template_hotfix.md).

El archivo resultante contiene exactamente **21 líneas** (cumpliendo la cota estricta `< 25 líneas`):

```markdown
# Hotfix: Corrección de Contraste en Hover de Botón CTA

**Tier:** 1 (Small / Hotfix) | **Fecha:** 2026-10-29
**Estado:** EN PROGRESO

## 1. Problema Detectado
El botón CTA en `/contacto` pierde legibilidad en estado `:hover` debido a que `--color-accent-hover` tiene asignado un tono oscuro `#0B0E14` idéntico al fondo.

## 2. Archivos Afectados (Máximo 3)
- `src/styles/tokens.css` (líneas 42-47)

## 3. Solución Propuesta
Ajustar la variable `--color-accent-hover` en `tokens.css` al valor dorado champán oficial `#E5C158` para garantizar ratio de contraste WCAG AA (>4.5:1).

## 4. Verificación
- Comando de compilación: `pnpm build`
- Verificación visual: Inspeccionar renderizado de botón en `http://localhost:4321/contacto` verificando contraste legible.

## 5. Checklist de Cierre Rápido
- [ ] Compila sin errores (`pnpm build`).
- [ ] Verificación visual exitosa con contraste verificado.
- [ ] Menos de 80 líneas netas cambiadas.
```

---

## 5. Fase 3: Implementación Quirúrgica y Circuit Breaker

### Principio Ponytail ("Lazy Senior Dev")
Siguiendo las directrices de [harnesses/ai-rules.md](../../harnesses/ai-rules.md), el Worker aplica la mínima intervención requerida:
- Cero dependencias añadidas a `package.json`.
- Cero abstracciones o wrappers innecesarios.
- Modificación directa y quirúrgica de tokens existentes.

### Diff Aplicado en `src/styles/tokens.css`
```diff
--- a/src/styles/tokens.css
+++ b/src/styles/tokens.css
@@ -42,6 +42,6 @@
   --color-obsidian-deep: #05070B;
   --color-obsidian-card: #0B0E14;
   --color-gold-champagne: #D4AF37;
-  --color-accent-hover: #0B0E14;
+  --color-accent-hover: #E5C158;
   --color-laser-cyan: #5FD0F5;
 }
```
*Métricas del cambio:* 1 archivo modificado, 1 inserción(+), 1 eliminación(-), 2 LOC netas.

### Gobernanza del Circuit Breaker de 2 Intentos (`isolation.md`)
Conforme a [harnesses/isolation.md](../../harnesses/isolation.md), todo Worker en Tier 2 o Tier 3 está sujeto a la regla de máximo 2 intentos ante fallos de compilación o tests.

- **Intento 1**:
  ```bash
  pnpm build
  ```
  *Salida verbatim de terminal:*
  ```text
  [vite] transforming...
  [astro] rendering pages...
  ✔ 13 pages built in 1.38s
  ✔ [build] Complete! Artifacts written to dist/
  Exit code: 0
  ```
- **Resultado del Circuit Breaker**: Exitoso en el **Intento 1**. No se requirieron reintentos (0 fallos). El disyuntor no fue activado.

---

## 6. Fase N: Protocolo de Cierre Rápido (Fast Closing)

Siguiendo el protocolo estandarizado de [harnesses/closing.md](../../harnesses/closing.md), se ejecutan las 4 etapas de cierre:

### Etapa 1: Post-Implementation Verification Sign-Off (PVS) Abreviado
El Worker formaliza la evidencia de verificación en la matriz PVS:

| Criterio | Verificación Ejecutada | Evidencia Verbatim / Log | Estado | Rol / Modelo |
|---|---|---|---|---|
| Clean Build | `pnpm build` | `✔ 13 pages built in 1.38s (exit code 0)` | PASS | Worker (Tier 2 Fast) |
| Verificación Visual | Inspección `:hover` en `/contacto` | `Ratio de contraste 5.2:1 (Cumple WCAG AA)` | PASS | Worker (Tier 2 Fast) |
| Límite de Archivos | `git diff --name-only` | `src/styles/tokens.css` (1 archivo <= 3) | PASS | Worker (Tier 2 Fast) |
| Límite de LOC | `git diff --stat` | `1 file changed, 1 insertion(+), 1 deletion(-)` (2 LOC <= 80) | PASS | Worker (Tier 2 Fast) |

### Etapa 2: Sincronización de Memoria en `Engram.md`
Siguiendo las reglas de [harnesses/engram.md](../../harnesses/engram.md), la memoria viva del repositorio [Engram.md](../../Engram.md) debe mantenerse en un rango estricto de **80 a 150 líneas**. Para hotfixes Tier 1, se añade una actualización sintética de una línea en la tabla de módulos:

```markdown
| Design Tokens & Variables CSS | COMPLETADO | Worker | Corregido contraste de token --color-accent-hover en hotfix 20261029 |
```

### Etapa 3: Sincronización de Especificaciones
Se actualiza la cabecera del archivo `specs/hotfixes/20261029-cta-hover-token.md`:
```markdown
**Tier:** 1 (Small / Hotfix) | **Fecha:** 2026-10-29
**Estado:** COMPLETADA
```

### Etapa 4: Registro de Cambios y Commit Convencional
1. Se añade la entrada en `CHANGELOG.md` bajo la categoría canónica `Fixed`:
   ```markdown
   ### Fixed
   - Corregido contraste deficiente en estado hover de botones CTA principales mediante actualización de token `--color-accent-hover` en `tokens.css`.
   ```
2. Se genera el commit estandarizado conforme a Conventional Commits:
   ```bash
   git add src/styles/tokens.css specs/hotfixes/20261029-cta-hover-token.md Engram.md CHANGELOG.md
   git commit -m "fix(ui): correct cta button hover token in tokens.css"
   ```

---

## 7. Métricas de Eficiencia y Consumo de Tokens

La vía rápida para hotfixes optimiza radicalmente el presupuesto cognitivo del proyecto:

| Métrica / Dimensión | Flujo Estándar Completo (Tier 2) | Flujo Vía Rápida Utilizado (Tier 1) | Ahorro Obtenido |
|---|---|---|---|
| Roles Involucrados | 3 a 5 agentes (Orchestrator, Worker, Reviewer...) | 1 agente (Worker Tier 2 o Tier 3) | 66% menos cambios de contexto |
| Pasos de Especificación | Spec formal + Diseño por capas + PR Review | Micro-spec <25 líneas + Clean Build | 75% menos pasos intermedios |
| Tokens Consumidos | ~8,000 – 12,000 tokens | **~1,850 tokens** | **~82% de ahorro en tokens** |
| Tiempo Total de Resolución | 8 – 15 minutos | **< 2 minutos** | **85% reducción de latencia** |

---

## 8. Verificación de Invarianzas Cumplidas

- [x] **Clasificación Determinista**: Evaluada mediante las 4 preguntas de [harnesses/sizing.md](../../harnesses/sizing.md).
- [x] **Conteo de Archivos**: 1 archivo modificado (límite: <= 3 archivos).
- [x] **Volumen de Líneas (LOC)**: 2 líneas netas cambiadas (límite: <= 80 LOC).
- [x] **Micro-Spec Concisa**: 21 líneas en `specs/hotfixes/` (límite: < 25 líneas).
- [x] **Cero Mutaciones de Esquema**: Sin cambios en bases de datos ni contratos públicos.
- [x] **Circuit Breaker Respetado**: Compilación y verificación superadas en el Intento 1.
- [x] **Cierre Completo (PVS & Engram)**: Matriz PVS firmada, [Engram.md](../../Engram.md) actualizado respetando el presupuesto de 80-150 líneas.

Para explorar cómo se orquesta una funcionalidad compleja desde cero mediante colaboración multi-agente, consulte el [Ejemplo Greenfield (Tier 2 Standard SDD)](example_greenfield.md).

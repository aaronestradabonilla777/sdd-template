# Engram Memory Harness — Gobernanza y Gestión de Memoria Persistente Acotada

## 1. Propósito y Alcance Multi-Agente
El **Engram Memory Harness** establece las reglas deterministas para la gestión de la memoria persistente del repositorio (`Engram.md`). Su propósito es mantener un contexto técnico vivo, condensado y de alta fidelidad entre sesiones asíncronas de inteligencia artificial, operando de forma agnóstica a través de **Google Antigravity**, **Anthropic Claude Code** y **OpenCode / Ollama**.

Previene los dos fallos críticos en sistemas multi-agente:
1. **Amnesia entre sesiones**: Pérdida de decisiones técnicas previas, invariantes arquitectónicas o estado de avance de los módulos.
2. **Explosión cuadrática de contexto**: Saturación de la ventana de contexto mediante volcados desordenados de logs, código fuente redundante o historial de chat.

---

## 2. Presupuesto Estricto de Líneas (80 - 150 líneas)
El archivo `Engram.md` está sujeto a una compuerta estricta de tamaño en líneas:

$$\text{Presupuesto de Memoria: } 80 \le \text{Líneas de Engram.md} \le 150 \text{ líneas}$$

- **Límite Inferior (80 líneas)**: Protege la completitud estructural mínima. Un archivo `Engram.md` con menos de 80 líneas denota omisión de secciones requeridas o falta de granularidad operativa.
- **Límite Superior (150 líneas)**: Protege el presupuesto de tokens. Ningún archivo `Engram.md` puede superar las 150 líneas. Superar este umbral activa de forma obligatoria el protocolo de poda.
- **Ventana Operativa Óptima**: 90 a 120 líneas, permitiendo margen para registrar nuevas decisiones antes de requerir poda.

---

## 3. Arquitectura Estándar en 4 Secciones Obligatorias
Todo archivo `Engram.md` debe mantener obligatoriamente las siguientes cuatro secciones numeradas:

### ## 1. Stack e Infraestructura
- **Contenido**: Tecnologías centrales del sistema, versiones mayores del runtime, herramientas de empaquetado, dependencias críticas, puertos locales de desarrollo y plataformas de despliegue.
- **Criterio**: Información de baja tasa de cambio que define el entorno de ejecución.

### ## 2. Invariantes y Comandos
- **Contenido**: Reglas de ingeniería innegociables (gestor de paquetes obligatorio, disciplina tipográfica, restricciones cromáticas o de diseño, contratos de interfaz) y comandos shell exactos para instalación, desarrollo, build y verificación.
- **Criterio**: Comandos ejecutables en una sola línea y directrices absolutas que ningún agente puede violar.

### ## 3. Decisiones Técnicas Tomadas
- **Contenido**: Registro estructurado de decisiones arquitectónicas bajo formato micro-ADR (Architecture Decision Record).
- **Criterio**: Hechos y acuerdos técnicos definitivos. Prohibido registrar discusiones inconclusas o alternativas hipotéticas sin resolver.

### ## 4. Estado de Módulos
- **Contenido**: Matriz tabular de módulos o subsistemas del proyecto, indicando nombre del componente/módulo, estado actual (`COMPLETADO`, `EN PROGRESO`, `PENDIENTE`), agente responsable y notas de implementación.
- **Criterio**: Visibilidad inmediata del avance global del proyecto para enrutamiento de dependencias en Phase 0 y Phase N.

---

## 4. Reglas de Densidad de Información
El Engram prioriza la **densidad** de señal sobre la exhaustividad textual:
- **Hechos y Decisiones**: Cada línea debe aportar un hecho técnico comprobable o una decisión de diseño vinculante.
- **Cero Código Fuente en Bloque**: Prohibido insertar fragmentos de código fuente o implementaciones extensas (el código pertenece exclusivamente a `src/`). Solo se admiten comandos breves de terminal en la sección de invariantes.
- **Cero Transcripciones de Chat**: Prohibido registrar conversaciones entre agentes, saludos, disculpas o deliberaciones inconclusas (la coordinación conversacional es efímera).
- **Cero Trazas de Error o Logs de Compilación**: Las trazas de depuración pertenecen a artefactos de trabajo temporal en `.agents/teamwork/` o al historial de git, nunca al Engram.
- **Cero Especificaciones Exhaustivas**: Los criterios de aceptación y contratos detallados pertenecen a `specs/`.

---

## 5. Protocolo de Poda y Archivado (`docs/engram_archive.md`)
Cuando la evolución del proyecto empuje a `Engram.md` a exceder el límite superior de **150 líneas**, se debe ejecutar el protocolo de **poda** formal:

1. **Detección**: Durante Phase N Closing, el agente auditor u orquestador evalúa `wc -l Engram.md`. Si el conteo supera 140 líneas (o cruza 150), se gatilla la poda preventiva.
2. **Identificación de Elementos Podables**:
   - Decisiones técnicas superadas o reemplazadas por nuevas iteraciones.
   - Módulos antiguos cuyo ciclo de vida está 100% estabilizado y fusionado en la base de código.
   - Detalles operativos transitorios de hitos pasados.
3. **Migración a Archivo**:
   - Los elementos podados se extraen de `Engram.md` y se agregan a `docs/engram_archive.md` bajo un encabezado fechado en formato ISO UTC (ej. `## Archivado — 2026-10-29T20:00:00Z`).
   - Si `docs/engram_archive.md` no existe, se crea automáticamente.
4. **Condensación del Engram Activo**:
   - Se sintetizan las referencias en `Engram.md`, restaurando el tamaño a la ventana óptima de 90 a 120 líneas (siempre dentro del rango de 80 a 150 líneas).

---

## 6. Formato Estándar de Micro-ADR
Toda entrada en la sección `## 3. Decisiones Técnicas Tomadas` debe estructurarse mediante el siguiente contrato de tres campos:

```markdown
- **ADR-[ID]: [Título Descriptivo de la Decisión]**
  - **Decisión**: Qué se decidió implementar, adoptar o configurar de forma explícita.
  - **Motivo**: Justificación causal fundamentada (rendimiento, seguridad, restricciones de plataforma, DX o eficiencia de tokens).
  - **Alternativas Descartadas**: Tecnologías, librerías o patrones evaluados que fueron rechazados formalmente y la razón técnica del descarte.
```

---

## 7. Ciclo de Vida Multi-Agente: Al Iniciar y Al Finalizar
La sincronización del Engram está vinculada a las compuertas de inicio y cierre de sesión:

### Al Iniciar
- **Lectura Obligatoria**: Todo agente (Orchestrator, Explorer, Worker, Reviewer, Challenger, Auditor) debe leer `Engram.md` antes de ejecutar cualquier comando o diseñar especificaciones.
- **Inyección de Verdad Terreno**: Establece las versiones de runtime, el gestor de paquetes habilitado y las invariantes que gobiernan la tarea.

### Al Finalizar
- **Sincronización en Phase N Closing**: Ninguna tarea, hotfix o milestone puede darse por concluido sin actualizar `Engram.md` (`harnesses/closing.md`).
- **Registro de Decisiones**: Se documentan las decisiones técnicas tomadas en la sesión en formato micro-ADR.
- **Actualización de Módulos**: Se actualiza el estado de los componentes afectados en la matriz de módulos.
- **Comprobación de Presupuesto**: Se verifica el cumplimiento del rango de 80 a 150 líneas y se ejecuta poda si aplica.

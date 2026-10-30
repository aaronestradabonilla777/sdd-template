# AI Rules Harness — Directrices de Ejecución y Minimalismo

Este harness define las reglas operativas, los estándares de código y las directrices de ejecución de inteligencia artificial para cualquier agente que colabore en este repositorio. Opera bajo los dos pilares maestros de [AGENTS.md](../AGENTS.md):
1. **Ponytail**: Filosofía de minimalismo radical y máxima eficiencia ("lazy senior developer").
2. **SDD (Software-Design-Driven)**: El proceso gobierna el ciclo de vida (ningún código sin especificación previa en `specs/`).

---

## 1. Ponytail — Modo "Lazy Senior Dev"

Eres un senior developer perezoso: perezoso significa eficiente y quirúrgico, nunca descuidado. El mejor código es el que nunca se escribe. Menos código = menor superficie de bugs y menor coste de mantenimiento.

Antes de escribir una sola línea de código o introducir abstracciones, detente en el primer escalón que aplique:

1. **¿Esto necesita construirse?** (Principio **YAGNI** estricto: *You Aren't Gonna Need It*. Si no está explícitamente en los criterios de aceptación de la spec, no existe).
2. **¿Lo resuelve la librería estándar del lenguaje o framework?** Úsala de inmediato antes de buscar librerías de terceros o crear utilidades artesanales.
3. **¿Lo cubre una feature nativa de la plataforma o runtime?** Priorízala frente a capas añadidas.
4. **¿Lo resuelve una dependencia ya instalada?** Reutilízala.
5. **¿Puede resolverse en una línea clara?** Hazlo en una línea.
6. **Solo entonces**: Escribe el mínimo código indispensable que satisfaga la especificación.

### Reglas de Código Ponytail
- **Cero abstracciones no solicitadas**: Prohibido crear fábricas, interfaces genéricas abstractas, adaptadores o capas de indirección si no fueron solicitadas explícitamente en la spec. Tres líneas de código repetido son preferibles a una abstracción prematura.
- **Cero dependencias arbitrarias**: Queda terminantemente prohibido agregar nuevas librerías en `package.json`, `Cargo.toml`, `pyproject.toml` o `requirements.txt` sin justificación y aprobación formal en la spec.
- **Borrar mejor que añadir**: El código muerto, redundante o en desuso se elimina sin piedad. No mantengas hacks de retrocompatibilidad para código obsoleto.
- **Tipado seguro y estricto**: Prohibido el uso de `any`, `unknown` evasivo o tipos permisivos. Toda firma de función, contrato de API y componente debe contar con tipado explícito y verificable en tiempo de compilación.
- **Documentación de simplificaciones intencionales**: Si aplicas un atajo o simplificación deliberada para evitar complejidad accidental, documenta el límite con la etiqueta estándar:
  `// ponytail: [descripción del techo funcional y ruta de expansión futura]`

### Invariantes No Negociables (Aquí NUNCA se es perezoso)
- Validación estricta y exhaustiva en las fronteras de confianza del sistema (inputs de usuario, queries de base de datos, payloads de webhooks, llamadas a APIs externas).
- Manejo defensivo y seguro de errores que previene pérdida de datos, corrupción de estado o caídas del sistema.
- Seguridad robusta en autenticación, autorización y prevención activa de inyecciones (SQL, XSS, SSRF).
- Rendimiento base y cobertura de tests automatizados para el 100% de los criterios de aceptación y edge cases descritos en la spec.

---

## 2. Precedencia SDD y Exención de Micro-Spec

### Relación entre SDD y Ponytail
La estructura y compuertas del ciclo SDD ([harnesses/phase.md](phase.md)) son **inviolables**: el proceso manda. La filosofía Ponytail opera **dentro** de cada fase para evitar el sobre-diseño y optimizar tokens, pero nunca en contra de la gobernanza:
- En sitios estáticos o frontends puros: no introduzcas capas complejas de Clean Architecture de backend si no hay backend.
- En arquitecturas de producción: mantén las separaciones de dominio y módulos establecidas en [harnesses/structure.md](structure.md).

### Exención de Micro-Spec para Tier 1 (Small / Hotfix)
El principio fundamental de SDD establece que *ningún código se escribe sin especificación previa en `specs/`*. Sin embargo, para evitar fricción burocrática y reducir el consumo de tokens hasta en un 85%:
- **Para cambios Tier 1 (Small / Hotfix)**: Se autoriza y requiere la vía rápida mediante una **micro-spec** de **menos de 25 líneas** ([specs/_template_hotfix.md](../specs/_template_hotfix.md)). La micro-spec fusiona el diseño técnico en el mismo documento compacto, permitiendo implementación directa y verificación focalizada sin requerir la redacción separada de fases burocráticas de SPEC y DISEÑO.
- **Para cambios Tier 2 (Medium) y Tier 3 (Large / Epic)**: La especificación formal completa en [specs/_template.md](../specs/_template.md) o [specs/_template_epic.md](../specs/_template_epic.md) con fases diferenciadas de SPEC y DISEÑO es estrictamente obligatoria antes de iniciar implementación.

---

## 3. Reglas de Ejecución por Roles Agnósticos

Siguiendo la arquitectura de [harnesses/multi-agent.md](multi-agent.md), los agentes operan bajo directrices según su función:

### Reglas para el Orquestador y Modelos Tier 1 (Pro / Arquitectura)
- **Archivos para entrega de contenido, Mensajes para coordinación**: Escribe especificaciones, planes, decisiones y reportes directamente en disco (`specs/`, `harnesses/`, `.agents/teamwork/`). Los mensajes inter-agente deben ser concisos (`<300 palabras`).
- **Respuestas directas y de alta densidad**: Evita introducciones ceremoniales o resúmenes decorativos. Comunica directamente decisiones, trade-offs y comandos.
- **Resolución de ambigüedad**: Si un requerimiento o criterio es ambiguo en la spec, indaga y clarifica antes de asumir supuestos arquitectónicos.
- **Propuesta unívoca**: Al resolver dudas de diseño, presenta la solución recomendada con su trade-off principal, no un abanico indeterminado de opciones.

### Reglas para el Trabajador y Modelos Tier 2/3 (Fast / Local / Implementación)
- **Lectura completa de la spec**: Lee la especificación técnica completa y sus criterios de aceptación antes de escribir la primera línea de código.
- **Una tarea a la vez**: Respeta estrictamente los Result Contracts acotados por el Orquestador; no anticipes implementaciones futuras ni amplíes el alcance de la tarea en curso.
- **Confinamiento estricto de archivos**: Prohibido modificar o crear archivos fuera del inventario explícitamente delimitado en la spec.
- **Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)**: Si una prueba o comando de build falla, dispones de un máximo de 2 intentos consecutivos de corrección. Si el segundo intento falla, **detén la ejecución inmediatamente**, empaqueta el log de error textual y escala al Orquestador (Tier 1). Quedan prohibidos los bucles de reintento ciego.
- **Disyuntor de Escalación de Sizing**: Si durante una tarea Tier 1 descubres que requieres tocar más de 3 archivos, superar 80 LOC netas, o modificar un contrato/esquema público, detén la mutación y escala a Tier 2.

### Reglas para el Revisor y Forensic Auditor (Verificación y Gobernanza)
- **Validación de Criterios de Aceptación**: Verifica empíricamente que cada criterio de la spec cuenta con pruebas ejecutadas y aprobadas.
- **Inspección de Límite de PR (≤300 LOC)**: Todo cambio que supere las 300 líneas de código neto debe ser rechazado y devuelto para su subdivisión en PRs o slices menores.
- **VETO BINARIO del Forensic Auditor**: Rechazo inmediato e incondicional ante cualquier indicio de pruebas deshabilitadas, assertions mockeadas sin justificación, debilitamiento de tipos o dependencias no autorizadas.

---

## 4. Reglas de Commits y Pull Requests

### Convención de Commits
Los mensajes de commit siguen la especificación convencional en minúsculas, modo infinitivo y sin punto final:
```
tipo(alcance_opcional): descripción breve en infinitivo y minúsculas

Tipos permitidos:
feat:     Nueva funcionalidad o característica
fix:      Corrección de bug o anomalía
chore:    Tareas de mantenimiento (dependencias, configuración, scripts)
docs:     Actualización de documentación o especificaciones
test:     Adición o corrección de pruebas automatizadas
refactor: Modificación de código sin alteración funcional ni corrección de bug
```
*Ejemplos*:
- `fix(auth): correct token expiration calculation in session handler`
- `feat(api): add user profile endpoint handler`
- `chore(deps): bump typescript from 5.4.2 to 5.4.3`

### Reglas de Pull Requests
- **Límite de tamaño**: PRs de más de 300 líneas cambiadas deben dividirse obligatoriamente en partes más pequeñas (excepto lockfiles generados).
- **Enlace de especificación**: Todo PR debe referenciar explícitamente la spec técnica que implementa en `specs/`.
- **Estructura del PR**:
  1. *Qué hace* (1-2 oraciones claras).
  2. *Spec de referencia* (ruta relativa al archivo en `specs/`).
  3. *Comandos de prueba ejecutados* (evidencia de build y tests superados).

---

## 5. Lo que NUNCA se Hace (Violaciones Críticas de Gobernanza)

Quedan estrictamente prohibidas las siguientes acciones bajo apercibimiento de rechazo inmediato:

1. **Escribir código sin spec previa en `specs/`**: Todo cambio requiere una especificación formal (Tier 2/3) o una micro-spec de `<25 líneas` (Tier 1).
2. **Saltar la Fase 0: DISCOVERY en proyectos brownfield**: Prohibido alterar repositorios en producción sin la previa inspección de los 6 pilares de [harnesses/discovery.md](discovery.md).
3. **Omitir la Fase N: CLOSING**: Ninguna tarea se considera terminada sin el cierre formal de [harnesses/closing.md](closing.md) (PVS, actualización de [Engram.md](../Engram.md), spec marcada `COMPLETADA` y changelog).
4. **Utilizar tipos permisivos o evasivos**: Prohibido el uso de `any`, casts inseguros o bypasses de tipado.
5. **Introducir abstracciones prematuras o sobre-ingeniería**: Diseña para los requerimientos actuales según la regla Ponytail.
6. **Hacer un PR sin descripción o >300 LOC**: Sin evidencia de verificación ni límites de tamaño no hay merge.
7. **Pushear directamente a ramas principales**: Todo cambio entra a través de ramas de feature y compuertas de verificación.
8. **Modificar tests para ocultar fallos**: La alteración de pruebas para forzar un pase verde activa el VETO BINARIO del Forensic Auditor.

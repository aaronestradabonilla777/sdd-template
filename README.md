# SDD Template v2

Template agnóstico para desarrollar software con **Spec-Driven Development
(SDD)** y una integración opcional con **Receipt-Driven Development (RDD)**.

SDD conserva la intención como fuente de verdad: antes de implementar se define
qué se construye, por qué, qué queda fuera y cómo se comprobará. RDD añade una
capa posterior de revisión adversarial y autorización ligada al contenido exacto
del candidato.

```text
ANÁLISIS → SPEC → DISEÑO → IMPLEMENTACIÓN → RDD → VERIFICACIÓN → ENTREGA
```

## Qué resuelve cada capa

| Capa | Pregunta |
|---|---|
| SDD | ¿Estamos construyendo lo que se pidió? |
| Tests/TDD | ¿Las propiedades comprobadas se comportan correctamente? |
| RDD | ¿Existe evidencia suficiente para autorizar este candidato exacto? |
| Entrega | ¿El recibo todavía corresponde al contenido que se entregará? |

RDD no reemplaza SDD, tests, CI ni revisión humana. Un test verde es evidencia,
no autoridad total. Un resumen escrito por el mismo agente tampoco es un recibo.

## Principios

- Ninguna implementación nueva sin una spec aprobada.
- Los criterios de aceptación deben tener métodos de verificación observables.
- El implementador no puede autodeclarar aprobado su candidato.
- Una aprobación RDD pertenece a una huella exacta; cambiar el contenido exige
  una nueva autorización.
- El riesgo se deriva de las superficies afectadas y la evidencia, no únicamente
  del tamaño del diff.
- Sin una autoridad RDD nativa, el estado es `disabled/unmanaged`, nunca una
  aprobación simulada.
- Las políticas del repositorio siempre prevalecen sobre un recibo: RDD no
  concede permiso para hacer commit, push, PR o deploy.

## Flujo

### 1. Análisis

Investiga el contexto y los riesgos sin modificar el producto.

### 2. Spec

Crea una spec desde `specs/_template.md`. Debe incluir alcance, criterios con
IDs estables, edge cases, riesgos, invariantes, evidencia y fuera de scope.

### 3. Diseño

Define arquitectura, contratos, componentes, dependencias y decisiones
necesarias para satisfacer la spec aprobada.

### 4. Implementación

Implementa el candidato y ejecuta las comprobaciones ordinarias. Declara también
qué no pudo comprobarse.

### 5. RDD

Si existe una autoridad compatible, congela el candidato, deriva el esfuerzo de
revisión desde el riesgo, inspecciona, permite como máximo la corrección acotada
autorizada, valida esa corrección en read-only y emite un recibo ligado a la
huella. Consulta `harnesses/rdd.md`.

### 6. Verificación

Contrasta independientemente el resultado contra spec, diseño y criterios. Si
la verificación exige modificar el candidato, se vuelve a implementación y la
autorización anterior deja de gobernar el contenido nuevo.

### 7. Entrega

Valida el mismo recibo contra el candidato actual y aplica las políticas del
repositorio. La entrega puede continuar bajo política ordinaria cuando RDD esté
explícitamente deshabilitado, pero no debe presentarse como aprobada por RDD.

## Estructura

| Ruta | Responsabilidad |
|---|---|
| `AGENTS.md` / `CLAUDE.md` | Contexto para agentes compatibles |
| `Engram.md` | Memoria durable y decisiones del proyecto |
| `specs/` | Intención y criterios por feature |
| `harnesses/phase.md` | Máquina de fases |
| `harnesses/rdd.md` | Contrato de interoperabilidad y autoridad RDD |
| `harnesses/contract.md` | Definition of Done y contrato de evidencia |
| `harnesses/review.md` | Revisión basada en riesgo |
| `harnesses/isolation.md` | Separación de roles y capacidades |
| `harnesses/state.md` | Estado operativo, sin fabricar autoridad |
| `docs/` | ADRs y guías de migración |

## Compatibilidad

El template no obliga a usar un proveedor, modelo ni runtime concreto. Puede
utilizarse con Codex, Claude Code, Pi, OpenCode, Cursor u otros agentes que lean
instrucciones del repositorio.

La integración recomendada para recibos nativos es
[Gentle-AI](https://github.com/Gentleman-Programming/gentle-ai). Gentle-AI se
instala y configura por separado; este template no copia su implementación ni
fabrica sustitutos Markdown de sus recibos.

## Inicio rápido

1. Crea un repositorio desde este template.
2. Completa `AGENTS.md` o el archivo de contexto de tu agente.
3. Registra stack, comandos y decisiones iniciales en `Engram.md`.
4. Copia `specs/_template.md` para crear la primera feature.
5. Obtén aprobación explícita de la spec.
6. Sigue `harnesses/phase.md` hasta verificación y entrega.

### Gentle-AI opcional

Consulta primero los requisitos y versiones vigentes del proyecto upstream.
Después de instalarlo, la integración puede diagnosticarse y controlarse con:

```bash
gentle-ai doctor
gentle-ai review mode status --cwd .
gentle-ai review mode enable --cwd .
```

No se incluye una instalación automática para evitar ejecutar scripts remotos o
modificar configuraciones globales sin consentimiento.

## Migración desde v1

La versión 1 estaba orientada principalmente a Claude Code + OpenCode + Ollama.
La v2 reemplaza esa asignación fija por roles y capacidades, conserva SDD como
obligatorio e incorpora RDD como autoridad opcional. Sigue la guía
[`docs/migration-v2-rdd.md`](docs/migration-v2-rdd.md).

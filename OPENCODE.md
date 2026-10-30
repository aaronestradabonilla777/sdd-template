# OPENCODE.md — Local SLM Execution Rules

Entry point para OpenCode y Modelos Pequeños Locales (SLMs) vía Ollama (Qwen 2.5 Coder, DeepSeek Coder, Llama 3.1).
Optimizado para ventanas de contexto compactas (8k–32k tokens) y máxima fidelidad de ejecución.

---

## 1. Rol Permitido: Worker / Implementer (Tier 3 / Tier 2)

Operas exclusivamente como **Worker** (Implementador).
- Implementas código según el Result Contract de la spec asignada en `specs/`.
- Tareas permitidas: CRUD estándar, boilerplate, endpoints tipados, queries de base de datos, refactors simples y tests unitarios.
- **PROHIBIDO**:
  - Tomar decisiones arquitectónicas globales (reservado para Orchestrator / Tier 1).
  - Modificar specs o criterios de aceptación en `specs/` (para hacer pasar tests que fallan).
  - Modificar archivos en `harnesses/`, `docs/` o alterar `Engram.md`.
  - Agregar dependencias externas al proyecto sin autorización explícita en la spec.

---

## 2. Límites de Aislamiento y Scope

1. **Lee únicamente lo asignado**: Lee solo la spec asignada (`specs/<feature>.md` o `specs/_template_hotfix.md`) y los archivos fuente explícitamente indicados en el contrato.
2. **Una sola tarea atómica a la vez**: No anticipes pasos futuros. Modifica únicamente los archivos en el alcance del contrato.
3. **Sin archivos fuera de scope**: Si una tarea requiere tocar un archivo no declarado en el contrato, detén la ejecución y solicita autorización.

---

## 3. Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)

Para evitar bucles destructivos y degradación de código:

```
[Ejecutar cambio] ──► [Correr tests/build local]
                             │
            ┌────────────────┴───────────────┐
            ▼                                ▼
         [PASA]                           [FALLA]
            │                                │
      Emitir reporte                         ▼
      y terminar                   [Intento de arreglo #1]
                                             │
                             ┌───────────────┴───────────────┐
                             ▼                               ▼
                          [PASA]                          [FALLA]
                             │                               │
                       Emitir reporte                        ▼
                       y terminar                  [Intento de arreglo #2]
                                                             │
                                             ┌───────────────┴───────────────┐
                                             ▼                               ▼
                                          [PASA]                          [FALLA]
                                             │                               │
                                       Emitir reporte                        ▼
                                       y terminar                   🛑 STOP INMEDIATO
                                                                    (Disyuntor disparado)
```

**Regla Innegociable**:
Si tus modificaciones no compilan o fallan los tests locales tras **2 intentos consecutivos**, **DETÉNTE INMEDIATAMENTE**.
- No intentes un 3er arreglo.
- Vuelca el mensaje de error textual y el diff actual.
- Escala el bloqueo al Orquestador (Tier 1) o al desarrollador humano.

---

## 4. Filosofía Ponytail para Modelos Locales

1. **Mínimo código funcional**: La solución de 3 líneas siempre gana sobre la abstracción prematura.
2. **Biblioteca estándar primero**: Usa utilidades nativas del lenguaje antes de inventar helpers o pedir librerías.
3. **Cero `any`**: Tipado explícito y estricto.
4. **No comentes lo obvio**: Código autodocumentado; solo comenta razones de diseño críticas con `// ponytail: [razón]`.

---

## 5. Comandos Locales de Verificación

Ejecuta antes de dar por terminada la tarea:

```bash
# Formateo y linter (según stack del proyecto)
pnpm lint           # o npm run lint / ruff check / cargo clippy

# Ejecución de tests específicos del módulo modificado
pnpm test <path>    # o pytest <path> / cargo test <test_name>

# Build de verificación
pnpm build          # o cargo check / npm run build
```

---

## 6. Referencias

- Reglas maestras del proyecto: `AGENTS.md`
- Gobernanza multi-agente: `harnesses/multi-agent.md`
- Aislamiento y disyuntores: `harnesses/isolation.md`
- Guía de configuración de OpenCode: `docs/adapters/opencode.md`

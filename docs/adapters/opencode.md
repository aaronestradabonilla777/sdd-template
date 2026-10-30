# Guía de Adaptador: OpenCode y Modelos Locales (Ollama / SLMs)

Esta guía documenta la configuración y mejores prácticas para ejecutar `sdd-template` utilizando **OpenCode**, **Aider** o herramientas CLI locales conectadas a modelos pequeños de lenguaje (**SLMs**) alojados en local mediante **Ollama**, **vLLM** o **llama.cpp**.

---

## 1. Visión General y Ventajas de Modelos Locales

El uso de modelos locales en `sdd-template` proporciona:
- **Coste Cero de Tokens de Nube**: Cero gasto recurrente para tareas mecánicas repetitivas.
- **Privacidad Total**: El código fuente nunca abandona la máquina local del desarrollador (ideal para entornos con secretos corporativos o compliance estricto).
- **Baja Latencia de Red**: Ejecución sin depender de cuotas de API externas.

### Limitaciones de los SLMs y Mitigaciones en el Template
1. **Ventana de Contexto Acotada (8k a 32k tokens)**: Se mitiga utilizando el punto de entrada ultradenso `OPENCODE.md` (<75 líneas) y enviando únicamente la spec asignada y el archivo a editar.
2. **Capacidad de Razonamiento Acotada**: Los SLMs locales se restringen al rol de **Worker (Tier 3)** para tareas bien definidas (CRUD, boilerplate, tests unitarios). Nunca se les asigna el rol de Orchestrator o Challenger.
3. **Tendencia a Degradar en Bucles de Error**: Se mitiga mediante la aplicación estricta del **Disyuntor de 2 Intentos (2-Attempt Circuit Breaker)**.

---

## 2. Modelos Recomendados en Ollama

| Modelo | Parámetros | RAM / VRAM Requerida | Casos de Uso Recomendados |
|---|---|---|---|
| **Qwen 2.5 Coder** | **14B** (Q4_K_M) | ~9 GB VRAM | **Recomendado**: Excelente precisión sintáctica, soporte de TypeScript/Python/Rust, seguimiento de contratos. |
| **Qwen 2.5 Coder** | **7B** (Q4_K_M) | ~5 GB VRAM | Ligero, rápido; apto para laptops con GPU integrada o recursos limitados. |
| **DeepSeek Coder V2 Lite** | **16B** | ~11 GB VRAM | Muy fuerte en algoritmos matemáticos y resolución de bugs aislados. |
| **Llama 3.1** | **8B** Instruct | ~6 GB VRAM | Generación de tests unitarios y documentación puntual. |

---

## 3. Instalación y Configuración Paso a Paso

### Paso 1: Instalar Ollama
Descarga e instala Ollama desde `https://ollama.ai`.

### Paso 2: Descargar el Modelo Recomendado
En la terminal:
```bash
# Modelo principal recomendado para Worker
ollama pull qwen2.5-coder:14b

# Modelo alternativo ultra-rápido
ollama pull qwen2.5-coder:7b
```

### Paso 3: Configurar el Entorno de Ejecución Local
Si utilizas OpenCode CLI o Aider en modo local:
```bash
# Configuración para Aider apuntando a Ollama local
export OLLAMA_API_BASE=http://localhost:11434
aider --model ollama_chat/qwen2.5-coder:14b
```

---

## 4. Disciplina de Contexto para OpenCode

Para no desbordar el contexto del modelo local:

### Lo que DEBES incluir en la sesión local:
1. Las directivas de `OPENCODE.md`.
2. La especificación asignada (`specs/<feature>.md` o `specs/_template_hotfix.md`).
3. Únicamente los archivos de código fuente declarados en el Result Contract (máximo 1 a 3 archivos).

### Lo que NO debes incluir:
- No incluyas `Engram.md` completo ni el historial extenso de sesiones anteriores.
- No incluyas todos los archivos de `harnesses/` (basta con las referencias de `OPENCODE.md`).
- No pegues volcados masivos de logs de build o trazas de 500 líneas.

---

## 5. Implementación del Disyuntor de 2 Intentos

El disyuntor es obligatorio cuando OpenCode actúa como Worker:

```
[Código modificado por OpenCode]
               │
               ▼
   [Ejecutar comando de test local]
   (ej. pnpm test tests/feature.test.ts)
               │
       ┌───────┴───────┐
       ▼               ▼
     PASA            FALLA
       │               │
       │               ▼
       │     [Intento de Corrección 1]
       │     (Pasa solo el log de error exacto)
       │               │
       │       ┌───────┴───────┐
       │       ▼               ▼
       │     PASA            FALLA
       │       │               │
       │       │               ▼
       │       │     [Intento de Corrección 2]
       │       │               │
       │       │       ┌───────┴───────┐
       │       │       ▼               ▼
       │       │     PASA            FALLA
       │       │       │               │
       ▼       ▼       ▼               ▼
   [Tarea Completada con Éxito]   🛑 DISYUNTOR ACTIVADO
                                  - Detener la sesión local
                                  - No intentar 3er arreglo
                                  - Escalar a Tier 1 / Humano
```

**Comportamiento ante Activación**:
1. El agente local guarda el estado actual en disco.
2. Emite un archivo de log `error_summary.txt` con la traza exacta.
3. Se solicita la intervención del Orchestrator (Tier 1: Claude Code o Gemini Pro) o del desarrollador humano.

---

## 6. Flujo de Trabajo Práctico E2E con OpenCode

1. **Diseño previo (Tier 1 o Humano)**: Se aprueba la spec en `specs/004-user-profile.md` con su Result Contract.
2. **Invocación de OpenCode**:
   ```bash
   opencode --rules OPENCODE.md --spec specs/004-user-profile.md --files src/services/user.ts,tests/user.test.ts
   ```
3. **Implementación**: Qwen 2.5 Coder escribe el código mínimo y los tests correspondientes.
4. **Verificación**: Se ejecuta `pnpm test tests/user.test.ts`.
5. **Cierre**:
   - Si pasa: Commit `feat: implement user profile endpoint`.
   - Si falla 2 veces: El disyuntor detiene la ejecución y previene bucles infinitos.

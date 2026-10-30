# Guía de Adaptador: Anthropic Claude Code

Esta guía documenta la configuración, flujo de trabajo y optimizaciones avanzadas para ejecutar `sdd-template` mediante **Anthropic Claude Code CLI** (`claude`), utilizando los modelos **Claude 3.7 Sonnet**, **Claude 3.5 Sonnet** y **Claude 3.5 Haiku**.

---

## 1. Visión General de Claude Code CLI

Claude Code es una herramienta de interfaz de línea de comandos (CLI) desarrollada por Anthropic que opera directamente sobre repositorios locales con capacidades de:
- Edición precisa de archivos con diffs estructurados.
- Ejecución de comandos de terminal bash.
- Reconocimiento automático de contexto mediante `CLAUDE.md` y `AGENTS.md`.
- Soporte nativo de **Prompt Prefix Caching** (reducción masiva de latencia y costes).

---

## 2. Instalación y Configuración Inicial

### Prerrequisitos
- Node.js 18.0 o superior.
- Clave de API de Anthropic (`ANTHROPIC_API_KEY`).

### Instalación Global
```bash
npm install -g @anthropic-ai/claude-code
```

### Inicialización en el Proyecto
Navega a la raíz del repositorio clonado desde `sdd-template`:
```bash
cd tu-proyecto
claude
```
Al iniciar, Claude Code leerá automáticamente `CLAUDE.md` y cargará las directrices maestras de `AGENTS.md`.

---

## 3. Mapeo de Modelos y Estrategia de Costes (Regla 80/20)

Para cumplir la regla de eficiencia de tokens de `sdd-template`:

### Tier 1: Claude 3.7 Sonnet / Claude 3.5 Sonnet (Por Defecto)
- **Roles**: Orchestrator, Discovery Specialist, Challenger (Red Team), Forensic Auditor.
- **Cuándo Usarlo**:
  - Fase 0 (Discovery y auditoría de repositorios existentes).
  - Fase 1 y 2 (Redacción y diseño de specs complejas en `specs/`).
  - Fase 4 (Revisión adversarial y auditoría forense con veto).
- **Invocación**:
  ```bash
  claude
  ```

### Tier 2: Claude 3.5 Haiku (Modo Económico)
- **Roles**: Worker (Implementador), Reviewer (Linter / Estilo / Contratos), Explorer.
- **Cuándo Usarlo**:
  - Implementación de tareas mecánicas, endpoints CRUD y tests unitarios.
  - Revisión sintáctica de código y validación de límites de PR (<300 líneas).
  - Ahorro de ~80% en coste de tokens y latencia inferior a 2 segundos.
- **Invocación**:
  ```bash
  claude --model claude-3-5-haiku-20241022
  ```

---

## 4. Optimización de Prompt Prefix Caching

Anthropic aplica un descuento del 90% en tokens de entrada que coinciden con un prefijo previamente cacheado. Para maximizar este beneficio en `sdd-template`:

1. **Invarianza de Cabecera**: No modifiques `CLAUDE.md` con datos volátiles (como fechas o logs temporales). Debe permanecer idéntico entre sesiones.
2. **Lectura Bajo Demanda**: Permite que Claude lea `Engram.md` y los archivos de spec mediante herramientas de lectura (`View`, `Grep`) en lugar de pegarlos manualmente en el prompt inicial.
3. **Comando `/compact`**: Cuando la sesión interactiva sea extensa, ejecuta `/compact` dentro del CLI. Claude sintetizará el historial manteniendo intactas las directrices maestras de `CLAUDE.md`.
4. **Telemetría de Ahorro**: Verifica el ratio de tokens cacheados ejecutando `/cost` en cualquier momento de la sesión.

---

## 5. Estrategias de Aislamiento y Trabajo Multi-Agente en CLI

Dado que Claude Code opera en la terminal, el aislamiento de roles se implementa mediante dos técnicas recomendadas:

### Opción A: Aislamiento por Git Worktrees (Recomendada para Workers en Paralelo)
Permite que un subproceso Worker trabaje en un directorio físico separado sin interferir con la sesión del Orchestrator:
```bash
# Crear worktree para la tarea de implementación
git worktree add ../task-auth-worker -b feat/001-auth-worker

# Abrir sesión dedicada para el Worker
cd ../task-auth-worker
claude --model claude-3-5-haiku-20241022 -p "Implementar el Result Contract definido en specs/001-auth.md y correr pnpm test"
```

### Opción B: Ejecución Desatendida Bounded (`-p` / Print Mode)
Para implementar hotfixes o tareas acotadas de forma programática:
```bash
claude -p "Actúa como Worker. Lee specs/003-hotfix.md. Modifica únicamente los archivos listados en el contrato. Si los tests fallan 2 veces, deténte y reporta."
```

---

## 6. Checklist de Validación del Desarrollador en Claude Code

Antes de fusionar cualquier cambio implementado por Claude Code:
- [ ] ¿El cambio fue precedido por una spec en `specs/`?
- [ ] ¿El diff total es menor a 300 líneas (`git diff --stat`)?
- [ ] ¿Se ejecutaron los tests locales del proyecto con éxito?
- [ ] ¿Se respetó el disyuntor de 2 intentos en caso de errores?
- [ ] ¿Se ejecutó el protocolo de cierre de `harnesses/closing.md` y se actualizó `Engram.md`?

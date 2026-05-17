# Review Harness

## Antes de hacer PR

- [ ] ¿El código cumple todos los criterios de aceptación de la spec?
- [ ] ¿El PR tiene menos de 300 líneas? Si no, divídelo
- [ ] ¿Compila sin errores ni warnings?
- [ ] ¿Los edge cases de la spec están cubiertos?
- [ ] ¿`Engram.md` está actualizado con decisiones nuevas?
- [ ] ¿`harnesses/state.md` refleja el estado actual?

## Tamaño de PR
| Líneas  | Acción                     |
|---------|----------------------------|
| < 150   | OK, merge directo          |
| 150-300 | OK, pide review            |
| > 300   | Divide en PRs más pequeños |

## Commit
```
tipo: descripción en infinitivo, minúsculas, sin punto

feat | fix | chore | docs | refactor | test
```

## Descripción del PR
- Qué hace (1-2 líneas)
- Spec que implementa (link al archivo en `specs/`)
- Cómo probarlo

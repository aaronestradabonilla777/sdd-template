# Review Harness

## Antes de hacer PR

- [ ] ¿El código cumple todos los criterios de aceptación de la spec?
- [ ] ¿El PR tiene menos de 400 líneas? Si no, divídelo
- [ ] ¿Compila sin errores ni warnings relevantes?
- [ ] ¿Los edge cases de la spec están cubiertos?
- [ ] ¿El Engram.md está actualizado con decisiones nuevas?

## Tamaño de PR
| Líneas  | Acción                        |
|---------|-------------------------------|
| < 200   | OK, merge directo             |
| 200-400 | OK, pide review               |
| > 400   | Divide en PRs más pequeños    |

## Mensaje de commit
```
tipo: descripción corta en presente

Tipos: feat, fix, chore, docs, refactor, test
```

## Descripción del PR
- Qué hace este PR (1-2 líneas)
- Spec que implementa (link al archivo)
- Cómo probarlo

# Role Isolation Harness

Los roles se asignan por responsabilidad y capacidad, no por marca, modelo o
proveedor. Una misma herramienta puede cubrir varios roles en momentos
distintos, pero no debe autootorgarse autoridad.

## Roles

### Spec Author

Define intención, alcance, criterios, invariantes y riesgos. No implementa antes
de obtener aprobación.

### Designer

Traduce la spec aprobada a arquitectura y contratos trazables.

### Implementer

Produce código y tests dentro del alcance. No autoriza su propio candidato.

### Reviewer

Inspecciona el candidato inmutable desde uno o más lentes y aporta findings con
evidencia.

### Fixer

Aplica únicamente la corrección y presupuesto autorizados por RDD. No amplía el
scope para “mejorar” áreas adyacentes.

### Fix Validator

Opera en read-only. Reproduce el defecto y demuestra si la corrección satisface
la condición definida por cada finding.

### Delivery Authority

Valida huella, recibo y política antes de una entrega. No sustituye permisos del
usuario, protecciones de rama ni CI.

## Intentos

Los intentos ordinarios durante implementación no son correcciones RDD. Una vez
congelado el candidato, solo se permite la corrección acotada autorizada por la
transacción. Un fallo posterior escala; no inicia un loop indefinido.

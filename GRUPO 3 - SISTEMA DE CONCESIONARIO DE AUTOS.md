# GRUPO 3 — Sistema de concesionario de autos

**Integrante:** Pablo Herrera

**Descripción del sistema:** Aplicación de consola para la administración de un concesionario de vehículos: alta de unidades 0km y usadas, gestión de clientes, cotización y venta de vehículos, y control del historial comercial.

---

## MENÚ PRINCIPAL

```
1. Registrar cliente
2. Buscar / modificar cliente
3. Registrar vehículo 0km
4. Registrar compra de vehículo usado
5. Buscar / modificar vehículo
6. Consultar vehículos disponibles
7. Cotizar un vehículo usado
8. Realizar venta
9. Ver historial de ventas
10. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar cliente
**Qué hace:** Da de alta un cliente del concesionario.
**Datos que pide:** DNI, nombre, apellido, teléfono, correo electrónico y localidad.
**Validaciones:** No se puede registrar dos clientes con el mismo DNI.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 2. Buscar / modificar cliente
**Qué hace:** Busca un cliente y permite ver su ficha o editar sus datos.
**Datos que pide:** DNI o apellido. Si se elige modificar, el dato a cambiar.
**Qué muestra:** Datos personales del cliente y su historial de operaciones (vehículos que compró y vehículos que vendió al concesionario).

### 3. Registrar vehículo 0km
**Qué hace:** Da de alta una unidad nueva en el stock.
**Datos que pide:** Marca, modelo, año, tipo de vehículo (auto, camioneta, SUV, moto), tipo de combustible, color, número de chasis y precio de lista.
**Validaciones:** No se puede repetir el número de chasis.
**Qué muestra:** Confirmación con los datos cargados y el estado inicial del vehículo (Disponible).
**Persistencia:** Se guarda en el archivo JSON de vehículos.

### 4. Registrar compra de vehículo usado
**Qué hace:** Registra la compra de un vehículo usado a un cliente y lo incorpora al stock para su reventa.
**Datos que pide:** Cliente al que se le compra, marca, modelo, año, kilometraje, tipo de vehículo, combustible, patente, estado general de la unidad y monto pagado.
**Cómo funciona:** El sistema sugiere un valor de compra a partir de la cotización calculada (ver opción 7) y el usuario confirma o modifica el monto final.
**Qué muestra:** Comprobante de la compra y la ficha del vehículo ingresado al stock.

### 5. Buscar / modificar vehículo
**Qué hace:** Busca una unidad puntual y permite ver su ficha o editar sus datos.
**Datos que pide:** Patente, número de chasis, marca o modelo. Si se elige modificar, el dato a cambiar (precio, color, estado, etc.).
**Qué muestra:** Ficha completa del vehículo: datos técnicos, si es 0km o usado, kilometraje, precio de venta y estado actual.

### 6. Consultar vehículos disponibles
**Qué hace:** Lista las unidades en stock listas para vender.
**Datos que pide:** Opcionalmente, filtros por tipo de vehículo, marca, rango de precio o condición (0km / usado).
**Qué muestra:** Marca, modelo, año, kilometraje, condición y precio de cada unidad, en pesos y su equivalente en dólares según la cotización obtenida de la API.

### 7. Cotizar un vehículo usado
**Qué hace:** Calcula cuánto vale un vehículo usado. Es la operación que le da criterio comercial al sistema.
**Datos que pide:** Marca, modelo, año, kilometraje y estado general de la unidad.
**Cómo calcula:** Parte del valor de referencia del modelo y le aplica depreciación por antigüedad, por kilometraje y por estado de conservación. El resultado se muestra en pesos y en dólares, usando la cotización traída de la API.
**Qué muestra:** El valor estimado con el desglose de cada descuento aplicado.
**Para qué sirve:** Se usa tanto al comprarle un vehículo a un cliente (opción 4) como al fijar el precio de reventa.

### 8. Realizar venta
**Qué hace:** Registra la venta de un vehículo a un cliente. Es la operación principal del sistema.
**Datos que pide:**
- Cliente (existente o nuevo).
- Vehículo a vender (seleccionado entre los disponibles).
- Forma de pago: contado, financiado o con permuta (el cliente entrega su usado como parte de pago).
**Cómo calcula el precio final:**
- **Contado:** precio del vehículo con descuento.
- **Financiado:** precio con recargo por interés, mostrando el valor de cada cuota.
- **Permuta:** al precio del vehículo se le descuenta la cotización del usado que entrega el cliente, y ese usado ingresa al stock automáticamente.
**Validaciones:** El vehículo debe estar disponible. Al confirmar, su estado pasa a Vendido y deja de aparecer en el stock.
**Qué muestra:** Comprobante con número de operación, cliente, vehículo, forma de pago, detalle del cálculo y monto final.
**Persistencia:** Se guarda en el archivo JSON de ventas.

### 9. Ver historial de ventas
**Qué hace:** Lista las ventas realizadas.
**Datos que pide:** Un criterio de filtro: por fecha, por cliente o por forma de pago.
**Qué muestra:** Número de operación, fecha, cliente, vehículo vendido, forma de pago y monto. Si el vehículo era usado, muestra además a cuánto lo había comprado el concesionario y la ganancia obtenida en la reventa.

### 10. Reportes
**Qué hace:** Muestra un submenú con la información comercial del concesionario.
**Reportes que debe incluir como mínimo:**
- Ventas y facturación de un período, en pesos y en dólares.
- Comparación entre ventas de 0km y de usados.
- Marcas y modelos más vendidos.
- Valor total del stock disponible.
- Ganancia obtenida en la reventa de usados.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Vehiculo` como clase base, con `Vehiculo0km` y `VehiculoUsado` heredando de ella. El usado agrega kilometraje, patente y estado de conservación. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo: el 0km toma el precio de lista, mientras que el usado calcula su valor aplicando depreciación por antigüedad, kilometraje y estado. |
| **Composición** | La `Venta` está compuesta por su detalle: forma de pago, cuotas y, si corresponde, el vehículo entregado en permuta. |
| **Enumeraciones** | `TipoVehiculo` (Auto, Camioneta, SUV, Moto), `Combustible` (Nafta, Diesel, GNC, Híbrido, Eléctrico), `EstadoVehiculo` (Disponible, Reservado, Vendido) y `FormaPago` (Contado, Financiado, Permuta). |
| **Listas** | Colecciones de clientes, vehículos y ventas. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de clientes, vehículos y ventas en archivos `.json`. |
| **API externa** | Consulta de la cotización del dólar para mostrar y calcular los precios de los vehículos, que en el mercado argentino se manejan en esa moneda. |
| **Menú de consola** | El menú detallado en este documento. |

# GRUPO 2 — Sistema de venta de mates

**Integrantes:** Franco Di Stilio — Genaro Lencioni — Benjamin Retta

**Descripción del sistema:** Aplicación de consola para la gestión de un emprendimiento de venta de mates cincelados. Permite administrar el catálogo de modelos, tomar pedidos de mates de catálogo o personalizados, controlar el estado de los encargues y llevar el registro de ventas.

---

## MENÚ PRINCIPAL

```
1. Cargar nuevo modelo
2. Modificar producto del catálogo
3. Eliminar producto del catálogo
4. Ver catálogo
5. Buscar producto
6. Registrar cliente
7. Ver clientes
8. Vender
9. Ver pedidos
10. Cambiar estado de un pedido
11. Control de stock
12. Estadísticas
```

---

## DETALLE DE CADA OPCIÓN

### 1. Cargar nuevo modelo
**Qué hace:** Da de alta un modelo de mate en el catálogo.
**Datos que pide:** Código, nombre del modelo, material (elegido de una lista fija: calabaza, madera, algarrobo, acero, vidrio), tipo de cincelado, descripción, precio y cantidad en stock.
**Validaciones:** No se puede repetir el código de producto.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON del catálogo.

### 2. Modificar producto del catálogo
**Qué hace:** Permite editar los datos de un modelo ya cargado.
**Datos que pide:** Código del producto y el dato a modificar (precio, descripción, stock, etc.).
**Qué muestra:** El producto con los datos actualizados.

### 3. Eliminar producto del catálogo
**Qué hace:** Da de baja un modelo del catálogo.
**Datos que pide:** Código del producto y confirmación.
**Validaciones:** No se puede eliminar un producto que tenga pedidos pendientes de entrega. En ese caso, el sistema debe avisar el motivo.
**Qué muestra:** Confirmación de la baja.

### 4. Ver catálogo
**Qué hace:** Lista todos los productos disponibles.
**Datos que pide:** Opcionalmente, un filtro por material o por rango de precio.
**Qué muestra:** Código, nombre, material, tipo de cincelado, precio y stock disponible de cada producto.

### 5. Buscar producto
**Qué hace:** Busca un producto puntual.
**Datos que pide:** Código o nombre (o parte del nombre).
**Qué muestra:** Ficha completa del producto: datos, precio, stock y cantidad de unidades vendidas.

### 6. Registrar cliente
**Qué hace:** Da de alta un cliente.
**Datos que pide:** Nombre, apellido, teléfono, correo electrónico y localidad de envío.
**Validaciones:** No se pueden repetir clientes con el mismo teléfono o correo.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 7. Ver clientes
**Qué hace:** Lista los clientes registrados.
**Qué muestra:** Datos de contacto, cantidad de compras realizadas y monto total gastado por cada cliente.

### 8. Vender
**Qué hace:** Registra una venta. Es la operación principal del sistema.
**Datos que pide:**
- Cliente (existente o nuevo).
- Tipo de venta: **producto del catálogo** o **mate personalizado**.
- Si es del catálogo: código del producto y cantidad.
- Si es personalizado: material, tipo de cincelado, si lleva nombre o diseño grabado, y demás detalles del encargue.
- Medio de pago (efectivo, transferencia o tarjeta).
**Cómo calcula el precio:**
- **Producto del catálogo:** precio de lista por la cantidad.
- **Mate personalizado:** precio base del material más un adicional según el nivel de detalle del cincelado y los extras solicitados (grabado de nombre, diseño a pedido, etc.).
El medio de pago también afecta el total (por ejemplo, descuento por transferencia o recargo por tarjeta).
**Validaciones:** Para productos del catálogo debe haber stock suficiente. Al confirmar la venta, el stock se descuenta automáticamente.
**Qué muestra:** Comprobante con número de pedido, cliente, detalle de lo vendido, precio final y estado inicial del pedido.
**Persistencia:** Se guarda en el archivo JSON de ventas.

### 9. Ver pedidos
**Qué hace:** Lista los pedidos registrados.
**Datos que pide:** Un criterio de filtro: por estado, por cliente o por fecha.
**Qué muestra:** Número de pedido, fecha, cliente, detalle, monto y estado actual.

### 10. Cambiar estado de un pedido
**Qué hace:** Actualiza en qué instancia está un pedido. Es especialmente importante para los mates personalizados, que llevan días de trabajo.
**Datos que pide:** Número de pedido y nuevo estado.
**Estados posibles:** Pendiente → En proceso → Terminado → Entregado. También puede pasar a Cancelado.
**Validaciones:** No se puede volver a un estado anterior ni modificar un pedido ya entregado o cancelado.
**Qué muestra:** Confirmación del cambio con la fecha en que se registró.

### 11. Control de stock
**Qué hace:** Permite ver y reponer el stock de los productos.
**Qué muestra:** Listado de productos con su stock actual, destacando los que están por debajo del mínimo o agotados.
**Datos que pide:** Para reponer, el código del producto y la cantidad a agregar.

### 12. Estadísticas
**Qué hace:** Muestra la información comercial del emprendimiento.
**Reportes que debe incluir como mínimo:**
- Total de ventas y dinero recaudado en un período.
- Productos más vendidos.
- Comparación entre ventas de catálogo y ventas personalizadas.
- Pedidos pendientes de entrega.
- **Total recaudado convertido a dólares**, usando la cotización obtenida de una API.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Producto` como clase base, con `MateCatalogo` y `MatePersonalizado` heredando de ella. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de producto: precio de lista en el mate de catálogo y precio calculado por material, nivel de detalle y extras en el personalizado. |
| **Composición** | El `MatePersonalizado` está compuesto por los detalles o extras del encargue (grabado, diseño a pedido, etc.): sin el mate, esos detalles no existen. La `Venta` también está compuesta por sus líneas de detalle. |
| **Enumeraciones** | `Material` (Calabaza, Madera, Algarrobo, Acero, Vidrio), `EstadoPedido` (Pendiente, EnProceso, Terminado, Entregado, Cancelado) y `MedioDePago`. |
| **Listas** | Colecciones de productos, clientes y ventas. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia del catálogo, los clientes y las ventas en archivos `.json`. |
| **API externa** | Consulta de la cotización del dólar para mostrar la recaudación convertida y para actualizar precios de referencia. |
| **Menú de consola** | El menú detallado en este documento. |


# GRUPO 15 — Sistema de gestión de planta de fertilizantes

**Integrante:** Patricio Insúa

**Descripción del sistema:** Aplicación de consola para la administración de una planta que produce y compra fertilizantes: control de stock, compra a proveedores, venta a clientes, generación de cupos diarios de carga y gestión de los camiones que los clientes envían a retirar la mercadería.

---

## MENÚ PRINCIPAL

```
1. Registrar producto
2. Compra de producto
3. Stock
4. Registrar cliente
5. Venta de producto
6. Ventas totales y reportes
7. Generación de cupos
8. Cupos por día
9. Autorización y despacho de camiones
10. Información sobre camiones recibidos
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar producto
**Qué hace:** Da de alta un fertilizante en el sistema. Puede ser de dos tipos.
**Fertilizante simple:** Un producto único (urea, fosfato diamónico, superfosfato, etc.).
**Mezcla:** Un producto elaborado en la planta, **compuesto por varios fertilizantes simples en determinadas proporciones**.
**Datos que pide:**
- Código, nombre y unidad de medida (kilogramos o toneladas).
- Si es simple: precio por tonelada y stock mínimo.
- Si es mezcla: qué fertilizantes simples la componen y en qué porcentaje cada uno, más el costo de elaboración.
**Validaciones:** No se puede repetir el código. En una mezcla, los porcentajes deben sumar 100% y todos los componentes deben existir.
**Qué muestra:** Ficha del producto. En el caso de las mezclas, muestra el costo de cada componente y el precio final calculado.
**Persistencia:** Se guarda en el archivo JSON de productos.

### 2. Compra de producto
**Qué hace:** Registra la compra de fertilizante a un proveedor y lo ingresa al stock.
**Datos que pide:** Proveedor, producto, cantidad en toneladas, precio unitario, moneda (pesos o dólares) y fecha.
**Cómo funciona:** Si la compra es en dólares, el sistema **consulta la cotización a una API** y registra también el equivalente en pesos, que es como se comercializa el fertilizante en el mercado.
**Validaciones:** No se puede superar la capacidad de almacenamiento de la planta.
**Qué muestra:** Comprobante de la compra con el costo total en ambas monedas y el stock actualizado del producto.
**Persistencia:** Se guarda en el archivo JSON de movimientos.

### 3. Stock
**Qué hace:** Muestra el estado del depósito.
**Datos que pide:** Opcionalmente, un filtro por tipo de producto (simples / mezclas).
**Qué muestra:** Cada producto con su stock actual en toneladas, stock comprometido (vendido pero todavía no retirado), stock realmente disponible para vender y su valor total. Destaca los productos por debajo del stock mínimo.
**Por qué importa el stock comprometido:** Una venta con cupo pendiente ya tiene mercadería reservada aunque el camión no haya llegado. Si no se descuenta, la planta puede vender dos veces la misma tonelada.

### 4. Registrar cliente
**Qué hace:** Da de alta un cliente que compra fertilizante.
**Datos que pide:** CUIT, razón social, nombre de contacto, teléfono, localidad y condición de pago (contado o cuenta corriente).
**Validaciones:** No se puede repetir el CUIT.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 5. Venta de producto
**Qué hace:** Registra la venta de fertilizante a un cliente. Es la operación que después genera los cupos de carga.
**Datos que pide:** Cliente, producto, cantidad en toneladas, precio por tonelada, moneda y condición de pago.
**Cómo calcula el precio:** Depende del tipo de producto:
- **Fertilizante simple:** precio de lista por tonelada.
- **Mezcla:** costo de los componentes según sus proporciones más el costo de elaboración y el margen de la planta.
Si la operación es en dólares, se convierte con la cotización obtenida de la API.
**Validaciones:** Debe haber stock disponible suficiente. Al confirmar, la cantidad vendida pasa a ser **stock comprometido** hasta que el cliente la retire.
**Qué muestra:** Comprobante con número de venta, cliente, producto, toneladas, precio total en ambas monedas y toneladas pendientes de retiro.
**Persistencia:** Se guarda en el archivo JSON de ventas.

### 6. Ventas totales y reportes
**Qué hace:** Lista las ventas registradas.
**Datos que pide:** Un criterio de filtro: por fecha, por cliente o por producto.
**Qué muestra:** Número de venta, fecha, cliente, producto, toneladas vendidas, toneladas ya retiradas, toneladas pendientes y monto. Al final, el total facturado del período.
**Resumen que debe incluir al pie:**
- Toneladas compradas, vendidas y despachadas del período.
- Facturación en pesos y en dólares.
- Productos más vendidos y clientes con mayor volumen.
- Cupos otorgados, utilizados y vencidos.

### 7. Generación de cupos
**Qué hace:** Genera los cupos de carga para que un cliente pueda mandar sus camiones a retirar lo que compró.
**Datos que pide:** Número de venta, fecha para la que se otorga el cupo y cantidad de camiones.
**Cómo funciona:** Cada cupo habilita a un camión a cargar una cantidad determinada de toneladas en una fecha específica. El sistema verifica que la planta tenga **capacidad de despacho disponible ese día** (una planta no puede cargar cantidad ilimitada de camiones por jornada).
**Validaciones:**
- Las toneladas de los cupos no pueden superar lo que el cliente compró y aún no retiró.
- No se puede superar el cupo máximo de camiones por día de la planta.
- La fecha no puede ser anterior a la actual ni caer en un día no laborable.
**Qué muestra:** Los cupos generados con su número, fecha, cliente, producto y toneladas habilitadas.
**Persistencia:** Se guarda en el archivo JSON de cupos.

### 8. Cupos por día
**Qué hace:** Muestra la agenda de carga de la planta.
**Datos que pide:** Una fecha (por defecto, el día actual).
**Qué muestra:** Todos los cupos de esa jornada con su estado (**pendiente, utilizado, vencido o cancelado**), el cliente, el producto y las toneladas. Al final, el resumen: cupos otorgados, cupos disponibles y toneladas comprometidas para ese día.

### 9. Autorización y despacho de camiones
**Qué hace:** Gestiona el camión desde que llega a la planta hasta que sale cargado. Es la operación central de la logística.
**Paso 1 — Autorización:** Se ingresa la patente del camión, el chofer y el número de cupo. El sistema verifica que el cupo exista, sea de esa fecha y no haya sido utilizado. Si todo está en orden, autoriza el ingreso; si no, rechaza el camión e informa el motivo.
**Paso 2 — Despacho:** Se registra el producto y las toneladas efectivamente cargadas, que no pueden superar las habilitadas por el cupo.
**Qué muestra:** Remito de despacho con patente, chofer, cliente, producto, toneladas cargadas y número de cupo. Al confirmar, se descuenta el stock real, el cupo pasa a *utilizado* y se actualizan las toneladas pendientes de la venta.

### 10. Información sobre camiones recibidos
**Qué hace:** Muestra el historial de camiones que pasaron por la planta.
**Datos que pide:** Un criterio de filtro: por fecha, por cliente, por patente o por estado.
**Qué muestra:** Patente, chofer, cliente, producto, toneladas cargadas, horario de ingreso y de salida, y si fue despachado o rechazado (con el motivo).


---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Producto` como clase base, con `FertilizanteSimple` y `Mezcla` heredando de ella. También puede aplicarse `Movimiento` → `Compra` / `Venta`. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de producto: el fertilizante simple toma su precio de lista, mientras que la mezcla calcula el suyo a partir de las proporciones de sus componentes más el costo de elaboración. |
| **Composición** | La `Mezcla` está compuesta por sus componentes con sus porcentajes: sin la mezcla, esa composición no existe. Es la relación más clara del sistema y debe verse en el UML. |
| **Enumeraciones** | `TipoProducto` (Simple, Mezcla), `EstadoCupo` (Pendiente, Utilizado, Vencido, Cancelado), `EstadoCamion` (Autorizado, EnPlanta, Cargado, Despachado, Rechazado), `UnidadMedida` y `Moneda`. |
| **Listas** | Colecciones de productos, clientes, compras, ventas, cupos y despachos. Los componentes de cada mezcla también se manejan con una lista. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de productos, clientes, movimientos, ventas, cupos y despachos en archivos `.json`. |
| **API externa** | Consulta de la cotización del dólar, ya que el fertilizante se comercializa en esa moneda y los precios deben expresarse en ambas. |
| **Menú de consola** | El menú detallado en este documento. |


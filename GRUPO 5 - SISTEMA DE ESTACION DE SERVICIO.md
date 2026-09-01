# GRUPO 5 — Sistema de estación de servicio

**Integrantes:** Lucas Caro — Joaquín Rial — Aaron Pages

**Descripción del sistema:** Aplicación de consola para la administración de una estación de servicio con kiosco. El sistema cuenta con un inicio de sesión que muestra un menú distinto según el rol del empleado: el playero opera la venta de combustible y el kiosquero opera la venta de productos.

---

## PANTALLA DE INICIO DE SESIÓN

Al abrir el programa, lo primero que se muestra es el login.

**Datos que pide:** Nombre de usuario y contraseña.
**Cómo funciona:** El sistema busca al empleado en el archivo JSON de usuarios y, según su rol, muestra el menú correspondiente. Si los datos son incorrectos, avisa y vuelve a pedirlos.
**Qué muestra al ingresar:** Un saludo con el nombre del empleado, su rol y la fecha y hora de inicio del turno.

```
Usuario: ______
Contraseña: ______
```

**Aclaración:** los empleados vienen cargados en el archivo JSON. No hace falta una pantalla para darlos de alta desde el sistema.

---

## MENÚ PLAYERO

```
1. Cargar combustible
2. Consultar y actualizar precios
3. Consultar nivel de tanques
4. Reponer combustible en tanques
5. Ver ventas de mi turno
0. Cerrar sesión
```

### 1. Cargar combustible
**Qué hace:** Registra la carga de combustible a un vehículo. Es la operación principal del playero.
**Datos que pide:** Surtidor, tipo de combustible, cantidad de litros (o monto en pesos, y el sistema calcula los litros), patente del vehículo y medio de pago.
**Cómo calcula:** Litros por el precio vigente del combustible. El medio de pago puede modificar el total (por ejemplo, descuento con app o recargo con tarjeta).
**Validaciones:** El tanque debe tener combustible suficiente. Al confirmar la carga, se descuenta automáticamente del tanque.
**Qué muestra:** Ticket con número de venta, combustible, litros, precio por litro, total y empleado que la realizó.
**Persistencia:** Se guarda en el archivo JSON de ventas.

### 2. Consultar y actualizar precios
**Qué hace:** Muestra la lista de precios vigente y permite modificarla.
**Cómo funciona:** El sistema **consulta los precios de referencia a una API** y los ofrece como sugerencia; el empleado confirma o carga un valor propio.
**Qué muestra:** Cada tipo de combustible con su precio por litro, el precio de referencia obtenido de la API y la fecha de la última actualización.
**Persistencia:** Se guarda en el archivo JSON de configuración.

### 3. Consultar nivel de tanques
**Qué hace:** Muestra cuánto combustible queda en cada tanque.
**Qué muestra:** Tanque, tipo de combustible, capacidad total, litros disponibles y porcentaje de ocupación, destacando los que están por debajo del nivel mínimo.

### 4. Reponer combustible en tanques
**Qué hace:** Registra el ingreso de combustible al depósito cuando llega el camión.
**Datos que pide:** Tanque, cantidad de litros ingresados y costo de compra.
**Validaciones:** No se puede superar la capacidad máxima del tanque.
**Qué muestra:** Nivel del tanque antes y después de la reposición.

### 5. Ver ventas de mi turno
**Qué hace:** Muestra únicamente las ventas realizadas por el empleado que inició sesión.
**Qué muestra:** Listado de cargas con horario, combustible, litros y monto, más el total recaudado y los litros vendidos por tipo de combustible en el turno.

---

## MENÚ KIOSQUERO

```
1. Realizar venta
2. Consultar productos
3. Agregar producto
4. Consultar stock
5. Ver ventas de mi turno
0. Cerrar sesión
```

### 1. Realizar venta
**Qué hace:** Registra la venta de uno o varios productos del kiosco.
**Datos que pide:** Código o nombre de cada producto, cantidad y medio de pago.
**Cómo calcula:** Suma el precio de cada producto por su cantidad. El medio de pago puede modificar el total.
**Validaciones:** Debe haber stock suficiente de cada producto. Al confirmar, el stock se descuenta automáticamente.
**Qué muestra:** Ticket con el detalle de los productos, el total y el empleado que realizó la venta.
**Persistencia:** Se guarda en el archivo JSON de ventas.

### 2. Consultar productos
**Qué hace:** Lista los productos del kiosco.
**Datos que pide:** Opcionalmente, un filtro por categoría (bebidas, golosinas, cigarrillos, comestibles, accesorios para el auto) o una búsqueda por nombre.
**Qué muestra:** Código, nombre, categoría, precio y stock disponible.

### 3. Agregar producto
**Qué hace:** Da de alta un producto nuevo o repone stock de uno existente.
**Datos que pide:** Código, nombre, categoría, precio y cantidad. Si el código ya existe, el sistema ofrece sumar la cantidad al stock actual.
**Validaciones:** El precio y la cantidad no pueden ser negativos.
**Persistencia:** Se guarda en el archivo JSON de productos.

### 4. Consultar stock
**Qué hace:** Muestra el estado del stock del kiosco.
**Qué muestra:** Listado de productos con su stock actual, destacando los que están por debajo del mínimo o agotados.

### 5. Ver ventas de mi turno
**Qué hace:** Muestra únicamente las ventas realizadas por el empleado que inició sesión.
**Qué muestra:** Listado de ventas con horario, detalle y monto, más el total recaudado y los productos más vendidos en el turno.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | Dos jerarquías: `Empleado` como clase base con `Playero` y `Kiosquero` (cada uno con su menú y sus permisos), y `Producto` como clase base con `Combustible` y `ProductoKiosco`. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de producto: el combustible se cobra por litro y el producto de kiosco por unidad. También puede usarse una interfaz para definir el menú que ve cada rol. |
| **Composición** | La `Estacion` está compuesta por sus tanques y surtidores. La `Venta` está compuesta por sus líneas de detalle. |
| **Enumeraciones** | `TipoCombustible` (Súper, Premium, Diésel, Diésel Premium, GNC), `Rol` (Playero, Kiosquero), `CategoriaProducto` y `MedioDePago`. |
| **Listas** | Colecciones de empleados, productos, tanques y ventas. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. La contraseña del empleado es el ejemplo más claro: solo se puede validar, nunca leer desde afuera. |
| **JSON** | Persistencia de usuarios, productos, tanques y ventas en archivos `.json`. |
| **API externa** | Consulta de precios de referencia de combustibles (o de la cotización del dólar) para actualizar la lista de precios. |
| **Menú de consola** | Los dos menús detallados en este documento, con el login como punto de entrada. |


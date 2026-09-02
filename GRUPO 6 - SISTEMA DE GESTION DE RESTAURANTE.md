# GRUPO 6 — Sistema de gestión de restaurante

**Integrantes:** Francisco Colombo — Victoria Figueroa

**Descripción del sistema:** Aplicación de consola para la administración de un restaurante. El sistema cuenta con un inicio de sesión que muestra un menú distinto según el rol del empleado: el **mesero** administra las mesas, toma los pedidos y cobra las cuentas, y el **cocinero** administra la carta y la comanda de la cocina.

---

## PANTALLA DE INICIO DE SESIÓN

Al abrir el programa, lo primero que se muestra es el login.

**Datos que pide:** Nombre de usuario y contraseña.
**Cómo funciona:** El sistema busca al empleado en el archivo JSON de usuarios y, según su rol, muestra el menú correspondiente. Si los datos son incorrectos, avisa y vuelve a pedirlos.
**Qué muestra al ingresar:** Un saludo con el nombre del empleado, su rol y la hora de inicio del turno.

```
Usuario: ______
Contraseña: ______
```

**Aclaración:** los empleados vienen cargados en el archivo JSON. No hace falta una pantalla para darlos de alta desde el sistema.

---

## MENÚ MESERO

```
1. Abrir mesa
2. Ver estado de las mesas
3. Ver carta
4. Tomar pedido
5. Consultar estado del pedido
6. Consultar cuenta
7. Cobrar y cerrar mesa
0. Cerrar sesión
```

### 1. Abrir mesa
**Qué hace:** Registra la llegada de un grupo de clientes y ocupa una mesa.
**Datos que pide:** Número de mesa, **cantidad de comensales**, nombre de contacto del cliente y si tiene reserva.
**Validaciones:** La mesa debe estar libre y su capacidad tiene que alcanzar para la cantidad de personas.
**Qué muestra:** Confirmación con el número de mesa, los comensales, el mesero a cargo y la hora de apertura.
**Persistencia:** Se guarda en el archivo JSON de mesas.

### 2. Ver estado de las mesas
**Qué hace:** Muestra el salón completo de un vistazo.
**Qué muestra:** Cada mesa con su capacidad y su estado (**libre, ocupada, esperando la cuenta**). Si está ocupada, muestra la cantidad de comensales, el mesero a cargo, hace cuánto está abierta y el consumo acumulado hasta el momento.

### 3. Ver carta
**Qué hace:** Lista los platos y bebidas disponibles.
**Datos que pide:** Opcionalmente, un filtro por categoría (entrada, plato principal, postre, bebida) o por tipo.
**Qué muestra:** Código, nombre, categoría, descripción, precio y disponibilidad de cada ítem. Los ítems marcados como sin stock por la cocina aparecen destacados y no se pueden pedir.

### 4. Tomar pedido
**Qué hace:** Carga lo que pide la mesa. Sirve tanto para el pedido inicial como para los pedidos adicionales que se agregan durante la comida.
**Datos que pide:** Número de mesa, ítems de la carta y cantidad de cada uno, más las observaciones para la cocina (sin sal, sin TACC, punto de cocción).
**Cómo funciona:** Los platos se envían a la comanda de la cocina en estado *Pendiente*. Las bebidas no pasan por cocina: se sirven directamente y quedan marcadas como *Servidas*.
**Validaciones:** La mesa debe estar abierta y el ítem debe estar disponible.
**Qué muestra:** Comprobante del pedido con el número asignado, los ítems cargados y el subtotal acumulado de la mesa.
**Persistencia:** Se guarda en el archivo JSON de pedidos.

### 5. Consultar estado del pedido
**Qué hace:** Permite saber cómo viene la cocina con lo que pidió una mesa.
**Datos que pide:** Número de mesa o número de pedido.
**Qué muestra:** Cada ítem del pedido con su estado actual (**pendiente, en preparación, listo o servido**) y hace cuánto tiempo fue solicitado. Destaca los que ya están listos para retirar de la cocina.

### 6. Consultar cuenta
**Qué hace:** Muestra el detalle de consumo de una mesa sin cerrarla.
**Datos que pide:** Número de mesa.
**Qué muestra:** Todos los pedidos de la mesa con sus ítems y precios, el subtotal, y la opción de ver el **total dividido por comensal**.

### 7. Cobrar y cerrar mesa
**Qué hace:** Cobra la cuenta, libera la mesa y cierra la atención.
**Datos que pide:** Número de mesa, medio de pago y si se divide la cuenta entre los comensales.
**Cómo calcula el total:** Suma todos los ítems consumidos. Cada tipo de ítem aporta su precio según su propia lógica: un plato a la carta cobra su precio de lista más los adicionales o guarniciones, un **menú del día** cobra un precio fijo que ya incluye entrada, principal y bebida, y las bebidas se cobran por unidad. Sobre el total se aplica el ajuste del medio de pago.
**Validaciones:** No se puede cerrar una mesa con pedidos que la cocina todavía no entregó.
**Qué muestra:** Ticket final con el detalle completo, el desglose del total y, si corresponde, el monto que paga cada comensal. La mesa vuelve a estado *libre*.

---

## MENÚ COCINERO

```
1. Ver comanda
2. Cambiar estado de un pedido
3. Cargar plato a la carta
4. Ver carta y marcar disponibilidad
0. Cerrar sesión
```

### 1. Ver comanda
**Qué hace:** Muestra la cola de trabajo de la cocina. Es la pantalla principal del cocinero.
**Qué muestra:** Todos los platos pendientes y en preparación, **ordenados por antigüedad** (primero el que se pidió antes), indicando mesa, plato, cantidad, observaciones del cliente y hace cuántos minutos se solicitó. Destaca los que llevan demasiado tiempo esperando.

### 2. Cambiar estado de un pedido
**Qué hace:** Actualiza en qué instancia de preparación está cada plato. Es lo que después ve el mesero desde su menú.
**Datos que pide:** Número de pedido o de plato, y el nuevo estado.
**Estados posibles:** Pendiente → En preparación → Listo. El mesero es quien lo marca como *Servido* al llevarlo a la mesa.
**Validaciones:** No se puede volver a un estado anterior.
**Qué muestra:** Confirmación con la hora del cambio y el tiempo que llevó la preparación.

### 3. Cargar plato a la carta
**Qué hace:** Da de alta un ítem en la carta del restaurante.
**Alta automática :** El cocinero ingresa el nombre del plato y **el sistema consulta una API de recetas** (por ejemplo TheMealDB) que devuelve la categoría, el origen, los ingredientes y las instrucciones de preparación. El cocinero confirma y le pone el precio.
**Alta manual:** Si la API no lo encuentra, se cargan los datos a mano.
**Datos que pide además:** Tipo de ítem (**plato, bebida o menú del día**), categoría, precio, tiempo estimado de preparación y, en el caso del menú del día, qué platos lo componen.
**Validaciones:** No se puede repetir el nombre del ítem. El precio no puede ser cero.
**Persistencia:** Se guarda en el archivo JSON de la carta.

### 4. Ver carta y marcar disponibilidad
**Qué hace:** Permite a la cocina avisar qué se puede pedir y qué no.
**Datos que pide:** Código del ítem y su nueva disponibilidad.
**Qué muestra:** La carta completa con el estado de cada ítem. Los marcados como no disponibles dejan de poder pedirse desde el menú del mesero hasta que la cocina los vuelva a habilitar.


---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | Dos jerarquías: `Empleado` como clase base con `Mesero` y `Cocinero` (cada uno con su menú y sus permisos), y `ItemCarta` como clase base con `Plato`, `Bebida` y `MenuDelDia`. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de ítem: el plato cobra su precio de lista más adicionales, la bebida se cobra por unidad y el menú del día cobra un precio fijo que agrupa varios platos. |
| **Composición** | El `Restaurante` está compuesto por sus `Mesas`, y el `Pedido` está compuesto por sus líneas de detalle: sin el pedido, esos ítems no existen. El `MenuDelDia` está compuesto por los platos que lo integran. |
| **Enumeraciones** | `Rol` (Mesero, Cocinero), `EstadoMesa` (Libre, Ocupada, EsperandoCuenta), `EstadoPedido` (Pendiente, EnPreparacion, Listo, Servido), `CategoriaItem` (Entrada, Principal, Postre, Bebida) y `MedioDePago`. |
| **Listas** | Colecciones de mesas, ítems de la carta, pedidos y empleados. La comanda de la cocina se arma ordenando una lista por antigüedad. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. La contraseña del empleado es el ejemplo más claro: solo se puede validar, nunca leer desde afuera. |
| **JSON** | Persistencia de usuarios, carta, mesas y pedidos en archivos `.json`. |
| **API externa** | Consulta a una API de recetas (por ejemplo TheMealDB) para cargar los datos de un plato a la carta. |
| **Menú de consola** | Los dos menús detallados en este documento, con el login como punto de entrada. |


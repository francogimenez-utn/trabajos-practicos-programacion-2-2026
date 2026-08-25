# GRUPO 4 — Sistema de gestión de comisiones de arte

**Integrantes:** Brisa Orbes — Luciana Longinotti

**Descripción del sistema:** Aplicación de consola para que un artista o estudio de ilustración administre los trabajos que le encargan sus clientes: registro de pedidos, cálculo de precios según el tipo de obra, seguimiento del estado de cada trabajo, orden de prioridad y control de pagos.

---

## MENÚ PRINCIPAL

```
1. Registrar cliente
2. Ver clientes
3. Determinar precios
4. Realizar un pedido
5. Ver comisiones
6. Ver detalle y precio de una comisión
7. Ordenar comisiones por prioridad
8. Cambiar estado de una comisión
9. Registrar pago
10. Cancelar pedido
11. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar cliente
**Qué hace:** Da de alta un cliente que encarga trabajos.
**Datos que pide:** Nombre o usuario (muchos clientes de comisiones contactan por redes), correo electrónico, red social de contacto, país y moneda en la que paga (pesos o dólares).
**Validaciones:** No se puede repetir el correo o el usuario.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 2. Ver clientes
**Qué hace:** Lista los clientes registrados.
**Qué muestra:** Datos de contacto, cantidad de comisiones encargadas, cuántas están en curso y monto total abonado por cada uno.

### 3. Determinar precios
**Qué hace:** Configura la lista de tarifas del artista. Es la base sobre la que después se calcula el precio de cada pedido.
**Datos que pide:**
- Precio base de cada tipo de obra (por ejemplo: sketch, ilustración a color, retrato, diseño de personaje, obra con fondo completo).
- Adicionales configurables: costo por personaje extra, costo por fondo detallado, porcentaje de recargo por entrega urgente.
**Validaciones:** Los precios no pueden ser negativos ni quedar sin definir.
**Qué muestra:** La lista de tarifas vigente.
**Persistencia:** Se guarda en el archivo JSON de configuración.

### 4. Realizar un pedido
**Qué hace:** Registra una nueva comisión. Es la operación principal del sistema.
**Datos que pide:**
- Cliente (existente o nuevo).
- Tipo de obra.
- Descripción de lo que pide el cliente.
- Cantidad de personajes, si lleva fondo y demás adicionales.
- Prioridad: normal o urgente.
- Fecha límite de entrega.
**Cómo calcula el precio:** El precio se arma a partir del precio base del tipo de obra más los adicionales que correspondan. Cada tipo de obra calcula su precio de manera distinta: por ejemplo, un sketch cobra solo el precio base, una ilustración a color suma un monto por cada personaje, y un diseño de personaje cobra por cantidad de vistas o poses. Si el pedido es urgente, se aplica el recargo configurado.
**Validaciones:** La fecha límite no puede ser anterior a la fecha actual. El artista no puede tomar más de una cantidad máxima de comisiones activas al mismo tiempo.
**Qué muestra:** Comprobante con número de comisión, cliente, tipo de obra, detalle del cálculo del precio, fecha límite y estado inicial (Pendiente).
**Persistencia:** Se guarda en el archivo JSON de comisiones.

### 5. Ver comisiones
**Qué hace:** Lista las comisiones registradas.
**Datos que pide:** Un criterio de filtro: por estado, por cliente, por tipo de obra o por fecha de entrega.
**Qué muestra:** Número, cliente, tipo de obra, prioridad, fecha límite, monto y estado actual de cada comisión.

### 6. Ver detalle y precio de una comisión
**Qué hace:** Muestra la ficha completa de un pedido puntual.
**Datos que pide:** Número de comisión.
**Qué muestra:** Todos los datos del pedido, el desglose del precio (precio base, cada adicional y el recargo por urgencia si corresponde), el total, cuánto pagó el cliente y cuánto resta abonar. Si el cliente paga en dólares, muestra además el equivalente en pesos según la cotización obtenida de la API.

### 7. Ordenar comisiones por prioridad
**Qué hace:** Muestra la cola de trabajo del artista ordenada, para saber qué hacer primero.
**Criterio de orden:** Primero las urgentes, y dentro de cada grupo las de fecha límite más próxima. Las comisiones ya entregadas o canceladas no aparecen.
**Qué muestra:** Listado ordenado con el número de comisión, cliente, tipo de obra, prioridad, días restantes hasta la fecha límite y estado. Debe destacar las que están vencidas o por vencer.

### 8. Cambiar estado de una comisión
**Qué hace:** Actualiza en qué instancia del trabajo está cada pedido.
**Datos que pide:** Número de comisión y nuevo estado.
**Estados posibles:** Pendiente → Aceptada → En proceso → En revisión → Entregada. También puede pasar a Cancelada.
**Validaciones:** No se puede volver a un estado anterior ni modificar una comisión ya entregada o cancelada. Para pasar a Entregada, el pedido debe estar totalmente pago (o el sistema debe advertirlo).
**Qué muestra:** Confirmación del cambio con la fecha en que se registró.

### 9. Registrar pago
**Qué hace:** Registra el dinero que abona el cliente. Debe contemplar pagos parciales, ya que es habitual cobrar una seña por adelantado y el resto al entregar.
**Datos que pide:** Número de comisión, monto abonado, moneda y medio de pago.
**Cómo funciona:** Si el pago es en dólares, el sistema consulta la cotización a la API y registra también el equivalente en pesos.
**Qué muestra:** Total de la comisión, lo abonado hasta el momento y el saldo pendiente.

### 10. Cancelar pedido
**Qué hace:** Cambia el estado de la comisión a *cancelada*.
**Datos que pide:** Número de comisión y motivo de la cancelación.
**Validaciones:** No se puede cancelar una comisión ya entregada. Si el cliente había pagado una seña, el sistema debe informar el monto abonado para gestionar la devolución.
**Qué muestra:** Confirmación de la cancelación.

### 11. Reportes
**Qué hace:** Muestra un submenú con la información del emprendimiento.
**Reportes que debe incluir como mínimo:**
- Ingresos totales de un período, en pesos y en dólares.
- Cantidad de comisiones por tipo de obra (cuál es la más pedida).
- Comisiones entregadas, en curso y canceladas.
- Clientes con más pedidos.
- Montos pendientes de cobro.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Obra` como clase base, con `Sketch`, `Ilustracion`, `Retrato` y `DisenioPersonaje` heredando de ella. También puede aplicarse `Persona` → `Cliente` / `Artista`. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de obra: cada una arma su precio con sus propios adicionales. |
| **Composición** | La `Comision` está compuesta por la obra y por los adicionales solicitados: sin la comisión, esos elementos no existen. |
| **Enumeraciones** | `EstadoComision` (Pendiente, Aceptada, EnProceso, EnRevision, Entregada, Cancelada), `Prioridad` (Normal, Urgente), `TipoObra` y `Moneda`. |
| **Listas** | Colecciones de clientes, comisiones y pagos. La cola de trabajo ordenada por prioridad también se resuelve sobre una lista. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de clientes, comisiones, pagos y lista de tarifas en archivos `.json`. |
| **API externa** | Consulta de la cotización del dólar para registrar pagos de clientes del exterior y convertir los importes a pesos. |
| **Menú de consola** | El menú detallado en este documento. |


# GRUPO 8 — Sistema de gestión de estacionamiento

**Integrantes:** Agustina Albornoz — Victoria Lizazu — Morena Barreyro

**Descripción del sistema:** Aplicación de consola para la administración de una playa de estacionamiento: control de ingresos y egresos de vehículos, ocupación de plazas, cobro por tiempo de estadía según el tipo de vehículo, gestión de clientes abonados y reportes de recaudación.

---

## MENÚ PRINCIPAL

```
1. Registrar ingreso de vehículo
2. Registrar egreso y cobro
3. Ver estado de las plazas
4. Consultar un vehículo estacionado
5. Reservar una plaza
6. Registrar abonado
7. Ver abonados
8. Cobrar mensualidad
9. Configurar tarifas
10. Ver movimientos del día
11. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar ingreso de vehículo
**Qué hace:** Registra la entrada de un vehículo y le asigna una plaza. Es la operación principal del sistema.
**Datos que pide:** Patente y tipo de vehículo (auto, moto o camioneta).
**Cómo funciona:** El sistema verifica si la patente corresponde a un **abonado** o a un **cliente ocasional** y le asigna una plaza libre acorde al tipo de vehículo (una moto no ocupa una plaza de camioneta). Registra automáticamente la hora de entrada.
**Validaciones:**
- Debe haber plazas libres para ese tipo de vehículo. Si no hay, el sistema lo informa.
- La patente no puede estar ya dentro del estacionamiento.
- Si es abonado, verifica que tenga la mensualidad al día.
**Qué muestra:** Ticket de ingreso con número, patente, tipo de vehículo, plaza asignada, hora de entrada y la tarifa por hora que se le va a aplicar.
**Persistencia:** Se guarda en el archivo JSON de movimientos.

### 2. Registrar egreso y cobro
**Qué hace:** Registra la salida del vehículo, libera la plaza y cobra la estadía.
**Datos que pide:** Patente o número de ticket, y medio de pago.
**Cómo calcula el monto:**
- **Cliente ocasional:** cantidad de horas (o fracción) por la tarifa correspondiente a su tipo de vehículo. Se aplica una **tarifa diferencial en fines de semana y feriados**, verificando la fecha contra una API de feriados de Argentina.
- **Abonado:** no paga al salir, porque ya abonó la mensualidad. El sistema lo informa y solo registra el movimiento.
**Validaciones:** La patente debe estar registrada como ingresada.
**Qué muestra:** Comprobante con hora de entrada, hora de salida, tiempo total de estadía, desglose del cálculo y monto final a pagar.

### 3. Ver estado de las plazas
**Qué hace:** Muestra el mapa de ocupación de la playa.
**Qué muestra:** Cada plaza con su número, el tipo de vehículo que admite y su estado: **libre, ocupada, reservada o fuera de servicio**. Si está ocupada, muestra la patente y hace cuánto tiempo está. Al final muestra el resumen: cuántas plazas libres y ocupadas hay de cada tipo.

### 4. Consultar un vehículo estacionado
**Qué hace:** Busca un vehículo que está adentro y muestra su situación actual.
**Datos que pide:** Patente.
**Qué muestra:** Plaza que ocupa, hora de ingreso, **cuántas horas lleva estacionado** y cuánto tendría que pagar si saliera en ese momento.

### 5. Reservar una plaza
**Qué hace:** Reserva una plaza para un cliente que la va a usar más tarde.
**Datos que pide:** Patente, tipo de vehículo y horario estimado de llegada.
**Cómo funciona:** La plaza queda en estado *reservada* y no puede asignarse a otro vehículo. Si el cliente no llega dentro de un margen de tiempo, la reserva se cancela y la plaza vuelve a estar libre.
**Qué muestra:** Confirmación con el número de plaza reservada.

### 6. Registrar abonado
**Qué hace:** Da de alta un cliente con abono mensual.
**Datos que pide:** DNI, nombre, apellido, teléfono, patente del vehículo, tipo de vehículo y tipo de abono (por ejemplo: diurno, nocturno o completo).
**Validaciones:** No se puede repetir la patente ni el DNI.
**Qué muestra:** Confirmación con el número de abonado, el monto mensual que le corresponde y la fecha de vencimiento del abono.
**Persistencia:** Se guarda en el archivo JSON de abonados.

### 7. Ver abonados
**Qué hace:** Lista los clientes con abono.
**Datos que pide:** Opcionalmente, un filtro por estado (al día / vencido).
**Qué muestra:** Número de abonado, nombre, patente, tipo de abono, monto mensual y fecha de vencimiento, destacando los que adeudan.

### 8. Cobrar mensualidad
**Qué hace:** Registra el pago mensual de un abonado y renueva su abono.
**Datos que pide:** Número de abonado o patente, y medio de pago.
**Qué muestra:** Comprobante con el período abonado, el monto y la nueva fecha de vencimiento.

### 9. Configurar tarifas
**Qué hace:** Define los valores que usa el sistema para cobrar.
**Datos que pide:**
- Tarifa por hora de cada tipo de vehículo (auto, moto, camioneta).
- Porcentaje de recargo para fines de semana y feriados.
- Monto mensual de cada tipo de abono.
- Cantidad de plazas de cada tipo que tiene la playa.
**Validaciones:** Los valores no pueden ser negativos.
**Persistencia:** Se guarda en el archivo JSON de configuración.

### 10. Ver movimientos del día
**Qué hace:** Lista los ingresos y egresos registrados.
**Datos que pide:** Opcionalmente, una fecha (por defecto muestra el día actual).
**Qué muestra:** Patente, tipo de vehículo, hora de entrada, hora de salida, tiempo de estadía y monto cobrado de cada movimiento, con el total recaudado al final.

### 11. Reportes
**Qué hace:** Muestra un submenú con la información de gestión de la playa.
**Reportes que debe incluir como mínimo:**
- Recaudación de un período, separando estadías ocasionales y abonos mensuales.
- Porcentaje de ocupación promedio y horarios de mayor demanda.
- Cantidad de vehículos atendidos por tipo.
- Tiempo promedio de estadía.
- Abonados con la mensualidad vencida.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | Dos jerarquías posibles, y conviene usar ambas: `Vehiculo` como clase base con `Auto`, `Moto` y `Camioneta` (cada uno con su tarifa y su tamaño de plaza), y `Cliente` como clase base con `ClienteOcasional` y `Abonado`. |
| **Interfaces** | El cálculo del cobro debe resolverse con una interfaz implementada de forma distinta según el tipo de cliente: el ocasional paga por hora al salir, mientras que el abonado paga un monto fijo mensual y no abona la estadía. |
| **Composición** | El `Estacionamiento` está compuesto por sus `Plazas`: sin la playa, las plazas no existen. Es la relación de composición más clara del sistema. |
| **Enumeraciones** | `TipoVehiculo` (Auto, Moto, Camioneta), `EstadoPlaza` (Libre, Ocupada, Reservada, FueraDeServicio), `TipoAbono` (Diurno, Nocturno, Completo) y `MedioDePago`. |
| **Listas** | Colecciones de plazas, vehículos dentro de la playa, abonados y movimientos. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de abonados, movimientos, plazas y configuración de tarifas en archivos `.json`. |
| **API externa** | Consulta de feriados nacionales de Argentina para aplicar la tarifa diferencial los días no laborables. |
| **Menú de consola** | El menú detallado en este documento. |

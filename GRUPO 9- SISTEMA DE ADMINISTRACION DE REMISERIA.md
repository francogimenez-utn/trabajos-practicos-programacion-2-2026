# GRUPO 9 — Sistema de administración de remisería

**Integrantes:** Sebastián Casas — Joaquín Molina

**Descripción del sistema:** Aplicación de consola para la administración de una remisería: registro de clientes, choferes y vehículos, toma de viajes, asignación de chofer, cálculo de la tarifa según el tipo de viaje y control del historial de servicios.

---

## MENÚ PRINCIPAL

```
1. Registrar cliente
2. Ver clientes
3. Dar de baja cliente
4. Registrar chofer y vehículo
5. Consultar choferes disponibles
6. Registrar viaje
7. Asignar chofer
8. Cerrar viaje
9. Cancelar viaje
10. Consultar historial de viajes
11. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar cliente
**Qué hace:** Da de alta un cliente de la remisería.
**Datos que pide:** DNI, nombre, apellido, teléfono, dirección habitual y si es cliente particular o con cuenta corriente (empresas que viajan seguido y pagan a fin de mes).
**Validaciones:** No se puede repetir el DNI ni el teléfono.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 2. Ver clientes
**Qué hace:** Lista los clientes registrados.
**Datos que pide:** Opcionalmente, un filtro por tipo de cliente o una búsqueda por apellido o teléfono.
**Qué muestra:** Datos de contacto, cantidad de viajes realizados y, en el caso de las cuentas corrientes, el saldo pendiente de pago.

### 3. Dar de baja cliente
**Qué hace:** Da de baja un cliente del sistema.
**Datos que pide:** DNI o teléfono y confirmación.
**Validaciones:** No se puede dar de baja un cliente que tenga un viaje en curso o saldo impago. El sistema debe informar el motivo.
**Qué muestra:** Confirmación de la baja.

### 4. Registrar chofer y vehículo
**Qué hace:** Da de alta un chofer con el vehículo que maneja.
**Datos que pide:** DNI, nombre, apellido, teléfono, número de licencia, y del vehículo: patente, marca, modelo, año y cantidad de plazas.
**Validaciones:** No se puede repetir el DNI, la licencia ni la patente.
**Qué muestra:** Confirmación con el número de chofer asignado y su estado inicial (Disponible).
**Persistencia:** Se guarda en el archivo JSON de choferes.

### 5. Consultar choferes disponibles
**Qué hace:** Muestra qué choferes están libres para tomar un viaje.
**Qué muestra:** Cada chofer con su nombre, vehículo, estado (**disponible, en viaje o franco**) y, si está en viaje, el destino y desde qué hora. Al final, el resumen de cuántos hay libres.

### 6. Registrar viaje
**Qué hace:** Toma el pedido de un viaje. Es la operación principal del sistema.
**Datos que pide:**
- Cliente (existente o nuevo).
- Origen y destino.
- Tipo de viaje: **urbano, larga distancia o aeropuerto**.
- Kilómetros estimados y cantidad de pasajeros.
- Horario del viaje.
**Cómo calcula la tarifa estimada:** Cada tipo de viaje se cobra de forma distinta:
- **Urbano:** bajada de bandera más un monto por kilómetro recorrido.
- **Larga distancia:** monto por kilómetro con tarifa reducida, más peajes y retorno.
- **Aeropuerto:** tarifa plana fija según el destino.
Sobre ese valor se aplica **recargo nocturno y recargo por feriado o fin de semana**, verificando la fecha contra una API de feriados de Argentina.
**Qué muestra:** Comprobante con número de viaje, cliente, recorrido, tarifa estimada con el desglose de recargos y estado inicial (Solicitado).
**Persistencia:** Se guarda en el archivo JSON de viajes.

### 7. Asignar chofer
**Qué hace:** Asigna un chofer disponible a un viaje ya registrado.
**Datos que pide:** Número de viaje y chofer (elegido entre los disponibles).
**Validaciones:** El chofer debe estar disponible y su vehículo debe tener plazas suficientes para la cantidad de pasajeros. Al asignarlo, su estado pasa a *En viaje* y el del viaje a *Asignado*.
**Qué muestra:** Confirmación con el chofer asignado, su vehículo y el horario estimado de llegada.

### 8. Cerrar viaje
**Qué hace:** Registra que el viaje terminó, calcula el costo final y libera al chofer.
**Datos que pide:** Número de viaje, kilómetros realmente recorridos, esperas o paradas adicionales y medio de pago.
**Cómo calcula el costo final:** Recalcula la tarifa con los kilómetros reales y suma los adicionales (esperas, equipaje, paradas). Si el cliente tiene cuenta corriente, el monto se acumula en su saldo en lugar de cobrarse en el momento.
**Qué muestra:** Comprobante final con el desglose completo: tarifa base, recargos, adicionales y **costo total del viaje**. El chofer vuelve a estado *Disponible* y el viaje pasa a *Finalizado*.

### 9. Cancelar viaje
**Qué hace:** Cancela un viaje que todavía no se realizó.
**Datos que pide:** Número de viaje y motivo de la cancelación.
**Validaciones:** No se puede cancelar un viaje ya finalizado. Si ya tenía chofer asignado, este vuelve a quedar disponible.
**Qué muestra:** Confirmación de la cancelación.

### 10. Consultar historial de viajes
**Qué hace:** Lista los viajes registrados.
**Datos que pide:** Un criterio de filtro: por fecha, por cliente, por chofer o por estado.
**Qué muestra:** Número de viaje, fecha, cliente, chofer, recorrido, tipo de viaje, monto y estado.

### 11. Reportes
**Qué hace:** Muestra la información de gestión de la remisería.
**Reportes que debe incluir como mínimo:**
- Recaudación de un período, separada por tipo de viaje.
- Cantidad de viajes y kilómetros realizados por cada chofer.
- Clientes con más viajes y saldos pendientes de las cuentas corrientes.
- Horarios y días de mayor demanda.
- Viajes cancelados y sus motivos.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Viaje` como clase base, con `ViajeUrbano`, `ViajeLargaDistancia` y `ViajeAeropuerto` heredando de ella. También puede aplicarse `Persona` → `Cliente` / `Chofer`. |
| **Interfaces** | El cálculo de la tarifa debe resolverse con una interfaz implementada de forma distinta por cada tipo de viaje: bajada de bandera más kilómetro en el urbano, kilómetro con peajes en larga distancia y tarifa plana en el de aeropuerto. |
| **Composición** | El `Viaje` está compuesto por su detalle de tarifa: recargos, esperas y adicionales, que no existen fuera del viaje. |
| **Enumeraciones** | `EstadoViaje` (Solicitado, Asignado, EnCurso, Finalizado, Cancelado), `EstadoChofer` (Disponible, EnViaje, Franco), `TipoViaje` (Urbano, LargaDistancia, Aeropuerto) y `MedioDePago`. |
| **Listas** | Colecciones de clientes, choferes, vehículos y viajes. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de clientes, choferes y viajes en archivos `.json`. |
| **API externa** | Consulta de feriados nacionales de Argentina para aplicar el recargo correspondiente a los días no laborables. |
| **Menú de consola** | El menú detallado en este documento. |

# GRUPO 14 — Sistema de gestión de peluquería

**Integrante:** Santiago Alfaro

**Descripción del sistema:** Aplicación de consola para la administración de una peluquería: registro de clientes, catálogo de servicios con distinta duración y forma de cobro, agenda de turnos por profesional, atención y cobro, e historial de cada cliente.

---

## MENÚ PRINCIPAL

```
1. Registrar cliente
2. Historial de clientes
3. Registrar servicio
4. Consultar servicios
5. Registrar peluquero
6. Registrar turno
7. Consultar turnos
8. Cancelar turno
9. Atender turno y registrar pago
10. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar cliente
**Qué hace:** Da de alta un cliente de la peluquería.
**Datos que pide:** DNI, nombre, apellido, teléfono y tipo de cabello o preferencias.
**Validaciones:** No se puede repetir el DNI ni el teléfono.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 2. Historial de clientes
**Qué hace:** Busca un cliente y muestra su ficha completa con todo lo que se le hizo.
**Datos que pide:** DNI, teléfono o apellido. También permite ver el listado general de clientes.
**Qué muestra:** Datos personales, cantidad de visitas, fecha de la última visita, servicios realizados con su fecha y peluquero, total gastado y turnos futuros agendados.

### 3. Registrar servicio
**Qué hace:** Da de alta un servicio del catálogo (corte, color, brushing, tratamiento, peinado, etc.).
**Datos que pide:** Nombre del servicio, **tipo de servicio**, precio base, **duración estimada en minutos** y si requiere productos adicionales.
**Por qué importa la duración:** Un corte puede llevar 30 minutos y una coloración dos horas. La agenda debe reservar el tiempo real que ocupa cada servicio, no un bloque fijo.
**Validaciones:** No se puede repetir el nombre del servicio. El precio y la duración no pueden ser cero.
**Persistencia:** Se guarda en el archivo JSON de servicios.

### 4. Consultar servicios
**Qué hace:** Lista el catálogo de servicios.
**Datos que pide:** Opcionalmente, un filtro por tipo de servicio.
**Qué muestra:** Nombre, tipo, precio base, duración estimada y cantidad de veces que se realizó.

### 5. Registrar peluquero
**Qué hace:** Da de alta un profesional que atiende en el local.
**Datos que pide:** DNI, nombre, apellido, teléfono, especialidad (color, corte, tratamientos), días y horarios en los que trabaja y porcentaje de comisión sobre los servicios que realiza.
**Validaciones:** No se puede repetir el DNI.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de peluqueros.

### 6. Registrar turno
**Qué hace:** Agenda un turno para un cliente. Es la operación principal del sistema.
**Datos que pide:** Cliente (existente o nuevo), peluquero, uno o varios servicios, fecha y horario.
**Cómo funciona:** El sistema suma la duración de todos los servicios elegidos y reserva ese tiempo completo en la agenda del peluquero. También calcula el precio estimado del turno.
**Validaciones:**
- El día no puede ser feriado ni día no laborable (se verifica contra una **API de feriados de Argentina**).
- El peluquero debe trabajar ese día y tener el bloque de tiempo libre.
- La fecha no puede ser anterior a la actual.
**Qué muestra:** Comprobante con número de turno, cliente, peluquero, servicios solicitados, horario de inicio, horario estimado de finalización y precio estimado.
**Persistencia:** Se guarda en el archivo JSON de turnos.

### 7. Consultar turnos
**Qué hace:** Muestra la agenda del local.
**Datos que pide:** Un criterio: por fecha (agenda del día), por peluquero, por cliente o por estado.
**Qué muestra:** En la vista por peluquero y fecha, la franja horaria completa indicando qué bloques están ocupados, con qué cliente y qué servicio, y cuáles quedan libres.

### 8. Cancelar turno
**Qué hace:** Cambia el estado del turno a *cancelado* y libera el bloque horario del peluquero.
**Datos que pide:** Número de turno y motivo.
**Validaciones:** No se puede cancelar un turno ya atendido.
**Qué muestra:** Confirmación de la cancelación.

### 9. Atender turno y registrar pago
**Qué hace:** Registra que el cliente concurrió, se le realizaron los servicios y abonó.
**Datos que pide:** Número de turno, servicios efectivamente realizados (pueden diferir de los agendados), medio de pago y observaciones.
**Cómo calcula el monto final:** Cada tipo de servicio se cobra de forma distinta:
- **Corte o peinado:** precio fijo de lista.
- **Coloración:** precio base más un adicional según el largo del cabello y la cantidad de producto utilizada.
- **Tratamiento:** se cobra por sesión, con descuento si el cliente contrata varias.
Sobre el total se aplica el ajuste del medio de pago y los descuentos por cliente frecuente si corresponde.
**Qué muestra:** Comprobante con el detalle de los servicios, el desglose del precio, el total abonado y la comisión que le corresponde al peluquero.
**Otros estados:** Desde esta misma opción se marca el turno como *ausente* si el cliente no se presentó.

### 10. Reportes
**Qué hace:** Muestra un submenú con la información del local.
**Reportes que debe incluir como mínimo:**
- Recaudación de un período, separada por tipo de servicio.
- Servicios más solicitados.
- Facturación y comisiones de cada peluquero.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Servicio` como clase base, con `Corte`, `Coloracion` y `Tratamiento` heredando de ella. Cada uno tiene su propia duración y su propia forma de calcular el precio. También puede aplicarse `Persona` → `Cliente` / `Peluquero`. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada tipo de servicio: precio fijo en el corte, precio por largo y producto en la coloración, y precio por sesión en el tratamiento. |
| **Composición** | El `Turno` está compuesto por los servicios solicitados y sus adicionales: sin el turno, ese detalle no existe. |
| **Enumeraciones** | `EstadoTurno` (Pendiente, Atendido, Cancelado, Ausente), `TipoServicio` (Corte, Coloracion, Tratamiento, Peinado) y `MedioDePago`. |
| **Listas** | Colecciones de clientes, peluqueros, servicios y turnos. La agenda de cada peluquero también se resuelve sobre una lista. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de clientes, peluqueros, servicios y turnos en archivos `.json`. |
| **API externa** | Consulta de feriados nacionales de Argentina para impedir que se agenden turnos en días no laborables. |
| **Menú de consola** | El menú detallado en este documento. |


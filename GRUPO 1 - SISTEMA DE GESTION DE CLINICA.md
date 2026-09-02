# GRUPO 1 — Sistema de gestión de clínica

**Integrantes:** Fausto Rivero — Manuel Torrealba Cañon

**Descripción del sistema:** Aplicación de consola para la administración de una clínica médica: registro de pacientes y profesionales, gestión de la agenda de turnos, atención y cobro de consultas.

---

## MENÚ PRINCIPAL

```
1. Registrar paciente
2. Listar pacientes
3. Buscar paciente
4. Registrar profesional
5. Listar profesionales
6. Ver agenda de un profesional
7. Sacar turno
8. Ver turnos
9. Reprogramar turno
10. Cancelar turno
11. Atender turno
12. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar paciente
**Qué hace:** Da de alta un paciente nuevo en el sistema.
**Datos que pide:** DNI, nombre, apellido, fecha de nacimiento, teléfono, tipo de cobertura (particular u obra social) y, si corresponde, nombre de la obra social y número de afiliado.
**Validaciones:** No se puede registrar dos pacientes con el mismo DNI.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de pacientes.

### 2. Listar pacientes
**Qué hace:** Muestra todos los pacientes registrados.
**Qué muestra:** Tabla con DNI, nombre completo, edad (calculada a partir de la fecha de nacimiento), teléfono y cobertura.

### 3. Buscar paciente
**Qué hace:** Busca un paciente puntual y muestra su ficha.
**Datos que pide:** DNI.
**Qué muestra:** Datos personales del paciente y su historial de turnos (fecha, profesional, especialidad y estado de cada turno).

### 4. Registrar profesional
**Qué hace:** Da de alta un médico o profesional de la salud.
**Datos que pide:** Matrícula, nombre, apellido, especialidad (elegida de una lista fija), valor de la consulta y días/horarios en los que atiende.
**Validaciones:** No se puede repetir la matrícula.
**Qué muestra:** Confirmación con los datos cargados.
**Persistencia:** Se guarda en el archivo JSON de profesionales.

### 5. Listar profesionales
**Qué hace:** Muestra los profesionales de la clínica.
**Datos que pide:** Opcionalmente, una especialidad para filtrar.
**Qué muestra:** Matrícula, nombre completo, especialidad, valor de consulta y días de atención.

### 6. Ver agenda de un profesional
**Qué hace:** Muestra cómo está la agenda de un profesional en una fecha determinada.
**Datos que pide:** Matrícula del profesional y fecha.
**Qué muestra:** Todos los horarios de atención de ese día indicando cuáles están libres y cuáles ocupados (con el nombre del paciente y el estado del turno).

### 7. Sacar turno
**Qué hace:** Asigna un turno a un paciente con un profesional en una fecha y horario.
**Datos que pide:** DNI del paciente, matrícula del profesional (o especialidad, para que el sistema muestre quiénes atienden esa especialidad), fecha y horario.
**Validaciones:**
- El paciente y el profesional deben existir.
- La fecha no puede ser anterior a la fecha actual.
- **El día no puede ser feriado ni día no laborable** (se verifica consultando una API de feriados de Argentina).
- El profesional debe atender ese día de la semana.
- El horario debe estar libre (no puede haber dos turnos del mismo profesional a la misma hora).
**Qué muestra:** Comprobante del turno con número, paciente, profesional, especialidad, fecha, horario y costo estimado de la consulta según la cobertura del paciente.
**Persistencia:** Se guarda en el archivo JSON de turnos.

### 8. Ver turnos
**Qué hace:** Lista los turnos del sistema.
**Datos que pide:** Un criterio de filtro: por fecha, por profesional, por paciente o por estado (pendiente / atendido / cancelado / ausente).
**Qué muestra:** Listado de turnos con número, fecha, horario, paciente, profesional y estado.

### 9. Reprogramar turno
**Qué hace:** Cambia la fecha y/o el horario de un turno ya asignado, sin perder el registro del paciente y el profesional.
**Datos que pide:** Número de turno, nueva fecha y nuevo horario.
**Validaciones:** Las mismas que al sacar un turno. Solo se pueden reprogramar turnos en estado pendiente.
**Qué muestra:** Comprobante actualizado.

### 10. Cancelar turno
**Qué hace:** Cambia el estado del turno a *cancelado* y libera el horario en la agenda del profesional.
**Datos que pide:** Número de turno y motivo de la cancelación.
**Validaciones:** Solo se pueden cancelar turnos pendientes.
**Qué muestra:** Confirmación de la cancelación.

### 11. Atender turno
**Qué hace:** Registra que el paciente concurrió y fue atendido, y cobra la consulta.
**Datos que pide:** Número de turno y diagnóstico u observación de la atención.
**Cómo calcula el costo:**
- **Paciente particular:** paga el valor completo de la consulta del profesional.
- **Paciente con obra social:** paga únicamente el coseguro; el resto queda registrado como monto a facturar a la obra social.
**Qué muestra:** Detalle de la atención con el monto que abona el paciente y el monto que cubre la obra social.
**Otros estados:** Desde esta misma opción se debe poder marcar el turno como *ausente* si el paciente no se presentó.

### 12. Reportes
**Qué hace:** Muestra información de la clínica.
**Reportes que debe incluir como mínimo:**
- Turnos atendidos y recaudación total de una fecha o período.
- Cantidad de turnos por profesional y por especialidad.
- Porcentaje de ausentismo (turnos marcados como ausente sobre el total).
- Listado de montos pendientes de facturar a cada obra social.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Persona` como clase base, con `Paciente` y `Profesional` heredando de ella. |
| **Interfaces** | El cálculo del costo de la consulta debe resolverse con una interfaz implementada de forma distinta según el tipo de cobertura del paciente (particular / obra social). |
| **Enumeraciones** | `EstadoTurno` (Pendiente, Atendido, Cancelado, Ausente) y `Especialidad` (Clínica, Pediatría, Traumatología, etc.). |
| **Listas** | Colecciones de pacientes, profesionales y turnos. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de pacientes, profesionales y turnos en archivos `.json`. |
| **API externa** | Consulta de feriados nacionales de Argentina para impedir que se asignen turnos en días no laborables. |
| **Menú de consola** | El menú detallado en este documento. |


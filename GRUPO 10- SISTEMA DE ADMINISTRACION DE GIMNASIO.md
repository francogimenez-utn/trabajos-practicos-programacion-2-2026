# GRUPO 10 — Sistema de administración de gimnasio

**Integrantes:** Valentino Paolini — Benicio Talarico

**Descripción del sistema:** Aplicación de consola para la administración de un gimnasio: registro de socios con distintos tipos de membresía, control de pagos y vencimientos de cuota, registro de asistencia, inscripción a clases y asignación de rutinas de entrenamiento.

---

## MENÚ PRINCIPAL

```
1. Registrar socio
2. Mostrar socios
3. Buscar socio
4. Modificar socio
5. Dar de baja socio
6. Registrar pago
7. Consultar estado de cuota
8. Registrar asistencia
9. Asignar rutina a un socio
10. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Registrar socio
**Qué hace:** Da de alta un socio del gimnasio.
**Datos que pide:** DNI, nombre, apellido, fecha de nacimiento, teléfono, contacto de emergencia y **tipo de membresía** (básica, premium o pase diario).
**Cómo funciona:** Según el tipo de membresía elegido, el sistema calcula el valor de la cuota y define a qué tiene acceso el socio (horario reducido o acceso completo, con rutina personalizada o sin ella).
**Validaciones:** No se puede repetir el DNI. Si es menor de edad, debe registrarse un responsable.
**Qué muestra:** Confirmación con el número de socio, el tipo de membresía, el valor de la cuota y la fecha de vencimiento.
**Persistencia:** Se guarda en el archivo JSON de socios.

### 2. Mostrar socios
**Qué hace:** Lista los socios registrados.
**Datos que pide:** Opcionalmente, un filtro por tipo de membresía o por estado (al día / cuota vencida / dado de baja).
**Qué muestra:** Número de socio, nombre completo, tipo de membresía, estado de la cuota y fecha de vencimiento.

### 3. Buscar socio
**Qué hace:** Busca un socio puntual y muestra su ficha completa.
**Datos que pide:** DNI, número de socio o apellido.
**Qué muestra:** Datos personales, tipo de membresía, historial de pagos, cantidad de asistencias del mes y rutina asignada.

### 4. Modificar socio
**Qué hace:** Edita los datos de un socio o **cambia su tipo de membresía**.
**Datos que pide:** Número de socio y el dato a modificar.
**Cómo funciona:** Si se cambia la membresía, el sistema recalcula el valor de la cuota y muestra la diferencia a favor o en contra.
**Qué muestra:** La ficha del socio con los datos actualizados.

### 5. Dar de baja socio
**Qué hace:** Da de baja un socio que deja el gimnasio.
**Datos que pide:** Número de socio, motivo de la baja y confirmación.
**Validaciones:** Si el socio adeuda cuotas, el sistema debe informarlo antes de confirmar.
**Qué muestra:** Confirmación de la baja con la fecha.

### 6. Registrar pago
**Qué hace:** Registra el pago de la cuota de un socio y renueva su vencimiento.
**Datos que pide:** Número de socio, período que abona, medio de pago y si paga por mes, por trimestre o por año.
**Cómo calcula el monto:** El valor depende del tipo de membresía y del período abonado. Los planes trimestrales y anuales tienen descuento respecto del pago mensual. Si el socio tiene cuotas atrasadas, se suma el recargo por mora.
**Qué muestra:** Comprobante con el período abonado, el desglose del monto (cuota, descuento y recargo si corresponde) y la nueva fecha de vencimiento.
**Persistencia:** Se guarda en el archivo JSON de pagos.

### 7. Consultar estado de cuota
**Qué hace:** Muestra la situación de pago de un socio o de todos.
**Datos que pide:** Número de socio, o la opción de ver el listado general.
**Qué muestra:** Fecha del último pago, fecha de vencimiento, días restantes o días de atraso y monto adeudado. En el listado general, destaca los socios con la cuota vencida.

### 8. Registrar asistencia
**Qué hace:** Registra el ingreso de un socio al gimnasio.
**Datos que pide:** Número de socio o DNI.
**Validaciones:**
- El socio debe tener la cuota al día. Si está vencida, el sistema lo informa y no permite el ingreso (o lo permite con advertencia, según defina el grupo).
- El socio no debe estar dado de baja.
- Si la membresía es de pase diario, se descuenta un pase disponible.
**Qué muestra:** Confirmación del ingreso con el nombre del socio, fecha y hora, y su cantidad de asistencias del mes.
**Persistencia:** Se guarda en el archivo JSON de asistencias.

### 9. Asignar rutina a un socio
**Qué hace:** Arma una rutina de entrenamiento para un socio.
**Cómo funciona:** El sistema **consulta una API de ejercicios** para traer el listado de ejercicios disponibles según el grupo muscular elegido (pecho, espalda, piernas, etc.). El usuario selecciona los ejercicios y define series y repeticiones para cada uno.
**Datos que pide:** Número de socio, objetivo de la rutina, grupo muscular por día y los ejercicios seleccionados.
**Qué muestra:** La rutina completa del socio, organizada por día de entrenamiento.
**Persistencia:** Se guarda en el archivo JSON de rutinas.

### 10. Reportes
**Qué hace:** Muestra un submenú con la información de gestión del gimnasio.
**Reportes que debe incluir como mínimo:**
- Ingresos del mes, separados por tipo de membresía.
- Cantidad de socios activos, dados de baja y con cuota vencida.
- Horarios y días de mayor asistencia.
- Socios con menor asistencia del mes (para contactarlos).

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Membresia` como clase base, con `MembresiaBasica`, `MembresiaPremium` y `PaseDiario` heredando de ella. Cada una tiene distinto valor de cuota y distintos accesos. |
| **Interfaces** | El cálculo de la cuota debe resolverse con una interfaz implementada de forma distinta por cada tipo de membresía: la básica cobra un valor fijo mensual, la premium habilita el horario completo y la rutina personalizada, con otro valor, y el pase diario se cobra por cantidad de accesos. |
| **Composición** | La `Rutina` está compuesta por sus ejercicios con series y repeticiones: sin la rutina, esos elementos no existen. |
| **Enumeraciones** | `TipoMembresia` (Basica, Premium, PaseDiario), `EstadoSocio` (Activo, CuotaVencida, DadoDeBaja), `GrupoMuscular` y `MedioDePago`. |
| **Listas** | Colecciones de socios, pagos, asistencias y rutinas. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de socios, pagos, asistencias y rutinas en archivos `.json`. |
| **API externa** | Consulta a una API de ejercicios (por ejemplo wger o ExerciseDB) para armar las rutinas de entrenamiento. Como alternativa, puede usarse una API de cotización para actualizar el valor de las cuotas. |
| **Menú de consola** | El menú detallado en este documento. |

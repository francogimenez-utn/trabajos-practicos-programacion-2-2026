# GRUPO 12 — Sistema de gestión de estadio de fútbol

**Integrantes:** Brian Domínguez — Pedro Mendoza — Joaquín Fourcade

**Descripción del sistema:** Aplicación de consola para la administración de un estadio de fútbol: carga de los partidos, venta de entradas por sector, control de capacidad, gestión de socios del club con sus beneficios, promociones y estadísticas de recaudación.

---

## MENÚ PRINCIPAL

```
1. Cargar partido
2. Ver partidos
3. Consultar capacidad
4. Cargar espectador
5. Mostrar espectadores
6. Buscar espectador
7. Registrar membresía del club
8. Consultar beneficios de la membresía
9. Registrar venta de entrada
10. Anular entrada
11. Configurar promociones y descuentos
12. Mostrar estadísticas
```

---

## DETALLE DE CADA OPCIÓN

### 1. Cargar partido
**Qué hace:** Da de alta un partido o evento para el que se van a vender entradas. 
**Alta automática (recomendada):** El usuario elige un torneo y **el sistema consulta una API de fútbol** que devuelve los próximos partidos con rival, fecha y horario. El usuario selecciona uno y confirma.
**Alta manual:** Si la API no está disponible, se cargan los datos a mano.
**Datos que pide además:** Torneo o categoría del partido, sectores habilitados y precio base de la entrada de cada sector.
**Validaciones:** No puede haber dos partidos en la misma fecha y horario. La fecha no puede ser anterior a la actual.
**Qué muestra:** Ficha del partido con su ID, rival, fecha, horario, sectores habilitados y capacidad total disponible.
**Persistencia:** Se guarda en el archivo JSON de partidos.

### 2. Ver partidos
**Qué hace:** Lista los partidos cargados.
**Datos que pide:** Opcionalmente, un filtro por estado (próximos / ya jugados).
**Qué muestra:** ID, rival, fecha, horario, entradas vendidas, entradas disponibles y recaudación acumulada de cada partido.

### 3. Consultar capacidad
**Qué hace:** Muestra el detalle de ocupación de un partido por sector.
**Datos que pide:** ID del partido.
**Qué muestra:** Cada sector (popular, platea, palco, preferencial) con su capacidad total, entradas vendidas, lugares disponibles, porcentaje de ocupación y precio vigente. Al final, el total del estadio.

### 4. Cargar espectador
**Qué hace:** Da de alta una persona en el sistema.
**Datos que pide:** DNI, nombre, apellido, fecha de nacimiento, teléfono y correo electrónico.
**Validaciones:** No se puede repetir el DNI.
**Qué muestra:** Confirmación con los datos cargados y su condición inicial (no socio).
**Persistencia:** Se guarda en el archivo JSON de espectadores.

### 5. Mostrar espectadores
**Qué hace:** Lista las personas registradas.
**Datos que pide:** Opcionalmente, un filtro por tipo de membresía o por estado de la cuota social.
**Qué muestra:** DNI, nombre completo, tipo de membresía, estado de la cuota y cantidad de partidos a los que asistió.

### 6. Buscar espectador
**Qué hace:** Busca una persona puntual y muestra su ficha.
**Datos que pide:** DNI o apellido.
**Qué muestra:** Datos personales, tipo de membresía, beneficios que le corresponden, historial de entradas compradas y total gastado.

### 7. Registrar membresía del club
**Qué hace:** Convierte a un espectador en socio del club, o cambia su categoría de socio.
**Datos que pide:** DNI del espectador, tipo de membresía (por ejemplo: adherente, activo o vitalicio) y medio de pago de la cuota social.
**Cómo funciona:** Cada tipo de membresía tiene un valor de cuota distinto y otorga beneficios distintos: porcentaje de descuento sobre la entrada, prioridad de compra o acceso sin cargo a determinados sectores.
**Validaciones:** La persona debe estar registrada previamente como espectador.
**Qué muestra:** Confirmación con el número de socio, el tipo de membresía, el valor de la cuota y la fecha de vencimiento.
**Persistencia:** Se guarda en el archivo JSON de socios.

### 8. Consultar beneficios de la membresía
**Qué hace:** Muestra qué le corresponde a cada categoría de socio.
**Datos que pide:** DNI del socio, o la opción de ver el cuadro comparativo de todas las membresías.
**Qué muestra:** Descuento que aplica sobre cada sector, prioridad de compra, cantidad de entradas que puede adquirir por partido y estado de su cuota social.

### 9. Registrar venta de entrada
**Qué hace:** Vende una entrada para un partido. Es la operación principal del sistema.
**Datos que pide:** ID del partido, DNI del espectador (existente o nuevo), sector elegido, cantidad de entradas y medio de pago.
**Cómo calcula el precio:** Parte del precio base del sector y le aplica:
- El **descuento por membresía**, según la categoría de socio (un no socio paga el precio completo).
- Las **promociones vigentes** que correspondan (menores, jubilados, entrada familiar, partidos de menor convocatoria).
- El recargo o descuento del medio de pago.
**Validaciones:**
- Debe haber lugares disponibles en ese sector. Nunca se puede vender por encima de la capacidad.
- Un socio no puede superar el límite de entradas que le permite su membresía.
- La cuota social debe estar al día para acceder a los beneficios.
**Qué muestra:** Entrada emitida con número, partido, sector, ubicación, titular, desglose del precio (precio base, descuentos aplicados y total) y monto final.
**Persistencia:** Se guarda en el archivo JSON de entradas.

### 10. Anular entrada
**Qué hace:** Anula una entrada vendida y libera el lugar en el sector.
**Datos que pide:** Número de entrada y motivo de la anulación.
**Validaciones:** No se puede anular una entrada de un partido ya jugado.
**Qué muestra:** Confirmación de la anulación, el monto a devolver y la capacidad actualizada del sector.

### 11. Configurar promociones y descuentos
**Qué hace:** Define las promociones que después se aplican automáticamente en la venta.
**Datos que pide:** Nombre de la promoción, condición que debe cumplirse (edad, categoría de socio, cantidad de entradas, tipo de partido), porcentaje de descuento y período de vigencia.
**Validaciones:** Los descuentos no pueden superar el 100% ni acumularse por encima de un tope definido por el grupo.
**Qué muestra:** Listado de promociones vigentes con sus condiciones.
**Persistencia:** Se guarda en el archivo JSON de configuración.

### 12. Mostrar estadísticas
**Qué hace:** Muestra la información de gestión del estadio.
**Reportes que debe incluir como mínimo:**
- Recaudación por partido y por sector.
- Porcentaje de ocupación de cada partido y promedio general.
- Comparación entre entradas vendidas a socios y a no socios.
- Ingresos por cuotas sociales.
- Partido con mayor y menor convocatoria.
- Descuentos otorgados en total.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Espectador` como clase base, con `NoSocio`, `SocioAdherente`, `SocioActivo` y `SocioVitalicio` heredando de ella. Cada categoría paga un precio distinto y tiene distintos beneficios. |
| **Interfaces** | El cálculo del precio de la entrada debe resolverse con una interfaz implementada de forma distinta por cada categoría de espectador: el no socio paga el precio completo del sector, mientras que cada categoría de socio aplica su propio descuento y sus propios límites. |
| **Composición** | El `Estadio` está compuesto por sus `Sectores`, y cada sector por sus ubicaciones: sin el estadio, no existen. El `Partido` está compuesto por las entradas emitidas para él. |
| **Enumeraciones** | `Sector` (Popular, Platea, Palco, Preferencial), `TipoMembresia` (NoSocio, Adherente, Activo, Vitalicio), `EstadoEntrada` (Vendida, Anulada, Utilizada) y `MedioDePago`. |
| **Listas** | Colecciones de partidos, espectadores, socios, entradas y promociones. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de partidos, espectadores, socios, entradas y promociones en archivos `.json`. |
| **API externa** | Consulta a una API de fútbol (por ejemplo football-data.org) para cargar automáticamente el fixture con rival, fecha y horario de los próximos partidos. |
| **Menú de consola** | El menú detallado en este documento. |

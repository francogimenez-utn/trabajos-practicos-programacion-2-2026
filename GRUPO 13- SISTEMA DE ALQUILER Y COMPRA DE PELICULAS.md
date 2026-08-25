# GRUPO 13 — Sistema de alquiler y compra de películas

**Integrantes:** Agustín Angelico — Martín Scaricich — Federico Freggiaro

**Descripción del sistema:** Aplicación de consola para la administración de un local de venta y alquiler de películas: catálogo cargado desde una API de cine, registro de clientes, venta y alquiler de títulos con distintos plazos, devoluciones con recargo por atraso, reservas y estadísticas del negocio.

---

## MENÚ PRINCIPAL

```
1. Cargar película
2. Ver catálogo
3. Buscar película
4. Registrar cliente
5. Buscar cliente
6. Comprar película
7. Alquilar película
8. Registrar devolución
9. Reservar película
10. Configurar precios y descuentos
11. Estadísticas
```

---

## DETALLE DE CADA OPCIÓN

### 1. Cargar película
**Qué hace:** Da de alta una película en el catálogo. Puede hacerse de dos formas.
**Alta automática:** El usuario ingresa el título y **el sistema consulta una API de cine** (OMDb o TMDb) que devuelve año, director, género, duración, sinopsis y puntuación. El usuario solo confirma.
**Alta manual:** Si la API no encuentra la película, se cargan todos los datos a mano.
**Datos que pide además:**
- **Clasificación comercial: estreno o clásico** (define el precio y el plazo de alquiler).
- Formato (DVD, Blu-ray o digital).
- Cantidad de copias disponibles.
**Validaciones:** No se puede repetir el mismo título con el mismo formato. Si ya existe, el sistema ofrece sumar copias.
**Qué muestra:** Ficha completa de la película con su ID interno.
**Persistencia:** Se guarda en el archivo JSON del catálogo.

### 2. Ver catálogo
**Qué hace:** Lista las películas disponibles.
**Datos que pide:** Un criterio de filtro:
- Por **género** (comedia, terror, acción, aventura, romance, familiar).
- Solo **estrenos**.
- Solo **clásicos**.
- Todo el catálogo.
**Qué muestra:** ID, título, año, director, género, clasificación, formato, precio de venta, precio de alquiler, copias totales y copias disponibles.

### 3. Buscar película
**Qué hace:** Busca un título puntual.
**Datos que pide:** Título, director o ID.
**Qué muestra:** Ficha completa con la sinopsis traída de la API, disponibilidad actual, cuántas veces fue alquilada y si tiene reservas pendientes.

### 4. Registrar cliente
**Qué hace:** Da de alta un cliente del videoclub.
**Datos que pide:** DNI, nombre, apellido, teléfono, correo electrónico y fecha de nacimiento (necesaria para validar el alquiler de películas con restricción por edad).
**Validaciones:** No se puede repetir el DNI.
**Qué muestra:** Confirmación con el número de cliente asignado.
**Persistencia:** Se guarda en el archivo JSON de clientes.

### 5. Buscar cliente
**Qué hace:** Busca un cliente y muestra su ficha.
**Datos que pide:** DNI, número de cliente o apellido.
**Qué muestra:** Datos personales, alquileres activos con sus vencimientos, historial completo de compras y alquileres, y recargos adeudados si los tuviera.

### 6. Comprar película
**Qué hace:** Registra la venta definitiva de una copia al cliente.
**Datos que pide:** Cliente, película, cantidad y medio de pago.
**Cómo calcula el precio:** Precio de venta según la clasificación de la película: los **estrenos** se venden más caros y los **clásicos** tienen precio reducido. Se aplican los descuentos vigentes (por cantidad de títulos, por cliente frecuente, etc.) y el ajuste del medio de pago.
**Validaciones:** Debe haber copias disponibles. Al confirmar, la copia sale del stock definitivamente.
**Qué muestra:** Comprobante con el detalle de la compra, el desglose del precio y el total.
**Persistencia:** Se guarda en el archivo JSON de operaciones.

### 7. Alquilar película
**Qué hace:** Presta una copia al cliente por un plazo determinado.
**Datos que pide:** Cliente, película, **plazo de alquiler (24 horas, 3 días o una semana)** y medio de pago.
**Cómo calcula el precio:** El precio del alquiler depende del plazo elegido y de la clasificación: un estreno cuesta más que un clásico y suele alquilarse por menos tiempo. Se aplican los descuentos vigentes.
**Validaciones:**
- Debe haber copias disponibles.
- El cliente no puede tener alquileres vencidos ni recargos impagos.
- Si la película tiene restricción por edad, se valida contra la fecha de nacimiento del cliente.
**Qué muestra:** Comprobante con número de alquiler, película, precio y **fecha y hora límite de devolución**.
**Persistencia:** Se guarda en el archivo JSON de operaciones.

### 8. Registrar devolución
**Qué hace:** Registra que el cliente devolvió la copia y la vuelve a poner disponible.
**Datos que pide:** Número de alquiler o DNI del cliente, y el estado en que se devuelve la copia.
**Cómo calcula el recargo:** Si la devolución es posterior a la fecha límite, se cobra un recargo por cada día (o fracción) de atraso. Si la copia vuelve dañada, se suma un cargo adicional.
**Qué muestra:** Días de atraso si los hubo, recargo calculado con su desglose y confirmación de que la copia volvió al stock. Si había una reserva pendiente para ese título, el sistema avisa quién la estaba esperando.

### 9. Reservar película
**Qué hace:** Permite que un cliente quede en lista de espera cuando todas las copias están alquiladas.
**Datos que pide:** Cliente y película.
**Cómo funciona:** Al registrarse la devolución de ese título, el sistema informa que hay un cliente esperándolo.
**Qué muestra:** Confirmación con la posición del cliente en la lista de espera.

### 10. Configurar precios y descuentos
**Qué hace:** Define los valores que usa el sistema para cobrar.
**Datos que pide:**
- Precio de venta y de alquiler de estrenos y de clásicos.
- Valor de cada plazo de alquiler (24 horas, 3 días, semanal).
- Recargo por día de atraso.
- Descuentos vigentes: por cantidad de títulos, por cliente frecuente o promociones por género.
**Validaciones:** Los valores no pueden ser negativos y los descuentos no pueden superar un tope definido por el grupo.
**Persistencia:** Se guarda en el archivo JSON de configuración.

### 11. Estadísticas
**Qué hace:** Muestra un submenú con la información del negocio.
**Reportes que debe incluir como mínimo:**
- **Películas más alquiladas** y géneros más pedidos.
- Comparación entre ingresos por venta e ingresos por alquiler.
- Recaudación de un período.
- Alquileres activos y vencidos.
- Clientes con más operaciones.
- Total cobrado en recargos por atraso.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Operacion` como clase base, con `Venta` y `Alquiler` heredando de ella: el alquiler agrega plazo, vencimiento y devolución, que la venta no tiene. También `Pelicula` → `Estreno` / `Clasico`, con distinto precio y distinto plazo. |
| **Interfaces** | El cálculo del precio debe resolverse con una interfaz implementada de forma distinta por cada operación: la venta cobra un precio fijo según la clasificación, mientras que el alquiler calcula su precio según el plazo elegido y suma recargos por atraso. |
| **Composición** | La `Pelicula` está compuesta por sus `Copias`: sin la película, las copias no existen. Es lo que permite tener varios ejemplares del mismo título. |
| **Enumeraciones** | `Genero` (Comedia, Terror, Accion, Aventura, Romance, Familiar), `Clasificacion` (Estreno, Clasico), `EstadoAlquiler` (Activo, Devuelto, Vencido), `Formato` (DVD, BluRay, Digital) y `PlazoAlquiler` (VeinticuatroHoras, TresDias, Semanal). |
| **Listas** | Colecciones de películas, clientes y operaciones. La lista de espera de reservas también se resuelve sobre una lista. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia del catálogo, los clientes, las operaciones y la configuración de precios en archivos `.json`. |
| **API externa** | Consulta a OMDb o TMDb para cargar automáticamente los datos de una película a partir de su título. |
| **Menú de consola** | El menú detallado en este documento. |


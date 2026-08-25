# GRUPO 7 — Sistema de gestión bibliotecaria

**Integrantes:** Boris Campos Reyna — Massimo Devito — Natalia Martinez

**Descripción del sistema:** Aplicación de consola para la administración de una biblioteca: catálogo de libros y ejemplares, registro de socios, préstamos y devoluciones con cálculo de multas por atraso, reservas y reportes de circulación.

---

## MENÚ PRINCIPAL

```
1. Agregar libro
2. Dar de baja libro
3. Mostrar catálogo completo
4. Buscar libro
5. Ordenar catálogo
6. Registrar socio
7. Ver socios
8. Buscar socio
9. Registrar préstamo
10. Registrar devolución
11. Ver préstamos activos y vencidos
12. Reservar un libro
13. Reportes
```

---

## DETALLE DE CADA OPCIÓN

### 1. Agregar libro
**Qué hace:** Da de alta un libro en el catálogo. Puede hacerse de dos formas.
**Alta automática:** El usuario ingresa el ISBN o el título y **el sistema consulta una API de libros** (OpenLibrary o Google Books) que devuelve título, autor, año de publicación, editorial y cantidad de páginas. El usuario solo confirma y completa los datos que faltan.
**Alta manual:** Si la API no encuentra el libro, se cargan todos los datos a mano.
**Datos que pide además:** Género, ubicación en la estantería y **cantidad de ejemplares** disponibles.
**Validaciones:** No se puede repetir el ISBN. Si el libro ya existe, el sistema ofrece sumar ejemplares.
**Qué muestra:** Ficha del libro cargado con su ID interno.
**Persistencia:** Se guarda en el archivo JSON del catálogo.

### 2. Dar de baja libro
**Qué hace:** Retira un libro o un ejemplar del catálogo (por extravío, deterioro o descarte).
**Datos que pide:** ID del libro, cantidad de ejemplares a dar de baja y motivo.
**Validaciones:** No se puede dar de baja un ejemplar que esté prestado. El sistema debe avisar quién lo tiene.
**Qué muestra:** Confirmación con los ejemplares restantes.

### 3. Mostrar catálogo completo
**Qué hace:** Lista todo el catálogo de la biblioteca.
**Qué muestra:** ID, título, autor, género, ejemplares totales, ejemplares disponibles y cuántos están prestados en este momento.

### 4. Buscar libro
**Qué hace:** Busca un libro puntual.
**Datos que pide:** ID, título, autor, ISBN o género.
**Qué muestra:** Ficha completa del libro, disponibilidad y su historial de préstamos.

### 5. Ordenar catálogo
**Qué hace:** Muestra el catálogo ordenado según el criterio que se elija.
**Criterios:** Por título, por autor, por año de publicación, por cantidad de ejemplares disponibles, por ID o por cantidad de veces prestado (los más pedidos).
**Qué muestra:** El listado reordenado según el criterio elegido.

### 6. Registrar socio
**Qué hace:** Da de alta un socio de la biblioteca.
**Datos que pide:** DNI, nombre, apellido, teléfono, correo electrónico y **tipo de socio** (estudiante, docente o general).
**Validaciones:** No se puede repetir el DNI.
**Qué muestra:** Confirmación con el número de socio asignado y las condiciones de préstamo que le corresponden según su tipo.
**Persistencia:** Se guarda en el archivo JSON de socios.

### 7. Ver socios
**Qué hace:** Lista los socios registrados.
**Datos que pide:** Opcionalmente, un filtro por tipo de socio o por estado (activo / con préstamos vencidos / con multas impagas).
**Qué muestra:** Número de socio, nombre, tipo, cantidad de préstamos activos y si adeuda multas.

### 8. Buscar socio
**Qué hace:** Busca un socio y muestra su ficha.
**Datos que pide:** DNI, número de socio o apellido.
**Qué muestra:** Datos personales, préstamos activos con sus fechas de vencimiento, historial completo de préstamos y multas adeudadas.

### 9. Registrar préstamo
**Qué hace:** Presta un ejemplar a un socio. Es la operación principal del sistema.
**Datos que pide:** Número de socio y libro a prestar.
**Cómo funciona:** El plazo de devolución depende del tipo de socio: por ejemplo, un estudiante puede llevarlo menos días que un docente. La cantidad máxima de libros simultáneos también varía según el tipo.
**Validaciones:**
- Debe haber ejemplares disponibles.
- El socio no puede superar su límite de préstamos simultáneos.
- El socio no puede tener préstamos vencidos ni multas impagas.
**Qué muestra:** Comprobante con número de préstamo, socio, libro, fecha de retiro y **fecha límite de devolución**.
**Persistencia:** Se guarda en el archivo JSON de préstamos.

### 10. Registrar devolución
**Qué hace:** Registra que el socio devolvió el ejemplar y lo vuelve a poner disponible.
**Datos que pide:** Número de préstamo o número de socio, y el estado en que se devuelve el libro.
**Cómo calcula la multa:** Si la devolución es posterior a la fecha límite, se cobra una multa por cada día de atraso. **El monto por día depende del tipo de socio.** Si el libro se devuelve dañado, se suma un recargo.
**Qué muestra:** Días de atraso (si los hubo), multa calculada con su desglose y confirmación de que el ejemplar volvió al stock.

### 11. Ver préstamos activos y vencidos
**Qué hace:** Muestra el estado de la circulación.
**Datos que pide:** Un criterio de filtro: todos los activos, solo los vencidos, por socio o por libro.
**Qué muestra:** Número de préstamo, socio, libro, fecha de retiro, fecha límite y días restantes o días de atraso, destacando los vencidos.

### 12. Reservar un libro
**Qué hace:** Permite que un socio quede en lista de espera cuando todos los ejemplares están prestados.
**Datos que pide:** Número de socio y libro.
**Cómo funciona:** Cuando se registra la devolución de ese libro, el sistema avisa que hay un socio esperándolo y quién es.
**Qué muestra:** Confirmación con la posición del socio en la lista de espera.

### 13. Reportes
**Qué hace:** Muestra un submenú con la información de la biblioteca.
**Reportes que debe incluir como mínimo:**
- Libros más prestados y géneros más solicitados.
- Socios con más préstamos.
- Préstamos vencidos y multas adeudadas.
- Total recaudado en multas de un período.
- Estado general del catálogo: cantidad de títulos, ejemplares totales y ejemplares prestados.

---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Socio` como clase base, con `SocioEstudiante`, `SocioDocente` y `SocioGeneral` heredando de ella. Cada tipo tiene distinto plazo de préstamo, distinto límite de libros y distinto valor de multa. |
| **Interfaces** | El cálculo de la multa y del plazo de devolución debe resolverse con una interfaz implementada de forma distinta por cada tipo de socio. |
| **Composición** | El `Libro` está compuesto por sus `Ejemplares`: sin el libro, los ejemplares no existen. Es lo que permite tener varias copias del mismo título. |
| **Enumeraciones** | `TipoSocio` (Estudiante, Docente, General), `EstadoPrestamo` (Activo, Devuelto, Vencido), `Genero` (Novela, Ensayo, Técnico, Infantil, etc.) y `EstadoEjemplar` (Disponible, Prestado, Dañado, DeBaja). |
| **Listas** | Colecciones de libros, socios y préstamos. La lista de espera de reservas y el ordenamiento del catálogo también se resuelven sobre listas. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia del catálogo, los socios y los préstamos en archivos `.json`. |
| **API externa** | Consulta a OpenLibrary o Google Books para cargar automáticamente los datos de un libro a partir de su ISBN o título. |
| **Menú de consola** | El menú detallado en este documento. |


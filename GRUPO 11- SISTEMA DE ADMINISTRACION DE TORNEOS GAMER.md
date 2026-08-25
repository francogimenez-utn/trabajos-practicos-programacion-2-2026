# GRUPO 11 — Sistema de administración de torneos gamer

**Integrante:** Dylan Velázquez

**Descripción del sistema:** Aplicación de consola para organizar torneos de videojuegos: creación de torneos con distintos formatos de competencia, inscripción de jugadores, generación de enfrentamientos, carga de resultados, tabla de posiciones y definición del campeón.

---

## MENÚ PRINCIPAL

```
1. Crear nuevo torneo
2. Ver torneos
3. Registrar jugador
4. Inscribir jugador a un torneo
5. Ver lista de participantes
6. Generar o ver enfrentamientos
7. Cargar resultado de partida
8. Ver tabla de posiciones
9. Terminar torneo y ver ganadores
```

---

## DETALLE DE CADA OPCIÓN

### 1. Crear nuevo torneo
**Qué hace:** Da de alta un torneo. Sin torneo creado no se puede inscribir a nadie ni generar partidas.
**Datos que pide:**
- Nombre del torneo.
- **Videojuego:** el usuario ingresa el título y **el sistema consulta una API de videojuegos** (por ejemplo RAWG) que devuelve género, plataformas, fecha de lanzamiento y puntuación. Si no lo encuentra, se carga a mano.
- **Formato de competencia: eliminación directa o todos contra todos.**
- Cantidad máxima de participantes y fecha de inicio.
- Premio, si lo hubiera.
**Validaciones:** No se puede repetir el nombre del torneo. En eliminación directa, la cantidad de participantes debe permitir armar el cuadro.
**Qué muestra:** Ficha del torneo con su ID, el juego con los datos traídos de la API, el formato y el estado inicial (Creado).
**Persistencia:** Se guarda en el archivo JSON de torneos.

### 2. Ver torneos
**Qué hace:** Lista los torneos del sistema, tanto los que están en juego como los ya terminados.
**Datos que pide:** Opcionalmente, un filtro por estado (creado / en curso / finalizado) o por videojuego.
**Qué muestra:** ID, nombre, juego, formato, cantidad de inscriptos, estado y, en los finalizados, el campeón.

### 3. Registrar jugador
**Qué hace:** Da de alta un jugador en el sistema.
**Datos que pide:** Nickname, nombre real, edad, correo electrónico y plataforma principal (PC, PlayStation, Xbox, Nintendo).
**Validaciones:** No se puede repetir el nickname ni el correo.
**Qué muestra:** Confirmación con el ID de jugador asignado.
**Persistencia:** Se guarda en el archivo JSON de jugadores.

### 4. Inscribir jugador a un torneo
**Qué hace:** Anota a un jugador ya registrado en un torneo.
**Datos que pide:** ID del torneo e ID o nickname del jugador.
**Validaciones:**
- El torneo debe estar en estado *Creado* (una vez que empezó, no se puede sumar gente).
- No se puede superar el cupo máximo de participantes.
- El jugador no puede estar inscripto dos veces en el mismo torneo.
**Qué muestra:** Confirmación con la cantidad de inscriptos y los lugares que quedan libres.

### 5. Ver lista de participantes
**Qué hace:** Muestra quiénes están anotados en un torneo.
**Datos que pide:** ID del torneo.
**Qué muestra:** Nickname, plataforma, partidas jugadas, ganadas y perdidas de cada participante dentro de ese torneo.

### 6. Generar o ver enfrentamientos
**Qué hace:** Arma el fixture del torneo y permite consultarlo. Es la opción donde el formato de competencia marca la diferencia.
**Cómo genera los enfrentamientos según el formato:**
- **Eliminación directa:** arma el cuadro por rondas (octavos, cuartos, semifinal, final). Solo avanza el ganador de cada llave, y cada ronda se genera cuando terminó la anterior.
- **Todos contra todos:** genera de una vez todas las combinaciones posibles de jugadores, de manera que cada uno enfrente a todos los demás una vez.
**Validaciones:** El torneo debe tener participantes suficientes. No se pueden regenerar los enfrentamientos de un torneo ya empezado.
**Qué muestra:** El fixture completo con cada partida numerada, los dos jugadores, la ronda a la que pertenece y su estado (pendiente o jugada). Al generarlos, el torneo pasa a estado *En curso*.
**Persistencia:** Se guarda en el archivo JSON de partidas.

### 7. Cargar resultado de partida
**Qué hace:** Registra quién ganó un enfrentamiento.
**Datos que pide:** Número de partida, resultado (marcador de cada jugador) y fecha en que se jugó.
**Cómo funciona según el formato:**
- **Eliminación directa:** el ganador avanza automáticamente a la ronda siguiente y el perdedor queda eliminado.
- **Todos contra todos:** se suman los puntos correspondientes (por ejemplo, 3 por victoria y 0 por derrota) a la tabla de posiciones.
**Validaciones:** La partida no puede estar ya jugada. Los dos jugadores deben pertenecer a ese enfrentamiento. En eliminación directa no puede haber empate.
**Qué muestra:** El resultado cargado y, si corresponde, el nuevo enfrentamiento que se generó en la ronda siguiente.

### 8. Ver tabla de posiciones
**Qué hace:** Muestra cómo va el torneo. Lo que se muestra depende del formato.
**Datos que pide:** ID del torneo.
**Qué muestra:**
- **Todos contra todos:** tabla ordenada por puntos, con partidas jugadas, ganadas, perdidas y diferencia de marcador como criterio de desempate.
- **Eliminación directa:** el cuadro actualizado, indicando en qué ronda quedó eliminado cada jugador y quiénes siguen en carrera.

### 9. Terminar torneo y ver ganadores
**Qué hace:** Cierra el torneo y define el podio.
**Datos que pide:** ID del torneo y confirmación.
**Validaciones:** No se puede cerrar un torneo con partidas pendientes. El sistema debe informar cuáles faltan jugar.
**Qué muestra:** El campeón, el segundo y el tercer puesto, más las estadísticas finales del torneo: cantidad de partidas jugadas, jugador con más victorias y resumen de cada participante. Al confirmar, el torneo pasa a estado *Finalizado* y queda guardado en el historial.


---

## REQUISITOS TÉCNICOS OBLIGATORIOS DEL TP

Estas funcionalidades deben resolverse aplicando los temas de la materia. El diagrama UML tiene que mostrar dónde se aplica cada uno.

| Requisito | Dónde se aplica en este sistema |
|---|---|
| **Herencia** | `Torneo` como clase base, con `TorneoEliminacionDirecta` y `TorneoTodosContraTodos` heredando de ella. Cada uno arma sus enfrentamientos y define sus posiciones de manera distinta. |
| **Interfaces** | La generación del fixture y el cálculo de la tabla de posiciones deben resolverse con una interfaz implementada de forma distinta por cada formato: por rondas eliminatorias en uno, por combinaciones y puntaje acumulado en el otro. |
| **Composición** | El `Torneo` está compuesto por sus rondas y enfrentamientos: sin el torneo, esas partidas no existen. Cada `Partida` está compuesta por su resultado. |
| **Enumeraciones** | `FormatoTorneo` (EliminacionDirecta, TodosContraTodos), `EstadoTorneo` (Creado, EnCurso, Finalizado), `EstadoPartida` (Pendiente, Jugada) y `Plataforma` (PC, PlayStation, Xbox, Nintendo). |
| **Listas** | Colecciones de jugadores, torneos, inscriptos y partidas. La tabla de posiciones se arma ordenando una lista. |
| **Encapsulamiento** | Todos los atributos privados, accesibles mediante propiedades. |
| **JSON** | Persistencia de jugadores, torneos y partidas en archivos `.json`. |
| **API externa** | Consulta a una API de videojuegos (por ejemplo RAWG) para traer los datos del juego sobre el que se disputa el torneo. |
| **Menú de consola** | El menú detallado en este documento. |

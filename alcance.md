## 1. Alcance del Proyecto: FutBot

### 1.1 Introducción
El objetivo del proyecto es desarrollar una plataforma web cliente/servidor que permita a los usuarios jugar a "FutBot": un simulador de fútbol en formato multijugador estructurado mediante la programación de comportamientos automáticos.


### 1.2 Tecnologías Propuestas
Para la implementación de la plataforma se proponen las siguientes tecnologías:

* **Backend / API Framework:** FastAPI (Python).
* **Base de Datos y ORM:** SQLAlchemy.
* **Frontend:** React.js.
* **Comunicación en Tiempo Real:** WebSockets para la simulación sincrónica de partidos tick a tick. 


### 1.3 Requisitos Funcionales
El sistema proporcionará las siguientes capacidades principales para gestionar la experiencia de juego, estructuradas de forma modular:


*   **Gestión de Cuentas y Equipos:** El sistema proveerá una interfaz para que los usuarios puedan registrarse, solicitando un nombre, mail y contraseña. Cada usuario tendrá que crear un único equipo, al que deberá nombrar (el nombre no será único), eligiendo un logo que servirá para identificar al equipo durante los partidos y en los rankings. El usuario tendrá la opción de cambiar el nombre de usuario, eliminar su cuenta (en cuyo caso también se eliminará su equipo y los datos asociados a él), y también se podrá modificar el nombre del equipo y su logo.


*   **Gestión de Jugadores y Atributos:** Cada usuario podrá crear la cantidad de jugadores que desee, pero deberá seleccionar 6 para participar en cada partido. Cada jugador requerirá un nombre, un número de camiseta único y 5 atributos inmutables denominados PACSS: Power, Agility, Control, Speed y Strength. En total, la suma de los valores de estos atributos deberá dar exactamente 300 puntos, con cada atributo tomando un valor entre 20 y 100 puntos. Al unirse a una liga o a un partido amistoso, el sistema permitirá a los usuarios elegir una alineación y una formación. 


*   **Programación de Comportamientos:** Se podrán crear comportamientos que determinarán el estilo de juego de cada jugador. El sistema exigirá un mínimo de 1 comportamiento creado por equipo, sin establecer un límite máximo. Cada comportamiento constará de un nombre y una descripción, y podrá ser asignado a un jugador. Durante el transcurso de un partido, se brindará la posibilidad de intercambiar estos comportamientos, aunque los mismos no podrán ser modificados (editados) en ese momento.


*   **Formatos de Competición:** El juego soportará la elección entre dos formatos de juego:
    *   **Ligas:** Se podrá crear una liga nueva o unirse a una ya existente. Requerirán un mínimo de 3 equipos participantes y un cupo máximo establecido por el anfitrión al momento de crearse. Las ligas podrán ser públicas (sin contraseña) o privadas (con contraseña).
    *   **Amistosos:** Consistirá en un único partido entre 2 equipos, permitiendo crear uno nuevo o unirse a la espera de un rival.
    *   **Nota aclaratoria:** El equipo del usuario podrá jugar varias ligas y/o partidos amistosos al mismo tiempo sin limitaciones.


*   **Salas de Espera y Emparejamiento:** Existirá una sala de espera previa a los encuentros. En el formato liga, el anfitrión podrá iniciar la competencia una vez cumplido el mínimo de 3 equipos o, si se llena el cupo, el sistema la iniciará automáticamente. En el formato amistoso, el partido iniciará de manera automática luego de que se una un rival. Si el usuario que creó una liga o un amistoso abandona la sala de espera, los rivales serán redirigidos afuera y la sala dejará de existir automáticamente. 


*   **Desarrollo del partido:** El sistema proveerá la lógica necesaria para jugar los partidos en vivo, con las siguientes características:
    *    El tiempo del partido estará estructurado en intervalos discretos denominados ticks. Durante cada tick, cada jugador ejecutará el código de su comportamiento asignado por un período mínimo de un tick. Los usuarios podrán solicitar el intercambio del comportamiento de un jugador, de modo que la modificación se procese de forma previa y, a partir del siguiente tick, el jugador comience a ejecutar el nuevo comportamiento. Por su parte, la duración total del partido será establecida con anterioridad por el creador de la liga o del amistoso.
    *    El encuentro se dividirá en 2 tiempos y tendrá 3 pausas: 2 cooling break y 1 entretiempo, equidistantes en términos a la duración total del partido. Durante el partido, se pueden hacer 3 cambios de jugadores en total, repartidos entre: el primer cooling break, el entretiempo y el segundo cooling break; siendo un solo cambio en cada pausa. Estos cambios no se pueden acumular si no se utilizan.
    *    Además, el sistema proveerá una interfaz donde se podrán ver los jugadores propios con sus atributos PACSS y comportamientos asignados y también los jugadores del rival y sus atributos -aunque no podrá ver sus comportamientos-.


*   **Puntuaciones, Fixtures y Rankings:** El sistema aplicará un esquema de puntuaciones de 3 puntos por victoria, 1 punto por empate y 0 puntos por derrota.
    *   Para las ligas, el sistema generará un fixture de todos contra todos. Cada liga tendrá su tabla de posiciones, ordenada en base a los siguientes criterios de desempate (en orden de prioridad):
         *   **1) Puntos:** Los puntos obtenidos de cada equipo, bajo el esquema de puntuación del sistema.
         *   **2) Diferencia de goles:** En caso de empate de puntos entre dos o más equipos, el sistema desempatará por diferencia de goles, computados con la resta entre Goles a favor - Goles en contra. 
    *   Habrá un ranking global de equipos donde se anotarán los puntos y goles obtenidos de cada equipo en todos los amistosos y ligas públicas que dispute, con el mismo criterio de desempate que el de la tabla de liga; no se contabilizarán las ligas privadas para este ranking.
    *   Al finalizar cada partido, se mostrará el resultado y las puntuaciones en un fixture (para ligas), o simplemente el resultado final (para amistosos).


### 1.4 Restricciones Funcionales (Exclusiones)
Para delimitar correctamente las características de la plataforma, el sitio **no deberá**:


*   Permitir amistosos privados o con contraseña.
*   Permitir a los usuarios manejar a los jugadores mediante el teclado, mouse u otros periféricos; las acciones dependerán exclusivamente del código.
*   Simular el cansancio físico de los jugadores con el paso de los minutos ni incluir variables climáticas.
*   Incluir árbitros, posiciones de fuera de juego (offside), faltas ni la figura del arquero (uso de las manos).
*   Permitir a los usuarios ver el código de comportamiento de los jugadores enemigos y/o utilizarlos; solo podrán ver sus estadísticas.
*   Permitir modificar los atributos PACSS después de la creación del jugador ni incluir un sistema de entrenamiento o progresión.
*   Utilizar técnicas de polling para la transmisión síncrona de los partidos.
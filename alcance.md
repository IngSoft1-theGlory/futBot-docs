# 1. Alcance del Proyecto: FutBot

El objetivo del proyecto es desarrollar una plataforma web que permita a los usuarios jugar a "FutBot", un simulador de fútbol en formato multijugador estructurado mediante la programación de comportamientos automáticos. 

El sitio tendrá:

• Una interfaz para que los usuarios puedan crear un usuario, solicitando nombre, mail y contraseña.

• La opción de que los usuarios puedan crear un unico equipo, solicitando nombre y logo. El nombre del equipo deberá ser único { y el logo será el avatar para identificar al equipo durante un partido y en rankings. ?? } 

• La opción de que los usuarios puedan crear jugadores para su equipo. Cada equipo deberá tener un mínimo de 6 jugadores y un máximo de N. Cada jugador tendrá nombre, número de camiseta único y 5 atributos (PACSS), los cuales serán inmutables.

• La opción de que los usuarios puedan elegir una alineación (y formacion?) al unirse a una liga o a un partido amistoso.

• La opción de que los usuarios puedan crear comportamientos para sus jugadores. Deberán existir un mínimo de 1 y un máximo de N comportamientos. Cada comportamiento tendrá nombre e identificador y deberá ser asignado a un jugador.

• La posibilidad de intercambiar comportamientos durante un partido, sin modificarlos en ese momento.

• La opción de que los usuarios puedan seleccionar entre dos formatos de juego: ligas y amistosos.

• La opción de crear una liga nueva o unirse a una liga ya existente. Las ligas podrán ser públicas, sin contraseña, o privadas, con contraseña, y tendrán un mínimo de 3 equipos y un máximo de N.

• La opción de crear un partido amistoso nuevo o unirse a un amistoso a la espera de un rival. Los amistosos serán un único partido entre 2 equipos y estarán prohibidos los amistosos con contraseña.

• Una sala de espera para las ligas y los amistosos. En las ligas, el anfitrión podrá iniciar la liga una vez cumplido el mínimo de 3 equipos. En los amistosos, el partido iniciará luego de que se una un rival, cuando el anfitrion lo disponga?

• La posibilidad de que, si el usuario que creó una liga o un amistoso abandona la sala de espera, los rivales sean redirigidos afuera de la sala y esta deje de existir automáticamente.

• La lógica necesaria para simular los partidos. En las ligas se jugará un tiempo de [N minutos] por partido y en los amistosos de [N minutos]. Los partidos tendrán 2 pausas de hidratación (cooling break) y 1 medio tiempo (half-time), dividiéndose en 4 cuartos equitativos.

• Un sistema de puntuaciones de 3 pts por victoria, 1 pt por empate y 0 pts por derrota. ¿?

• Un fixture para las ligas donde se registrarán los puntos y goles anotados.

• Un ranking global de equipos. En las ligas públicas y en los amistosos, los puntos y goles se sumarán al ranking global; en las ligas privadas, no.

• Una pantalla al finalizar cada partido. En las ligas se mostrará el resultado y las puntuaciones en un fixture, y en los amistosos se mostrará el resultado. Finalmente, se mostrará un ranking global con las puntuaciones de los equipos.

El sitio no deberá:

• Permitir a los usuarios manejar a los jugadores mediante el teclado, mouse u otros periféricos; las acciones dependerán exclusivamente del código.

• Simular el cansancio físico de los jugadores con el paso de los minutos ni incluir variables climáticas.

• Incluir árbitros, posiciones de fuera de juego (offside), faltas ni la figura del arquero (uso de las manos).

• Permitir a los usuarios ver el código de comportamiento de los jugadores enemigos; solo podrán ver sus estadísticas.

• Permitir modificar los atributos PACSS después de la creación del jugador ni incluir un sistema de entrenamiento o progresión.

• Utilizar técnicas de polling para la transmisión síncrona de los partidos.


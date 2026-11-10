# **HOW TO PLAY FUTBOT**

## **Qué es FutBot**

FutBot es un simulador de fútbol 3 contra 3 en el que **no controlás a los jugadores: los programás**. Cada jugador tiene un *comportamiento*, un pequeño código en Python que decide qué hacer en cada instante. Armas tu equipo, escribis comportamientos nuevos o usas los predeterminados y miras cómo juega contra el equipo de otra persona.

## **Empezar**

Creá tu cuenta desde **Registrarse** e iniciá sesión. Al entrar llegás a la pantalla de inicio, que tiene las zonas: jugar (ligas y amistosos), tu equipo (jugadores y comportamientos), y el ranking.

## **Armar tu equipo**

Desde **Crear jugador** le ponés un nombre, un número de camiseta (no se puede repetir en el equipo) y cinco atributos: Potencia, Agilidad, Control, Velocidad y Fuerza. Cada atributo vale entre 20 y 100 puntos y el total de puntos **tiene que sumar exactamente 300**, así que mejorar uno obliga a bajar otro.

Para jugar necesitás al menos tener **6 jugadores** creados: 3 titulares y 3 suplentes.

## **Comportamientos**

En **Comportamientos** ves la lista. Vienen tres de fábrica y **no se pueden editar**: *Neutro* (se queda quieto y patea si la pelota está a su alcance), *Ofensivo* (corre a la pelota y patea al arco) y *Defensor* (cuida su zona y sale si la pelota se acerca). Con **Crear comportamiento** hacés los tuyos.

Un comportamiento es una función `on_tick(api)` que se ejecuta en cada instante del partido. Este es el Ofensivo:

```py
def on_tick(api):
    gs = api.get_game_state()
    ball_to_player = gs.ball_position.distance_to(gs.my_position)
    kick_direction = gs.ball_position.direction_to(gs.opponent_goal)

    if ball_to_player <= api.get_reach_distance():
        api.kick_ball(kick_direction, 1.0)
    else:
        api.run_to(gs.ball_position, 1.0)
```

## **Qué podés usar en el código**

Cuando creás un comportamiento, lo escribís con código usando las acciones del jugador: \`api.idle()\` (quedarse quieto), \`api.run\_to(punto, velocidad)\` y \`api.kick\_ball(direccion, potencia)\`. 

La velocidad y la potencia van de 0.0 a 1.0. En cada instante vale la \*\*última acción que pediste. 

Para decidir qué acción usar, \`api.get\_game\_state()\` te da la posición de la pelota, la tuya, la de tus compañeros y rivales, y la de ambos arcos. Los puntos y direcciones se manejan con \`Point2D\` y \`Vector2D\`, que tienen \`distance\_to\`, \`direction\_to\` y \`normalize\`. La cancha mide 105 por 60 metros. La pantalla de comportamientos tiene un desplegable de ayuda con la lista completa.

## **Director técnico (DT)** 

El DT es un comportamiento especial, de tipo **DT**, que mira el partido y toma decisiones por vos: cambiar el comportamiento de un jugador durante el partido o hacer una sustitución en pausas. Se programa igual que los otros (con `on_tick(api)`), pero con sus propias acciones, estas aparecen en el desplegable de ayuda.

Se elige para una **liga en curso** y rige desde el partido siguiente, por lo que el primer partido de la liga nunca tiene DT. Si el código del DT falla, el partido se juega igual y el comportamiento del DT no se ejecuta.

## **Reglas del código**

Por seguridad, el código **no puede** usar `for`, `while`, `import` ni comprensiones de listas. Solo tenés funciones básicas (`abs`, `min`, `max`, `len`, `round`, `sum`, `sorted`, entre otras) y el módulo `math`. Si algo no está permitido o el código falla, ese jugador se queda quieto en ese instante, pero el partido sigue.

## **Amistosos**

Un amistoso es un partido suelto. En **Crear amistoso** elegís la duración (2 o 5 minutos) y armás tu alineación: una formación (**2-1** o **1-2**), quiénes son titulares y suplentes, y qué comportamiento lleva cada uno. Después esperás en la sala de espera.

Otra persona puede verlo en **Amistosos disponibles**, elegir su propia alineación y unirse. Apenas se une el rival, el partido arranca. Los amistosos no suman al ranking.

## **Ligas**

En **Crear liga** elegís el nombre, la duración de los partidos, la cantidad de equipos (**4, 6, 8, 10 o 12**: siempre par, para que en cada fecha todos jueguen), si es **pública** o **privada** (con contraseña) y si los empates se definen **con penales** o se dejan como empate.

Los demás se anotan desde **Ligas disponibles**. La liga arranca sola cuando se llena, o antes si quien la creó toca **Iniciar liga** (con cantidad par de equipos y un mínimo de 4). Hasta ese momento, quien la creó puede **Cancelarla** y los demás pueden **Abandonarla**. Con la liga en curso ves el fixture y la tabla de posiciones, y podés **Editar alineación**: los cambios valen desde tu próximo partido hasta el final de la **Liga** o hasta que la vuelvas a cambiar.

## **Durante el partido**

Mirás el partido en vivo: la cancha, el marcador y el reloj. Dura 2 o 5 minutos y tiene **tres pausas** (al 25 %, 50 % y 75 % del tiempo): dos *Cooling Break* de 10 segundos y el *Half-Time* de 20\. En el Half-Time los equipos cambian de lado.

En cada pausa podés **sustituir** a un titular por un suplente (un cambio por equipo y por pausa) y **cambiar el comportamiento** de un titular. Fuera de las pausas se pueden reasignar los comportamientos de los jugadores y planificar la **sustitución,** que se ejecutará en la pausa siguiente.

## **Empates y penales**

En una liga con penales, si el partido termina empatado se define con una tanda: cada equipo patea con sus titulares, hasta 3 rondas. Si siguen igualados, se sortea el ganador. Se juega con comportamientos propios del sistema, uno que **Ataja** y otro que **Patea,** así que no tenés que hacer nada más que mirar.

## **Resultado y ranking**

Al terminar, la pantalla de resultado final muestra el marcador (y los penales, si hubo). En las ligas, cada partido da **3 puntos por victoria, 1 por empate y 0 por derrota** (2 puntos si se gana por penales).

El **ranking** global se arma con los resultados de las **ligas públicas**. Los amistosos y las ligas privadas no suman.

## **Preguntas frecuentes**

* **Mi jugador o DT no hace nada.** Probablemente su código usa algo no permitido o tiene un error. Revisá la ayuda del desplegable.  
* **No puedo iniciar mi liga.** Necesita una cantidad par de equipos inscriptos (de 4 a 12).  
* **El DT no cambió nada en el primer partido.** Es lo esperado: rige desde el siguiente.


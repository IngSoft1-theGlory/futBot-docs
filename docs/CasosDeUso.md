# Especificación de Casos de Uso

## 1. Listado de Casos de Uso

### **Sección 1: Usuario / Sesión** {Noah}
* **CU-1:** Crear un Usuario 
* **CU-2:** Modificar Nombre de Usuario 
* **CU-3:** Eliminar Usuario 
* **CU-4:** Iniciar sesión
* **CU-5:** Cerrar sesión 

### **Sección 2: Equipo Y Ranking** {Ema}
* **CU-6:** Crear un Equipo 
* **CU-7:** Modificar Nombre del Equipo
* **CU-8:** Modificar logo del equipo
* **CU-9:** Mirar el ranking de equipos
* **CU-10:** Ver el perfil de un equipo

### **Sección 3: Jugadores y Comportamientos** {Cris}
* **CU-11:** Crear Comportamientos
* **CU-12:** Crear Jugador
* **CU-13:** Intercambiar comportamiento de un jugador durante el partido
* **CU-14:** Cambiar Nombre Jugador
* **CU-15:** Cambiar Numero de Jugador
* **CU-16:** Eliminar Comportamiento
* **CU-17:** Eliminar Jugador
* **CU-18:** Modificar nombre del Comportamiento
* **CU-19:** Modificar la descripción de un comportamiento
* **CU-20:** Asignar un Comportamiento a un Jugador
* **CU-21:** Elegir una alineación (Jugadores y Comportamientos)
* **CU-22:** Elegir una formación (Posiciones en la cancha)
* **CU-23:** Modificar actividad del Comportamiento

### **Sección 4: Ligas** {Fer}
* **CU-24:** Crear Liga 
* **CU-25:** Unirse a una Liga
* **CU-26:** Cancelar una liga
* **CU-27:** Iniciar una liga
* **CU-28:** Ver Todos los detalles de la liga
* **CU-29:** Abandonar una liga

### **Sección 5: Partido Amistoso** {Alex}
* **CU-30:** Crear un Partido Amistoso 
* **CU-31:** Cancelar amistoso
* **CU-32:** Unirse a un amistoso

### **Sección 6: Transcurso del Partido** {Orli} 
* **CU-33:** Ver atributos de un jugador en el partido
* **CU-34**  Ver comportamiento del jugador en partido
* **CU-35:** Ver el resultado final de un partido
* **CU-36:** Intercambiar jugador en partido
* **CU-37:** Salir del partido (en curso)

---

## 2. Desarrollo de Casos de Uso 

### CU-1: Crear un Usuario

### CU-2: Cambiar Nombre de Usuario 

### CU-3: Eliminar Usuario 

### CU-4: Iniciar sesión

### CU-5: Cerrar sesión 

### CU-6: Crear un Equipo
* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe estar logueado
  * El usuario no debe tener un equipo existente
* **Escenario Principal:**
  1. El usuario registra un nombre para su equipo, sin limitaciones porque no es único.
  2. El usuario selecciona un logo para su equipo.
* **Escenarios Alternativos:**
  * 1a. El usuario deja vacío el campo de nombre de equipo.
  * 1b. El sistema le muestra un mensaje al usuario de que el campo no puede quedar vacío. 
  * 2a. El usuario ingresa un logo invalido o no ingresa ningún logo para su equipo.
  * 2b. El sistema le muestra por mensaje al usuario que el logo es invalido o vacío. 

### CU-7: Modificar Nombre del Equipo
* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe tener un equipo creado
* **Escenario Principal:**
  1. El usuario ingresa al apartado del equipo
  2. El sistema muestra por pantalla la plantilla del equipo
  3. El usuario modifica el nombre del equipo eligiendo un nombre nuevo
  4. El sistema verifica que no esté vacío el nombre nuevo
  5. El sistema realiza el cambio de nombre 
  6. El usuario confirma los cambios y regresa al apartado anterior
  7. El sistema muestra por pantalla que el cambio fue exitoso y lleva al usuario al apartado anterior
* **Escenarios Alternativos:** 
  *  1a. El usuario sale del apartado equipo sin hacer cambios
  *  1b. El sistema cierra la gestión de equipo y vuelve al apartado anterior
  *  3a. El usuario ingresa al apartado equipo estando en un partido o liga
  *  3b. El sistema le impide modificar el nombre del equipo
  *  4a. El usuario ingresa un nombre vacío para el equipo  
  *  4b. El sistema no modifica el nombre del equipo ya que la casilla está vacía

### CU-8: Modificar Logo del Equipo
* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe tener un equipo creado
* **Escenario Principal:**
  1. El usuario ingresa al apartado del equipo
  2. El sistema muestra por pantalla la plantilla del equipo
  3. El usuario modifica el logo del equipo
  4. El sistema verifica que no esté vacío y modifica el logo correctamente
  5. El usuario confirma los cambios y regresa al apartado anterior
  6. El sistema muestra por pantalla que el cambio fue exitoso y lleva al usuario al apartado anterior
* **Escenarios Alternativos:**
  *  1a. El usuario sale del apartado equipo sin hacer cambios
  *  1b. El sistema cierra la gestión de equipo y vuelve al apartado anterior
  *  3a. El usuario ingresa al apartado equipo estando en un partido o liga
  *  4a. El sistema le impide modificar el logo del equipo
  *  3b. El usuario no coloca ningún logo nuevo o ingresa un formato invalido
  *  4b. El sistema no valida el logo y le pide que ingrese un logo nuevo

### CU-9: Mirar el Ranking Global de Equipos
* **Actor:**
   * Usuario
* **Precondición:**
   * El usuario debe estar logueado
* **Escenario Principal:**
   1. El usuario accede a la página principal
   2. El sistema muestra la lista de todos los equipos ordenados en base a sus estadísticas. 
* **Escenarios Alternativos:**
   * 2a. El sistema detecta que no existen equipos y muestra la lista de equipos vacía.

### CU-10: Ver el Perfil de un Equipo 
* **Actor:**
   * Usuario
* **Precondición:**
   * El usuario debe estar logueado 
* **Escenario Principal:**
   1. El usuario selecciona ver perfil de un equipo
   2. El sistema le muestra por pantalla las estadísticas del equipo: Puntos, Goles a favor, Goles en contra, Victorias, Derrotas y Posición en el ranking.
* **Escenarios Alternativos:**
   * 1a. El usuario sale del perfil del equipo visitado
   * 1b. El sistema muestra por pantalla el apartado anterior

### CU-11: Crear Comportamientos

* **Actor:**
  * Usuario 
* **Precondición:**
  * El usuario debe haber creado un equipo
* **Escenario Principal:**
  1. El usuario accede a la sección de creación de comportamientos de la aplicación.
  2. El usuario ingresa el nombre y una breve descripción del comportamiento. 
  3. El usuario escribe o adjunta el código fuente en lenguaje Python que definirá las acciones del jugador para la duración de un tick.
  4. El usuario hace clic en el botón para guardar o crear el comportamiento.
  5. El sistema valida que los campos requeridos (Nombre y Código) no estén vacíos y que el código fuente cumpla con las restricciones de seguridad.
  6. El sistema almacena el nuevo comportamiento en la biblioteca personal del usuario, la cual no tiene límite de capacidad 
  7. El sistema muestra un mensaje de éxito confirmando la creación.
* **Escenarios Alternativos:**
  * 1a. El usuario decide no crear un comportamiento nuevo cancelando la creación, el sistema lo devuelve a la página anterior.
  * 5a. Datos incompletos: Si el campo "Nombre" o "Código" se encuentran vacíos, el sistema muestra un mensaje de error solicitando que se completen y detiene el proceso (quedándose en el punto 4).
  * 5b. Código malicioso/inválido: Si el sistema detecta en el paso 5 que el código intenta importar librerías prohibidas o tiene errores de sintaxis, se rechaza la creación, se muestra un mensaje de advertencia ("Código no permitido") y detiene el proceso (quedándose en el punto 4).

### CU-12: Crear Jugador

* **Actor:**
  * Usuario.
* **Precondición:**
  * El usuario debe tener un equipo creado.
* **Escenario Principal:**
  1. El Usuario selecciona la opción de "crear jugador".
  2. El sistema muestra los campos a completar: nombre, número de camiseta único, atributos (pacss) y comportamiento.
  3. El usuario ingresa nombre, número de camiseta, un valor en cada atributo del jugador en un rango permitido (20 a 100) y un comportamiento, después selecciona crear.
  4. El sistema valida que el nombre tenga caracteres permitidos, que los campos estén completos, que cada tributo esté en el rango de (20 a 100) y que la suma total de los 5 atributos sean exactamente 300 y que el jugador tenga asignado un comportamiento.
  5. El sistema acepta los datos y confirma la creación del jugador exitosamente.
* **Escenario Alternativo:**
  * 3a. El usuario decide cancelar la creación del jugador, en el ingreso de datos en los campos.
  * El sistema muestra un mensaje y requiere una confirmación de cancelación.
  * El usuario confirma la cancelación.
  * El sistema valida la confirmación y regresa al usuario al menú anterior de "crear jugador".
  * 3b. El usuario ingresa un valor indebido o deja incompleto/s alguno/s de los campos como nombre, número de camiseta o comportamiento.
  * El sistema muestra una advertencia de valor incorrecto o campo incompleto.
  * 3c. El usuario intenta crear un jugador con un número de camiseta repetido al de otro jugador, del mismo equipo.
  * El sistema muestra una advertencia, no se puede crear un jugador con ese número de camiseta, ya que el usuario tiene un jugador con ese número.

### CU-13: Intercambiar comportamiento de un jugador durante el partido

* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe estar jugando un partido.
* **Escenario Principal:**
  1. El usuario selecciona el comportamiento actual asignado al jugador durante el partido.
  2. El sistema muestra la lista de comportamientos.
  3. El usuario elige un comportamiento de la lista.
  4. El sistema registra el intercambio del comportamiento y se ejecutará en el inicio del siguiente tick.
* **Escenarios Alternativos:**
  * 2a. El sistema detecta que solo hay un comportamiento y no deja intercambiar ningún comportamiento a los jugadores
  * 2b. El sistema muestra un mensaje de advertencia, indicando que no hay comportamientos disponibles para intercambiar
  * 4a. El sistema no intercambia el comportamiento porque el usuario seleccionó el mismo comportamiento que está siendo usado

### CU-14: Cambiar Nombre Jugador

* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe tener jugadores creados
* **Escenario Principal:**
  1. El usuario accede al campo del nombre del jugador.
  2. El sistema le muestra el nombre del jugador
  3. El usuario escribe el nombre nuevo del jugador y confirma
  4. El sistema verifica que el nombre sea correcto y modifica el nombre del jugador por el nombre nuevo
* **Escenarios Alternativos:**
  * 3a. El sistema detecta que el jugador está jugando en un partido
  * 3a.1 El sistema envía un mensaje que no es posible modificar el nombre porque el jugador está jugando en un partido.
  * 3a.2 El sistema no modifica el atributo nombre y muestra las características del jugador.
  * 3a. El usuario se arrepiente y cancela el cambio.
  * 3a.1 El sistema no modifica el atributo nombre y muestra las características del jugador.
  * 3b. El sistema detecta que el nombre ingresado está vacío.
  * 3b.1 El sistema informa que el nombre no puede quedar vacío.
  * 3b.2 El sistema no modifica el atributo nombre y muestra las características del jugador.

### CU-15: Cambiar Numero de Jugador

* **Actor:**
  * Usuario
* **Precondición:**
  * El usuario debe tener jugadores creados
* **Escenario Principal:**
  1. El usuario accede al campo del número del jugador.
  2. El usuario elige modificar el número del jugador.
  3. El sistema habilita el campo del número del jugador para que el usuario ingrese un nuevo número.
  4. El usuario ingresa el nuevo número y confirma.
  5. El sistema verifica que el número ingresado no esté siendo utilizado por otro jugador del equipo.
  6. El sistema modifica el número del jugador.
  7. El sistema muestra el nuevo número en las propiedades del jugador.
  8. El sistema informa al usuario que el número fue cambiado exitosamente.
* **Escenarios Alternativos:**
  * 3a. El sistema detecta que el jugador está jugando en un partido/liga
  * 3a.1 El sistema envía un mensaje que no es posible modificar el nombre porque el jugador está jugando en un partido o liga.
  * 3a.2 El sistema no modifica el atributo número y muestra las características del jugador.
  * 5a. El usuario se arrepiente y cancela el cambio
  * 5a.1 El sistema no modifica el atributo número y muestra la página con las características del jugador.
  * 6a. El sistema detecta que el número ingresado ya está siendo utilizado por otro jugador del mismo equipo.
  * 6a.1. El sistema informa al usuario que el número ingresado ya está siendo utilizado.
  * 6a.2. El sistema vacía el campo del número para que el usuario pueda ingresar otro número.

### CU-16: Eliminar Comportamiento

* **Actor:**
  * Usuario
* **Precondición:**
  * Debe estar creado el comportamiento
* **Escenario Principal:**
  1. El usuario accede a la lista de comportamientos
  2. El usuario selecciona un comportamiento y elige eliminar comportamiento
  3. El sistema le pregunta al usuario si desea confirmar la operación
  4. El usuario confirma la operación
  5. El sistema elimina el comportamiento
* **Escenario Alternativo:**
  * 2a. El sistema detecta que el comportamiento está asignado a un jugador.
  * 2a.1 El sistema manda un mensaje: No se puede eliminar el comportamiento porque el comportamiento está asignado a un jugador.
  * 2a.2 El sistema no elimina el comportamiento y muestra nuevamente la lista de comportamientos. 
  * 4a. El usuario cancela la operación.
  * 4a.1. El sistema no elimina el comportamiento y muestra nuevamente la lista de comportamientos.

### CU-17: Eliminar Jugador

* **Actor:**
  * Usuario
* **Precondición:**
  * Ninguna
* **Escenario Principal:**
  1. El usuario accede a la lista de jugadores
  2. El usuario selecciona un jugador y elige eliminar jugador
  3. El sistema le pregunta al usuario si desea confirmar la operación
  4. El usuario confirma la operación
  5. El sistema elimina al jugador
* **Escenario Alternativo:**
  * 2a. El sistema detecta que el jugador pertenece a una alineación
  * 2a.1 El sistema manda un mensaje al usuario “Este jugador no se puede eliminar pues está en uso”
  * 2a.2 El sistema no elimina al jugador y muestra nuevamente la lista de jugadores
  * 4a. El usuario cancela la operación.
  * 4a.1. El sistema no elimina al jugador y muestra nuevamente la lista de jugadores.

### CU-18: Modificar nombre del Comportamiento

* **Actor:** 
  * Usuario
* **Precondición:** 
  * El comportamiento debe estar creado
* **Escenario Principal:**
  1. El usuario presiona el botón de gestionar comportamiento
  2. El sistema muestra los parámetros modificables del comportamiento 
  3. El usuario modifica el nombre del comportamiento
  4. El sistema valida que las modificaciones que hizo el usuario son correctas
  5. El usuario toca el botón guardar para guardar las modificaciones 
  6. El sistema registra el guardado 
  7. El sistema cierra la ventana 
* **Escenarios Alternativos:**
  * 1a El usuario está jugando un partido y toca el botón de gestionar comportamientos 
  * 1a2 El sistema muestra un mensaje en pantalla que dice “no se puede modificar los comportamientos mientras estas jugando un partido”
  * 3a El usuario no modifica nada
    * El usuario sale de la pestaña de gestionar comportamientos
  * 4a El sistema invalida las modificaciones que el usuario hizo
    * El sistema muestra un mensaje en pantalla de datos inválidos
  * 5a El usuario toca salir
    * El sistema muestra una ventana que le avisa al usuario si está seguro de salir sin guardar 
    * El Usuario presiona el botón si y sale de la pestaña de gestionar comportamiento
  * 5b El usuario toca salir
    * El sistema muestra una ventana que le avisa al usuario si está seguro de salir sin guardar 
    * El Usuario presiona el botón no y vuelve a la ventana de modificar comportamiento

### CU-19: Modificar la descripción de un comportamiento

* **Actor:** 
  * Usuario
* **Precondición:** 
  * El comportamiento debe estar creado 
* **Escenario Principal:**
  1. El usuario accede a la descripción del comportamiento
  2. El sistema muestra la descripción del comportamiento
  3. El usuario modifica la descripción y confirma el cambio 
  4. El sistema valida la modificación y cambia la descripción del comportamiento 
* **Escenario Alternativo:**
  * 3a. El usuario decide no modificar la descripción y sale al apartado anterior
  * 3b. El sistema no realiza ningún cambio y lleva al usuario al apartado anterior

### CU-20: Asignar un Comportamiento a un Jugador

* **Actor:** 
  * Usuario
* **Precondición:** 
  * El jugador debe estar creado 
  * Tiene que haber al menos 1 comportamiento creado
* **Escenario Principal:**
  1. El usuario presiona el botón comportamientos
  2. El sistema muestra la lista de comportamientos 
  3. El usuario selecciona un comportamiento
  4. El sistema muestra al comportamiento seleccionado con una marca
  5. El usuario selecciona el botón "asignar a"
  6. El sistema muestra la lista de jugadores 
  7. El usuario selecciona al jugador 
  8. El usuario toca el botón asignar
  9. El sistema le agrega el comportamiento al jugador
  10. El sistema muestra un mensaje "comportamiento asignado con éxito"
* **Escenarios Alternativos:**
  * 2a. El sistema muestra un mensaje en pantalla que dice “no se puede asignar un comportamiento mientras estas jugando un partido” y vuelve a la pantalla inicial
  * 9a. El jugador ya posee el comportamiento seleccionado.
  * El sistema muestra un mensaje informando que el jugador ya posee dicho comportamiento.
  * El sistema mantiene al usuario en la lista de jugadores

### CU-21: Elegir una alineación (Jugadores y Comportamientos)

* **Actor:** 
  * Usuario
* **Precondición:** 
  * El usuario debe tener el mínimo de jugadores y de comportamientos
* **Escenario Principal:**
  1. El usuario accede a la sección “elegir alineación”
  2. El sistema muestra la lista de jugadores y comportamientos a elegir
  3. El usuario selecciona los 3 titulares y los 3 suplentes con sus respectivos comportamientos asignados
  4. El usuario confirma la alineación
  5. El sistema verifica la alineación y lo guarda 
  6. El sistema muestra un mensaje de éxito
* **Escenarios Alternativos:**
  * 4a El usuario cancela elegir la alineación.
    * El sistema vuelve a la pestaña anterior sin cambios
  * 5a El sistema detecta alineación incompleta y no la valida.
    * El sistema vuelve a la pestaña de elegir una alineación 

### CU-22: Elegir una formación (Posiciones en la cancha)

* **Actor:** 
  * El usuario
* **Precondición:** 
  * El usuario debe haber elegido la alineación
* **Escenario Principal:**
  1. El usuario accede a la sección “seleccionar formación”
  2. El sistema muestra la pestaña de formaciones
  3. El usuario elige alguna de las formaciones disponibles
  4. El usuario confirma la selección
  5. El sistema muestra un mensaje de éxito
* **Escenarios Alternativos:**
  * 4a El usuario presiona el botón cancelar
    * El sistema vuelve a la pestaña anterior sin guardar ningún cambio

### CU-23: Modificar actividad del Comportamiento

* **Actor:** 
  * Usuario 
* **Precondición:** 
  * El comportamiento debe estar creado
* **Escenario Principal:**
  1. El usuario elige modificar la actividad del comportamiento.
  2. El sistema le muestra y habilita el campo de la actividad del comportamiento.
  3. El usuario modifica el apartado de actividad del comportamiento
  4. El sistema valida que las modificaciones del que hizo el usuario son correctas
  5. El usuario toca el botón guardar para guardar las modificaciones 
  6. El sistema registra el guardado.
  7. El sistema muestra las propiedades del comportamiento con la modificación.
* **Escenarios Alternativos:**
  * 3a El usuario decide cancelar la modificación.
    * El sistema muestra las propiedades del comportamiento sin cambios.
  * 4a El sistema invalida las modificaciones que el usuario hizo
    * El sistema muestra un mensaje en pantalla de datos inválidos
  * 5a El usuario toca salir
    * El sistema muestra una ventana que le avisa al usuario si está seguro de salir sin guardar 
    * El Usuario presiona el botón si y sale a la pestaña anterior 
    * El Usuario presiona el botón no y reanuda la modificación

### CU-24: Crear Liga 

* **Actor:**
    * Usuario
* **Precondición:**
    * El usuario debe tener el mínimo de jugadores y comportamientos creados.
    * Cada jugador debe tener un comportamiento asignado.
* **Escenario Principal:**
 1. El usuario ingresa al apartado de crear una liga
 2. El sistema le muestra las opciones para crear una liga
 3. El usuario elige un nombre para la liga 
 4. El usuario elige la duración de cada partido
 5. El usuario elige la cantidad máxima de equipos que van a participar en la liga
 6. El sistema muestra la opción de poner contraseña y el usuario decide si poner contraseña o no
 7. El usuario selecciona la alineación
 8. El usuario selecciona la formación
 9. El usuario termina de crear la liga
 10. El sistema muestra por pantalla que se creó la liga con éxito
 11. El sistema mueve al usuario automáticamente a la sala de espera
* **Escenario Alternativo:**
 * 1a. El usuario decide cancelar la creación de la liga
 * 1b. El sistema lleva al usuario al apartado anterior
 * 6a. El usuario decide no poner una contraseña a la liga y crea una liga pública que suma puntos en el ranking de ligas
 * 6b. El usuario decide poner una contraseña a la liga y crea una liga privada que no suma puntos en el ranking de ligas
 * 8a. El sistema muestra un mensaje de error al crear una liga
 * 8b. El sistema lleva al usuario al menú anterior sin terminar de crear la liga

### CU-25: Unirse a una Liga
* **Actor:**
    * Usuario
* **Precondición:**
    * El usuario debe tener el mínimo de jugadores y comportamientos creados.
    * La liga seleccionada no debe estar iniciada.
* **Escenario Principal:** 
 1. El usuario accede al apartado de unirse a una liga
 2. El sistema muestra la lista de ligas.
 3. El usuario selecciona una liga desde la lista de ligas.
 4. El sistema verifica si la liga seleccionada es pública o privada. Si es pública, omite el paso 5.
 5. El sistema solicita la contraseña de la liga y el usuario la ingresa.
 6. El sistema valida que aún haya cupo disponible y, si aplica, que la contraseña sea correcta.
 7. El usuario selecciona la alineación
 8. El usuario selecciona la formación
 9. El usuario confirma la inscripción a la liga
 10. El sistema inscribe al equipo del usuario en la liga.
 11. El sistema muestra un mensaje de confirmación de inscripción exitosa.
* **Escenarios alternativos:**
 * 5a. Contraseña incorrecta: Si la liga es privada y el usuario ingresa una contraseña incorrecta en el paso 5, el sistema muestra un mensaje de error y permite reintentar.
 * 6a. Liga sin cupo: Si entre el paso 1 y el 6 otro usuario ocupa el último lugar y la liga se queda sin cupos, el sistema cancela la inscripción y muestra un mensaje avisando que la liga está llena.

### CU-26: Cancelar una liga
* **Actor:**
    * Usuario
* **Precondición:**
    * El usuario debe ser el anfitrión de la liga.
    * La liga no debe estar iniciada.
* **Escenario Principal:**
1. El usuario se encuentra en la sala de espera de la liga que creó.
2. El usuario selecciona la opción de “Cancelar la Liga”.
3. El sistema solicita la confirmación para cancelar la liga.
4. El usuario confirma la cancelación de la liga.
5. El sistema disuelve la sala de espera y, de haber equipos en la sala, los redirige a la página anterior 
6. El sistema elimina la liga y muestra por pantalla un mensaje de éxito.
* **Escenarios Alternativos:**
 * 4a. El usuario decide no cancelar la liga.
 * 4a.1. El sistema cierra la pestaña de confirmación y el usuario permanece en la sala de espera.

### CU-27: Iniciar una liga
* **Actor:**
    * Usuario
* **Precondición:**
    * El usuario debe ser el anfitrión de la liga.
    * Debe haber un mínimo de 3 equipos unidos a la sala de espera.
    * La liga no debe estar iniciada.
* **Escenario Principal:**
1. El usuario accede a la sala de espera de la liga.
2. El usuario presiona el botón de “Iniciar Liga”.
3. El sistema genera el fixture completo de la liga y cambia su estado a “Iniciada”.
4. El sistema redirige al usuario a la pantalla de detalles de la liga durante un tiempo limitado y luego se inician los partidos automáticamente.
* **Escenarios Alternativos:**
 * 3a. El sistema detecta que la liga alcanzó su límite máximo de equipos. 
 * 3a.1. El sistema inicia automáticamente la liga y genera los detalles de la liga, sin necesidad de que el anfitrión presione el botón de inicio.

### CU-28: Ver Todos los detalles de la liga
* **Actor:**
    * Usuario
* **Precondición:**
    * Ninguna 
* **Escenario Principal:**
1. El usuario selecciona una liga desde el listado de ligas.
2. El usuario selecciona la opción “Ver detalles”
3. El sistema muestra la información completa de la liga, incluyendo el fixture de partidos (los ya jugados y los programados), y la tabla de la liga.
* **Escenarios Alternativos:**
 * 3a. La liga seleccionada aún no está iniciada
 * 3a.1. El sistema omite la tabla y el fixture (ya que aún no se han generado) y muestra únicamente la lista de equipos unidos actualmente.

### CU-29: Abandonar una liga 
* **Actor:**
    * Usuario 
* **Precondición:**
    * El usuario debe estar unido a la liga correspondiente.
    * La liga no debe estar iniciada.
    * El usuario no debe ser el anfitrión de la liga.
* **Escenario Principal:**
1. El usuario accede a la sala de espera de la liga a la que se unió.
2. El usuario selecciona la opción “Abandonar Liga”.
3. El sistema solicita confirmación para salir.
4. El usuario confirma su salida.
5. El sistema retira al equipo del usuario de la liga, liberando su cupo en la sala de espera.
El sistema redirige al usuario a la sección general de listado de ligas.
* **Escenarios Alternativos:**
 * 4a. El usuario decide no abandonar la liga y cancela la acción.
 * 4a.1. El sistema cierra el cuadro de confirmación y lo mantiene unido a la sala de espera.

### CU-30: Crear un Partido Amistoso 

### CU-31: Cancelar amistoso 

### CU-32: Unirse a un amistoso

### CU-33: Ver atributos de un jugador en el partido
* **Actor:** 
  * Usuario.
* **Precondición:** 
  * El usuario debe estar en el partido
* **Escenario Principal:**
  1. El usuario selecciona un jugador.
  2. El sistema muestra los atributos del jugador seleccionado.
* **Escenario Alternativo:**
  * 1a. El usuario selecciona otro jugador.
  * 1a.1 El sistema muestra los atributos del jugador seleccionado.
  * 1b El usuario selecciona de nuevo el jugador y se esconden los atributos del jugador.
  * 1b.1 El sistema deja de mostrar los atributos del jugador.

### CU-34: Ver comportamiento del jugador en partido
* **Actor:**
  * Usuario.
* **Precondición:**
  * El jugador debe ser propio del usuario.
  * El usuario debe estar en el partido.
* **Escenario Principal**
  1. El usuario accede a la sesión del partido.
  2. El sistema muestra en pantalla el comportamiento asignado al jugador.
* **Escenario Alternativo:**  

### CU-35: Ver el resultado final de un partido
* **Actor:** 
  * Usuario.
* **Precondición:** 
  * Partido finalizado.
* **Escenario Principal:**
  1. El sistema muestra por pantalla el resultado final del partido por un tiempo determinado y habilita un botón para cerrar la pestaña
  2. El usuario decide esperar a que se acabe el tiempo
  3. El sistema lo mueve automáticamente al apartado siguiente.
* **Escenarios Alternativos:**
  * 2a. El usuario cierra la pestaña del resultado final
  * 3a. El sistema mueve al usuario al apartado anterior

### CU-36: Intercambiar jugador en partido
* **Actor:** 
  * Usuario
* **Precondición:** 
  * Estar en el descanso del partido.
* **Escenario Principal:**
  1. El usuario accede a la pestaña de descanso.
  2. El sistema muestra la pantalla de descanso por un tiempo predeterminado
  3. El usuario selecciona el jugador titular propio a sustituir.
  4. El usuario selecciona el jugador suplente propio a sustituir
  5. El usuario confirma la sustitución
  6. El sistema verifica la sustitución e intercambia los jugadores
  7. El sistema, después del tiempo predeterminado, lleva al usuario al apartado siguiente
* **Escenarios Alternativos:**
  * 2a. El usuario selecciona salir del partido
  * 2b. El sistema lleva al usuario al apartado anterior
  * 3a y 4a. El usuario decide no hacer la sustitución 
  * 6a. El sistema no valida la sustitución y no intercambia los jugadores.

### CU-37: Salir del partido
* **Actor:** 
  * Usuario
* **Precondición:** 
  * Estar en un partido en transcurso.
* **Escenario Principal:**
  1. El usuario está jugando el partido.
  2. El usuario selecciona la opción para cerrar la interfaz del partido.
  3. El sistema lo redirecciona a la pestaña anterior, permitiendo navegar por otras secciones mientras el partido sigue su curso.
* **Escenarios Alternativos:**
  * El usuario decide no salir del partido 

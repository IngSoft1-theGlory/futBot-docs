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

### CU-7: Modificar Nombre del Equipo

### CU-8: Modificar logo del equipo

### CU-9: Mirar el ranking global de equipos

### CU-10: Ver el perfil de un equipo 

### CU-11: Crear Comportamientos

### CU-12: Crear Jugador 

### CU-13: Intercambiar comportamiento de un jugador durante el partido

### CU-14: Cambiar Nombre Jugador

### CU-15: Cambiar Numero de Jugador

### CU-16: Eliminar Comportamiento

### CU-17: Eliminar Jugador

### CU-18: Modificar nombre del Comportamiento 

### CU-19: Modificar la descripción de un comportamiento

### CU-20: Asignar un Comportamiento a un Jugador

### CU-21: Elegir una alineación (Jugadores y Comportamientos)

### CU-22: Elegir una formación (Posiciones en la cancha)

### CU-23: Modificar actividad del Comportamiento 

### CU-24: Crear Liga 

* **Actor:**
 * Usuario
* **Precondición:**
*  El usuario debe tener el mínimo de jugadores y comportamientos creados.
*  * Cada jugador debe tener un comportamiento asignado.
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

### CU-29: Salir de una liga 

### CU-30: Crear un Partido Amistoso {Alex}

### CU-31: Ver atributos de un jugador en el partido 

### CU-32: Cancelar amistoso 

### CU-33: Ver el resultado final de un partido

### CU-34: Unirse a un amistoso

### CU-35: Intercambiar jugador en partido

### CU-36: Salir del partido

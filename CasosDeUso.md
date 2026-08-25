# Especificación de Casos de Uso

## 1. Listado de Casos de Uso
* **CU-01:** Crear un usuario
* **CU-02:** Crear comportamientos 
* **CU-03:** Crear jugadores 
* **CU-04:** Crear Liga 
* **CU-05:** Unirse a una Liga 
* **CU-06:** Crear un amistoso 

---

## 2. Desarrollo de Casos de Uso 

### CU-01: Crear un usuario
* **Actor:**  
  *  Usuario
* **Precondicion:** 
  *  El email no debe estar en uso por otro usuario
  *  El mail debe existir (Consultar)
  *  El usuario debe tener un mail (Consultar)
* **Escenario Principal:**
  1.  El usuario accede al sistema
  2.  El sistema le muestra las opciones de registrar usuario o ingresar
  3.  El usuario elige registrarse
  4.  El sistema le muestra las casillas a rellenar por el usuario
  5.  El usuario registra un username
  6.  El usuario registra un nombre unico para su club 
  7.  El usuario registra un mail unico
  8.  El usuario registra una contraseña asociada a la cuenta
  9.  El usuario coloca una imagen para identificar el club
  10.  El usuario termina de registrarse
  11.  El sistema muestra por pantalla que el usuario se registro correctamente
* **Escenarios Alternativos:**
  *  1a. El usuario decide no crear la cuenta seleccionando el boton de cancelar
  *  1b. El sistema muestra por mensaje que el usuario no se pudo crear 
* **Escenarios Excepcionales:**
  *  6a. El usuario ingresa un nombre del club ya existente
  *  6b. El sistema muestra por mensaje que el nombre del club ya existe, ingrese otro nombre 
  *  7a. El usuario ingresa un mail erroneo o que no existe 
  *  7b. El sistema muestra por mensaje que ingreso un mail incorrecto
  *  9a. El usuario ingresa un avatar invalido
  *  9a. El sistema muestra por mensaje que el avatar es invalido y que ingrese otro avatar
  *  11a. El sistema muestra por mensaje que no se pudo crear el usuario por un error del sistema
* **Postcondiciones:** 
  *  El sistema logro registrar la cuenta nueva

### CU-02: Crear comportamientos
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * El usuario debe estar logueado.
* **Escenario Principal:**
  1. El usuario accede a la sección de creación de comportamientos de la aplicación.
  2. El usuario ingresa el nombre descriptivo para el comportamiento.
  3. El usuario escribe o adjunta el código fuente en lenguaje Python que definirá las acciones del jugador para la duración de un tick.
  4. El usuario hace clic en el botón para guardar o crear el comportamiento.
  5. El sistema valida que los campos requeridos (Nombre y Código) no estén vacíos *y que el código fuente cumpla con las restricciones de seguridad?*.
  6. El sistema almacena el nuevo comportamiento en la biblioteca personal del usuario, la cual no tiene límite de capacidad (N comportamientos posibles).
  7. El sistema muestra un mensaje de éxito confirmando la creación.
* **Escenarios Alternativos:**
  * 1a. El usuario decide no crear un comportamiento nuevo cancelando la creación, el sistema lo devuelve al menú.
* **Escenarios Excepcionales:**
  * 5a. Datos incompletos: Si el campo "Nombre" o "Código" se encuentran vacíos, el sistema muestra un mensaje de error solicitando que se completen y detiene el proceso (quedandose en el punto 4).
  * 5b. [FUTURA REVISION: SOLO EN CASO DE VALIDAR CODIGO EN ESTE PUNTO] Código malicioso/inválido: Si el sistema detecta en el paso 5 que el código intenta importar librerías prohibidas o tiene errores de sintaxis, se rechaza la creación, se muestra un mensaje de advertencia ("Código no permitido") y detiene el proceso (quedandose en el punto 4).
* **Postcondiciones:** 
  * El comportamiento queda creado con antelación y disponible en la cuenta del usuario para ser asignado a sus jugadores (titulares o suplentes) de cara a futuros partidos.


### CU-03: Crear jugador
* **Actor:**
  * Usuario.
* **Precondicion:** 
  * Usuario logeado.
* **Escenario Principal:**
  1. El Usuario selecciona la opcion de "crear jugador".  
  2. El sistema muestra los campos a completar nombre, numero de camiseta y atributos (pacss). 
  3. El usuario ingresa nombre, numero de camiseta y un valor en cada atributo del jugador en un rango permitido (20 a 100), despues selecciona crear.
  4. El sistema valida que los campos esten completos, que cada tributo este en el rango de (20 a 100) y que la suma total de los 5 atributos no supere el valor de 300.
  4. El sistema acepta los datos y confirma la creacion del jugador exitosamente.
  * [opcional]. El sistema muestra por pantalla los ultimos jugadores creados con sus datos.
* **Escenario Alternativo:**
  * 3a. El usuario decide cancelar la creacion del jugador, en el ingreso de datos en los campos.
    * El sistema muestra un mensaje y requiere una confirmacion de cancelacion.
    * El usuario confirma la cancelacion.
    * El sistema valida la confirmacion y regresa al usuario al menu anterior de "crear jugador". 
* **Escenarios Excepcional:**
  + 3b. El usuario ingresa un valor indebido o deja incompleto/s alguno/s de los campos como nombre o numero de camiseta.
    * El sistemas muestra una advertecia de valor incorrecto o campo imcompleto.
  * 3b. El usuario intenta crear un jugador con un numero de camiseta repetido a otro jugador, del mismo usuario.
    * El sistema muestra una abvertencia, no se puede crear un jugador con ese numero de camiseta, ya que el usuario tiene un jugar con ese numero.
  * [opcional] 4b. El sistema presenta una error de conexion o ejecucion.
    * El sistema muestra un mensaje de "error de conexion" o "error inesperado", se puede solicitar "reintentar".
* **Postcondiciones:** 
  * Jugador creado exitosamente en el sistema.

### CU-04: Crear Liga
* **Actor:** 
  *  Usuario
* **Precondicion:**
  *  El usuario debe estar logueado en el sistema 
  *  El usuario debe tener al menos 6 jugadores, con sus comportamientos previamente asignados, creados en su club
  *  El usuario debe tener creado un comportamiento como minimo 
* **Escenario Principal:**
  1.  El usuario ingresa al apartado de crear una liga
  2.  El sistema le muestra las opciones para crear una liga
  3.  EL usuario elige su alineacion
  4.  El usuario elige un nombre para la liga que no este en uso
  5.  El usuario elige la duracion de cada partido
  6.  El usuario elige la cantidad maxima de equipos
  7.  El usuario decide si pone una contraseña a la liga o no
  8.  El usuario termina de crear la liga
  9.  El sistema muestra por pantalla que se creo la liga con éxito
* **Escenario Alternativo:**
  *  1a. El usuario decide cancelar la creacion de la liga
  *  7a. El usuario decide no poner una contraseña a la liga, por lo que crea una liga publica
  *  7b. El usuario decide poner una contraseña a la liga, por lo que crea una liga privada
* **Escenario Excepcional:**
  *  3a. El sistema muestra por mensaje que la alineacion no es correcta
  *  3b. El usuario elige otra alineacion
  *  4a. El sistema muestra por pantalla que el nombre de la liga ya esta en uso
  *  4b. El usuario escribe otro nombre para la liga
  *  9a. El sistema muestra un mensaje de error al crear una liga
  *  9b. El sistema lleva al usuario al menu anterior sin terminar de crear la liga
* **Postcondiciones:** 
  *  El sistema logro registrar la liga creada

### CU-05: Unirse a una Liga
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * El usuario debe estar logueado en el sistema.
  * El usuario debe tener al menos 6 jugadores, con sus comportamientos previamente asignados, creados en su club para poder participar.
  * La liga seleccionada no debe haber superado su cantidad de equipos máxima.
* **Escenario Principal:**
  1. El usuario selecciona una liga desde la lista de ligas disponibles.
  2. El sistema verifica si la liga es pública o privada (si tiene contraseña opcional). Si es pública, omite el paso 3.
  3. El sistema solicita la contraseña de la liga. El usuario la ingresa.
  4. El sistema despliega la plantilla del equipo del usuario para que elija los jugadores.
  5. El usuario selecciona exactamente 6 jugadores de su club, designando quiénes serán los 3 titulares y los 3 suplentes para esa liga.
  6. El usuario confirma la inscripción a la liga.
  7. El sistema valida que la contraseña (si aplica) sea correcta y que aún haya cupo disponible.
  8. El sistema inscribe al equipo del usuario en la liga.
  9. El sistema muestra un mensaje de confirmación de inscripción exitosa.
* **Escenarios Excepcionales:**
  * 3a. Contraseña incorrecta: Si la liga es privada y el usuario ingresa una contraseña inválida en el paso 3, el sistema muestra un mensaje de error y detiene la inscripción (permite reintentar).
  * 5a. Selección de jugadores inválida: Si el usuario selecciona más o menos de 6 jugadores, o no define correctamente titulares y suplentes, el sistema deshabilita el botón de confirmación hasta que la selección sea válida.
  * 7a. Liga sin cupo: Si entre el paso 1 y el 7 otro usuario ocupó el último lugar y la liga se quedó sin cupos, el sistema cancela la inscripción y muestra un mensaje avisando que la liga está llena.
* **Postcondiciones:** 
  * El equipo del usuario queda inscripto en la liga y participará del fixture generado por el sistema.

### CU-06: Crear un amistoso
* **Actor:** 
  * Usuario.
* **Precondicion:** 
  * Usuario logeado. 
* **Escenario Principal:**
  1. El usuario selecciona la opcion de "Jugar nuevo amistoso".
  2. El sistema muestra ligas y clubes disponibles.
  3. El usuario selecciona un club rival para jugarar.
  4. El sistema prepara e inicia el partido.
  5. El sistema al terminar el partido, registra el resultado y guarda los goles de cada club.
  6. El sistema muestra un resumen del partido, cantidad de goles, etc.
  7. El sistema muestra 3 opciones "Volver a jugar" o "Jugar nuevo amistoso" o "Volver al menu inicial".
  8. El usuario selecciona una opcion.
* **Escenario Alternativo:**
  * 3a. El usuario cancela el amistoso y seleciona "Cancelar".
    * El sistema valida la cancelacion y regresa al usuario al menu anterior donde puede seleccionar 3 opciones accion.
    * El usuario selecciona una opcion.
* **Escenarios Excepcional:**
  * 3b. El sistema no tiene ligas o clubs disponibles.
    * El sistema sistema muestra un aviso "Actualmente no hay liga/s disponible/s".
    * El sistema sistema muestra un aviso "Actualmente no hay club/es disponible/s".
    * El sistema sistema muestra un aviso "Actualmente no hay liga/s y club/es disponible/s".
    * El usuario "acepta" el mensaje y tiene 3 opciones (donde "Volver a jugar" y "Jugar nuevo amistoso" no estan disponibles).

  * [opcional] 4b. El sistema presenta un error de conexion o ejecucion en la preparacion del amistoso.
    * El sistema muestra un advertencia de "error de conexion" o "error inesperado".
    * El sistema regresa al usuario al menu de crear amistoso y tiene 3 opciones de accion.
    * El usuario selecciona una opcion.
* **Postcondiciones:** 
  * Se registra partido amistoso en un historial.
  * Se actualizan la cantidad de goles de cada club.




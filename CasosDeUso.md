# Especificación de Casos de Uso

## 1. Listado de Casos de Uso
* **CU-01:** Crear un usuario {Ema}
* **CU-02:** Crear comportamientos {Fer}
* **CU-03:** Crear jugadores {Alex} 
* **CU-04:** Crear Liga {Ema}
* **CU-05:** Crear Unirse a una Liga {Fer}
* **CU-06:** Crear un amistoso {Alex}

---

## 2. Desarrollo de Casos de Uso 

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


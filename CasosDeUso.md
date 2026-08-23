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

### CU-01: Crear un usuario
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * El email no debe estar en uso
* **Escenario Principal:**
  *  
* **Escenarios Alternativos:**
  * 
* **Postcondiciones:** 

### CU-02: Unirse a Liga


### CU-03: Crear jugador
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * Iniciar sesion
* **Escenario Principal:**
  * El Usuario presiona la opcion de crear jugador  
  * El sistema muestra los campos de nombre, numero de camiseta y atributos (pacss) a completar 
  * El usuario ingresa el nombre, numero de camiseta, los atributos del jugador y selecciona confirmar creacion de jugador
  * El sistema confirma la creacion del jugador exitosamente
  * [El usuario selecciona ver jugadores creados o disponibles] 
  * [El sistema muestra por pantalla el jugador creado con sus campos correspondientes]
* **Escenario Principal Alternativo:**
  * El usuario decide cancelar la cracion del jugador, en el paso de completar campos de nombre, numero de camiseta y atributos
  * El sistema solicita confirmar la cancelacion
  * El usuario confirma la cancelacion
  * El sistema descarta o elimina los datos en los campos y no se guarda jugador 
* **Escenarios Excepcional:**
  * El usuario ingresa un valor indebido o deja incompletos en algunos de los campos como nombre o numero de camiseta
  * El sistemas muestra por pantalla una advertecia de valor incorrecto o campo imcompleto
  * El usuario ingresa en un atributo un valor fuera del rango 20-100
  * El sistema muestra un aviso, valor no permitido, fuera del rango 20-100
  * El usuario ingresa 5 atributos, con una suma total mayor a 300 fuera del maximo total permitido
  * El sistema muestra un aviso, que los valores ingresados en en cada atributo suupera el maximo permitido, debe ser modificado y su suma toal dene ser a lo sumo 300
  * [El Usuario crea un jugador con numero de camiseta  y/o entero atributos exactamente igual a otro jugador creado previamente por el mismo usuario]
  * [El sistema muestra una abvertencia donde no se puede crear 2 jugadores con los mismos numeros de camiseta y entero atributos]
  * El usuario confirma la creacion de usuario pero se pierde la conexion o falla la llamada 
  * El sistema muestra por pantalla, que hubo un error de conexion y no se crea jugador
* **Postcondiciones:** 
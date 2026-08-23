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
  *  Usuario
* **Precondicion:** 
  *  El email no debe estar en uso
  *  El mail debe existir
  *  El usuario debe tener un mail
* **Escenario Principal:**
  *  El usuario accede al sistema
  *  El sistema le muestra las opciones de registrar usuario o ingresar
  *  El usuario ingresa a la opcion de registrar usuario
  *  El sistema le muestra las casillas a rellenar por el usuario
  *  El usuario registra un nombre unico para su club 
  *  El usuario registra un mail unico
  *  El usuario registra una contraseña asociada a la cuenta
  *  El usuario coloca una imagen para identificar el club
  *  El sistema muestra por pantalla que el usuario se registro correctamente
* **Escenarios Alternativos:**
  *  El usuario no puede registrar su mail
  *  El usuario no puede registrar el nombre del club
  *  El usuario no puede colocar un avatar para su club
  *  El sistema muestra el mensaje de que no puede registrar el mail del usuario
  *  El sistema muestra el mensaje de que no puede registrar el nombre del club del usuario
  *  El sistema muestra el mensaje de que no puede colocar el avatar del usuario  
* **Postcondiciones:** 
  *  El sistema logro registrar la nueva cuenta 
  
### CU-04: Crear Liga
* **Actor:** 
  *  Usuario
* **Precondicion:**
  *  El usuario debe tener un club
  *  El usuario debe tener 6 jugadores creados como minimo (Consultar)
  *  El usuario debe tener creado un comportamiento como minimo (Consultar)
* **Escenario Principal:**
  *  El usuario ingresa al apartado de ligas
  *  El sistema le muestra las opciones de crear liga o unirse a una liga
  *  El usuario ingresa al apartado de crear una liga
  *  El sistema le muestra las opciones para crear una liga
  *  EL usuario elije su alineacion
  *  El usuario elige un nombre para la liga
  *  El usuario elije la duracion de cada partido de la liga
  *  El usuario decide si pone una contraseña para la liga (opcional)
  *  El usuario termina de crear la liga
  *  El sistema muestra por pantalla que se creo la liga con exito
* **Escenario Alternativo:**
  *  El usuario decide cancelar la creacion de una liga
  *  El sistema muestra por pantalla que el nombre de la liga ya esta en uso
  *  El sistema lleva al usuario al menu principal
  *  El sistema lleva al usuario al apartado de ligas
  *  El usuario entra al apartado de unirse a una liga
* **Postcondiciones:** 
  *  El sistema logro registrar la liga creada

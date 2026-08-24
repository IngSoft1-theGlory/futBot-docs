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
  *  7a. El usuario decide no poner una contraseña a la liga, por lo que crea una liga privada
  *  7b. El usuario decide poner una contraseña a la liga, por lo que crea una liga publica
* **Escenario Excepcional:**
  *  3a. El sistema muestra por mensaje que la alineacion no es correcta
  *  3b. El usuario elige otra alineacion
  *  4a. El sistema muestra por pantalla que el nombre de la liga ya esta en uso
  *  4b. El usuario escribe otro nombre para la liga
  *  9a. El sistema muestra un mensaje de error al crear una liga
  *  9b. El sistema lleva al usuario al menu anterior sin terminar de crear la liga
* **Postcondiciones:** 
  *  El sistema logro registrar la liga creada

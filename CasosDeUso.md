# Especificación de Casos de Uso

## 1. Listado de Casos de Uso
* **CU-01:** Crear un usuario {Ema}
* **CU-02:** Crear comportamientos {Fer}
* **CU-03:** Crear jugadores {Alex} 
* **CU-04:** Crear Liga {Ema}
* **CU-05:** Unirse a una Liga {Fer}
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
  * 5a. Datos incompletos: Si el campo "Nombre" o "Código" se encuentran vacíos, el sistema muestra un mensaje de error solicitando que se completen y detiene el proceso (quedandose en el punto 4).
  * 5b. [FUTURA REVISION: SOLO EN CASO DE VALIDAR CODIGO EN ESTE PUNTO] Código malicioso/inválido: Si el sistema detecta en el paso 5 que el código intenta importar librerías prohibidas o tiene errores de sintaxis, se rechaza la creación, se muestra un mensaje de advertencia ("Código no permitido") y detiene el proceso (quedandose en el punto 4).
* **Postcondiciones:** 
  * El comportamiento queda creado con antelación y disponible en la cuenta del usuario para ser asignado a sus jugadores (titulares o suplentes) de cara a futuros partidos.


### CU-05: Unirse a una Liga {Fer}
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * El usuario debe estar logueado en el sistema.
  * ¿? El usuario debe tener un equipo creado ¿?.
  * El usuario debe tener al menos 6 jugadores creados en su club para poder participar.
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
* **Escenarios Alternativos:**
  * 3a. Contraseña incorrecta: Si la liga es privada y el usuario ingresa una contraseña inválida en el paso 3, el sistema muestra un mensaje de error y detiene la inscripción (permite reintentar).
  * 5a. Selección de jugadores inválida: Si el usuario selecciona más o menos de 6 jugadores, o no define correctamente titulares y suplentes, el sistema deshabilita el botón de confirmación hasta que la selección sea válida.
  * 7a. Liga sin cupo: Si entre el paso 1 y el 7 otro usuario ocupó el último lugar y la liga se quedó sin cupos, el sistema cancela la inscripción y muestra un mensaje avisando que la liga está llena.
* **Postcondiciones:** 
  * El equipo del usuario queda inscripto en la liga y participará del fixture generado por el sistema.
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

### CU-06: Crear un amistoso
* **Actor:** 
  * Usuario
* **Precondicion:** 
  * Inicar sesion previamente 
  * Pertencer a un club
* **Escenario Principal:**
  * El usuario selecciona "Jugar nuevo amistoso"
  * El sistema muestra ligas y clubes disponibles
  * El usuario selecciona una liga/club contra el que jugara
  * El sistema prepara e inicia el partido
  * [Se juega el partido - Se finaliza el partido]
  * El sistema registra el resultado del partido y guarda los goles de cada club
  * El sistema muestra un resumen del partido, cantidad de goles, etc
  * El sistema muestra 2 alternativas "Volver a jugar" o "jugar nuevo amistoso" 
  * El usuario ve el resumen de partidos y tiene la opcion de elegir entre "Volver a jugar" o "Jugar nuevo amistoso" 
* **Escenario Principal:**
  * El usuario selecciona "Jugar nuevo amistoso"
  * El sistema muestra ligas y clubes disponibles
  * El usuario decide no jugar un nuevo amistoso y seleciona cancelar
  * El sistema vuelve a la pantalla anterior donde muestra "Volver a jugar" o "Jugar nuevo amistoso"
* **Escenarios Excepcional:**
  * El sistema no tiene ligas o clubs disponibles por el momento
  * El sistema sistema muestra un aviso "Actualmente no hay ligas disponibles"
  * El sistema sistema muestra un aviso "Actualmente no hay clubes disponibles"
  * El sistema sistema muestra un aviso "Actualmente no hay ligas y clubes disponibles"
  * El usuario confirma el mensaje y vuelve al menu principal (pantalla principal) (opcional)

  * [El sistema presenta problemas o errores de conexion durante preparacion del amistoso]
  * [El sistema muestra un aviso de "se perdio la conexion, intentelo de nuevo"] 
  * [El sistema devuelve al usuario al menu principal]
  * [El sistema presenta problemas o errores de ejecucion durante preparacion del amistoso]
  * [El sistema muestra un aviso de "No se puede inciar partida, intentelo de nuevo"] 
  * [El sistema devuelve al usuario al menu principal]
* **Postcondiciones:** 
[El partido amistoso queda registrado en el histoial] (opcional)
[Se actualizan las cantidad de goles del usario o club] (opcinal)
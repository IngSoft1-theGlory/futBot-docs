# CONTRATO API


**USUARIO:**
+ post crear usuario - Cristian
+ post iniciar sesión y autenticar - Cristian
+ post cerrar sesión - Cristian
+ put cambiar nombre de usuario - Cristian
+ delete eliminar usuario - Cristian


**EQUIPO y RANKING:**
+ post crear equipo - Cristian
+ put modificar nombre de equipo - Cristian
+ put modificar logo de equipo - Cristian
+ get ver perfil de un equipo - Orlando
+ get obtener alineación y formación de un equipo - Noah
+ get ver ranking global - Orlando


**JUGADOR:**
+ post crear jugador - Orlando
+ delete eliminar jugador - Orlando
+ put cambiar nombre de jugador - Noah
+ put cambiar número de jugador - Noah
+ post asignar un comportamiento a un jugador (antes de un partido) - Noah
+ get obtener lista de jugadores - Cristian


**COMPORTAMIENTO:**
+ post crear comportamientos - Noah
+ delete eliminar comportamiento - Alex
+ get listar comportamientos del usuario - Alex
+ put modificar nombre de comportamiento - Orlando
+ put modificar descripción - Alex
+ put modificar actividad del comportamiento - Orlando


**LIGAS:**
+ post crear liga - Alex
+ delete cancelar liga - Alex
+ delete abandonar liga - Alex
+ get ver todos los detalles de la liga (ranking de liga, fixture de liga) - Alex
+ get listar ligas - Ema
+ post unirse a una liga - Ema
+ post iniciar la liga - Cris


**PARTIDO:**
+ post crear partido amistoso - Ema
+ get listar partidos amistosos - Noah
+ delete eliminar partido - Fer
+ get ver resultado final de un partido - Fer
+ post unirse a un amistoso - Fer


**DENTRO DEL PARTIDO:**
+ put intercambiar jugadores en partido (requiere estar en una pausa) - Alex
+ put intercambiar comportamiento de un jugador en partido - Noah
+ get ver detalles dentro del partido - Fer


**WEBSOCKETS:**
+ post solicitar autorización mediante websockets - Cris
+ WS canal unidireccional en tiempo real para eventos del sistema - Cris
+ post solicitar autorización para conectarse al partido - Cris 
+ WS canal bidireccional en tiempo real para la simulación tick a tick - Cris


---


<!-- USUARIO -->


### 1. `POST /api/v1/auth/register`


Registra un nuevo usuario en la plataforma.


### Request Body:
```json
{
   "username": "Maradona10",
   "email": "diego@famaf.unc.edu.ar",
   "password": "Password123!"
}
```


### Responses:


#### 201 Created
```json
{
 "user_id": 1,
 "username": "Maradona10",
 "email": "diego@famaf.unc.edu.ar"
}
```


#### 400 Bad Request (Datos de entrada inválidos o duplicados)


* Email ya pertenece a otro usuario*:
```json
{
   "error": "DUPLICATE_EMAIL",
   "message": "El email ingresado se encuentra en uso. Intentar de nuevo"
}
```


---


### 2. `POST /api/v1/auth/login`


Autentica las credenciales de un usuario y genera una sesión (token de acceso o cookie).


### Request Body:
```json
{
  "email": "diego@famaf.unc.edu.ar",
  "password": "Password123!"
}
```


### Responses:


#### 200 OK (Inicio de sesión exitoso)
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "user": {
    "user_id": 1,
    "username": "Maradona10",
    "email": "diego@famaf.unc.edu.ar"
  }
}
```


#### 400 Bad Request (Formato de datos inválido)
```json
{
  "error": "INVALID_FORMAT",
  "message": "El email ingresado tiene un formato inválido"
}
```


#### 401 Unauthorized (Credenciales incorrectas)
```json
{
  "error": "INVALID_CREDENTIALS",
  "message": "Email o contraseña incorrecto"
}
```


---


### 3. `POST /api/v1/auth/logout`


Cierra la sesión del usuario actual e invalida su token de sesión.


**Header:**
* `Authorization`: `Bearer <token>` *(requerido)*


### Request Body:
(void)


### Responses:


#### 200 OK (Cierre de sesión exitoso)
```json
{
  "message": "Sesión cerrada exitosamente"
}
```


#### 401 Unauthorized (Sin sesión activa o token vencido)
```json
{
  "error": "UNAUTHORIZED",
  "message": "El token de sesión no es válido o ha expirado"
}
```


---


### 4. `PUT /api/v1/users/{userId}`


**Path Parameters:**
* userId (integer, requerido): ID único del usuario a modificar.


Cambia el nombre del usuario especificado.


### Request Body:
```json
{
   "username": "Pepe"
}
```


### Responses:


#### 200 OK
```json
{
   "user_id": 1,
   "username": "Pepe",
   "email": "pepe@famaf.unc.edu.ar"
}
```


#### 400 Bad Request


* Campo username vacío*:
```json
{
   "error": "VOID_USERNAME",
   "message": "El campo nombre de usuario no puede ser vacío"
}
```


#### 404 Not Found
```json
{
   "error": "USER_NOT_FOUND",
   "message": "El usuario solicitado no existe."
}
```


---


### 5. `DELETE /api/v1/users/{userId}`


**Path Parameters:**
* userId (integer, requerido): ID único del usuario a eliminar.


Elimina el usuario especificado, junto con su equipo y datos/estadísticas asociadas.


### Request Body:
(void)


### Responses:


#### 204 No Content
(void)


#### 404 Not Found
```json
{
   "error": "USER_NOT_FOUND",
   "message": "El usuario solicitado no existe."
}
```


#### 409 Conflict
```json
{
   "error": "USER_IN_ACTIVE_MATCH",
   "message": "No se puede eliminar el usuario porque actualmente pertenece a una liga o partido en curso."
}
```


<!-- EQUIPO -->


### 1. `POST /api/v1/teams/`


Registra un equipo en la plataforma.


### Request Body:
```json
{
   "team_name": "alfajoresFC",
   "team_logo": "https://example.com/logos/alfajores.png"
}
```


### Responses:


#### 201 Created
```json
{
   "team_id": 1,
   "team_name": "alfajoresFC",
   "team_logo": "https://example.com/logos/alfajores.png"
}
```


#### 400 Bad Request


*Caso 1: Nombre de equipo vacío*:
```json
{
   "error": "VOID_TEAM_NAME",
   "message": "El nombre del equipo no puede quedar vacío."
}
```


*Caso 2: Logo no proporcionado / campo de logo vacío*:
```json
{
   "error": "VOID_TEAM_LOGO",
   "message": "Debes proporcionar un logo para el equipo."
}
```


*Caso 3: Formato o URL de logo inválido*:
```json
{
   "error": "INVALID_LOGO_FORMAT",
   "message": "El logo ingresado debe ser una URL válida o tener un formato de imagen permitido (.png, .jpg, .jpeg, .svg)."
}
```


#### 409 Conflict (Usuario ya posee un equipo)
```json
{
   "error": "USER_ALREADY_HAS_TEAM",
   "message": "El usuario ya tiene un equipo registrado. Solo se permite un equipo por cuenta."
}
```


---


### 2. `PUT /api/v1/teams/{teamId}`


**Path Parameters:**
* teamId (integer, requerido): ID único del equipo a modificar el nombre.


Cambia el nombre del equipo especificado.


### Request Body:
```json
{
   "team_name": "sacachispasFC"
}
```


### Responses:


#### 200 OK
```json
{
   "team_id": 1,
   "team_name": "sacachispasFC",
   "team_logo": "https://example.com/logos/alfajores.png"
}
```


#### 400 Bad Request (Nombre de equipo vacío o inválido)
```json
{
   "error": "VOID_TEAM_NAME",
   "message": "El nombre del equipo no puede quedar vacío o contener sólo espacios."
}
```


#### 404 Not Found
```json
{
   "error": "TEAM_NOT_FOUND",
   "message": "El equipo solicitado no existe."
}
```


#### 409 Conflict
```json
{
   "error": "CURRENT_MATCHING",
   "message": "No se puede modificar el nombre del equipo mientras se encuentra jugando un partido o participando en una liga activa."
}
```


---


### 3. `PUT /api/v1/teams/{teamId}`


**Path Parameters:**
* teamId (integer, requerido): ID único del equipo a modificar el logo.


Cambia el logo del equipo especificado.


### Request Body:
```json
{
   "team_logo": "https://example.com/logos/sacachispas.png"
}
```


### Responses:


#### 200 OK
```json
{
   "team_id": 1,
   "team_name": "sacachispasFC",
   "team_logo": "https://example.com/logos/sacachispas.png"
}
```


#### 400 Bad Request (Validación de campos vacíos o formato inválido)


*Caso 1: Logo no proporcionado / campo de logo vacío*:
```json
{
   "error": "VOID_TEAM_LOGO",
   "message": "Debes proporcionar un logo para el equipo."
}
```


*Caso 2: Formato o URL de logo inválido*:
```json
{
   "error": "INVALID_LOGO_FORMAT",
   "message": "El logo ingresado debe ser una URL válida o tener un formato de imagen permitido (.png, .jpg, .jpeg, .svg)."
}
```


#### 404 Not Found
```json
{
   "error": "TEAM_NOT_FOUND",
   "message": "El equipo solicitado no existe."
}
```


#### 409 Conflict
```json
{
   "error": "CURRENT_MATCHING",
   "message": "No se puede modificar el logo del equipo mientras se encuentra jugando un partido o participando en una liga activa."
}
```


---


### 4. `GET /api/v1/teams/{teamId}`


**Path Parameters:**
* teamId (integer, requerido): ID único del equipo a consultar.


Muestra las estadísticas de un equipo.


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "teams": [
    {
      "team_id": 1,
      "name": "Instituto Atlético Central Córdoba",
      "position": 1,
      "matches_played": 800000,
      "wins": 800000,
      "draws": 0,
      "losses": 0,
      "points": 2400000
    }
  ]
}
```


#### 401 Unauthorized (Usuario no autenticado)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para consultar el equipo."
}
```


#### 404 Not Found
```json
{
   "error": "TEAM_NOT_FOUND",
   "message": "No hay equipo con ese id"
}
```


---


### 5. `GET /api/v1/teams/{teamId}/lineup`


**Path Parameters:**
* teamId (integer, requerido): ID único del equipo a consultar.


Obtiene la alineación actual (titulares y suplentes con sus comportamientos asignados) y la formación táctica guardada del equipo.


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "team_id": 1,
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


#### 404 Not Found (Equipo o alineación no encontrada)
```json
{
  "error": "LINEUP_NOT_SET",
  "message": "El equipo especificado no tiene una alineación guardada o no existe."
}
```


<!-- JUGADOR -->


### 1. `POST /api/v1/players`


Crea un nuevo jugador y lo asigna al equipo del usuario autenticado.


### Request Body:
```json
{
  "name": "Lionel Messi",
  "number": 10,
  "power": 50,
  "agility": 50,
  "control": 50,
  "speed": 50,
  "strength": 100
}
```


### Responses:


#### 201 Created
```json
{
  "player_id": 1,
  "name": "Lionel Messi",
  "number": 10,
  "power": 50,
  "agility": 50,
  "control": 50,
  "speed": 50,
  "strength": 100
}
```


#### 400 Bad Request (Errores de validación)


*Caso 1: Suma PACSS distinta de 300 o valores fuera de rango (20 - 100)*:
```json
{
  "error": "INVALID_PACSS_TOTAL",
  "message": "La suma de los atributos PACSS debe ser exactamente 300 puntos y cada uno debe estar entre 20 y 100."
}
```


*Caso 2: Número de camiseta duplicado en el equipo*:
```json
{
  "error": "DUPLICATE_JERSEY_NUMBER",
  "message": "El número de camiseta ingresado ya pertenece a otro jugador de tu equipo."
}
```


*Caso 3: Nombre o datos incompletos*:
```json
{
  "error": "INVALID_PLAYER_DATA",
  "message": "El nombre del jugador no puede quedar vacío y todos los campos son obligatorios."
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para crear jugadores."
}
```


#### 404 Not Found (Usuario no tiene equipo creado)
```json
{
  "error": "TEAM_NOT_FOUND",
  "message": "Debes crear un equipo antes de poder registrar jugadores."
}
```


---


### 2. `DELETE /api/v1/players/{player_id}`


**Path Parameters:**
* player_id (integer, requerido): ID único del jugador a eliminar.


Elimina un jugador del sistema perteneciente al usuario autenticado.


### Request Body:
(void)


### Responses:


#### 204 No Content
(void)


Operación exitosa. El jugador fue eliminado correctamente de la base de datos.


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para eliminar un jugador."
}
```


#### 404 Not Found
```json
{
  "error": "PLAYER_NOT_FOUND",
  "message": "No se encontró un jugador con el ID especificado"
}
```


#### 409 Conflict
```json
{
  "error": "PLAYER_NOT_DELETABLE",
  "message": "Este jugador no se puede eliminar pues está en uso en una alineación o partido activo."
}
```


---


### 3. `PUT /api/v1/players/{player_id}/name`


**Path Parameters:**
* player_id (integer, requerido): ID único del jugador a cambiarle el nombre.


Actualiza el nombre de un jugador.


### Request Body:
```json
{
  "name": "Lionel Messi"
}
```


### Responses:


#### 200 OK
```json
{
  "player_id": 1,
  "name": "Lionel Messi"
}
```


#### 400 Bad Request


*Caso 1: El nombre ingresado se encuentra vacío*:
```json
{
  "error": "VOID_PLAYER_NAME",
  "message": "El nombre del jugador no puede quedar vacío."
}
```


*Caso 2: El nombre contiene caracteres no permitidos*:
```json
{
  "error": "INVALID_PLAYER_NAME",
  "message": "El nombre del jugador contiene caracteres no permitidos."
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para modificar el jugador."
}
```


#### 404 Not Found
```json
{
  "error": "PLAYER_NOT_FOUND",
  "message": "No se encontró un jugador con el ID indicado."
}
```


#### 409 Conflict
```json
{
  "error": "PLAYER_IN_ACTIVE_MATCH",
  "message": "No es posible modificar el nombre del jugador porque se encuentra jugando un partido en curso."
}
```


---


### 4. `PUT /api/v1/players/{player_id}/number`


**Path Parameters:**
* player_id (integer, requerido): ID único del jugador.


Actualiza el número de camiseta de un jugador.


### Request Body:
```json
{
  "number": 10
}
```


### Responses:


#### 200 OK
```json
{
  "player_id": 1,
  "name": "Lionel Messi",
  "number": 10
}
```


#### 400 Bad Request


*Caso 1: El número ya pertenece a otro jugador del mismo equipo*:
```json
{
  "error": "DUPLICATE_JERSEY_NUMBER",
  "message": "El número de camiseta ingresado ya está siendo utilizado por otro jugador de tu equipo."
}
```


*Caso 2: Número de camiseta fuera de rango o formato no permitido*:
```json
{
  "error": "INVALID_JERSEY_NUMBER",
  "message": "El número de camiseta debe ser un entero positivo válido."
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para realizar cambios en el jugador."
}
```


#### 404 Not Found
```json
{
  "error": "PLAYER_NOT_FOUND",
  "message": "No se encontró un jugador con el ID indicado."
}
```


#### 409 Conflict
```json
{
  "error": "PLAYER_IN_ACTIVE_MATCH",
  "message": "No es posible cambiar el número de camiseta del jugador mientras se encuentra jugando un partido o en una liga."
}
```


---


### 5. `POST /api/v1/players/{player_id}/behaviors`


**Path Parameters:**
* player_id (integer, requerido): ID único del jugador.


Asigna un comportamiento a un jugador antes de un partido.


### Request Body:
```json
{
  "behavior_id": 5
}
```


### Responses:


#### 201 Created
```json
{
  "player_id": 1,
  "behavior_id": 5
}
```


#### 400 Bad Request (Datos inválidos)
```json
{
  "error": "INVALID_ASSIGNMENT",
  "message": "No se puede asignar el comportamiento al jugador"
}
```


#### 401 Unauthorized (Sin token o token inválido)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación para realizar esta acción"
}
```


#### 403 Forbidden (El recurso pertenece a otro usuario)
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos para modificar este jugador o partido"
}
```


#### 404 Not Found (Jugador, comportamiento o partido inexistente)
```json
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "No se encontró el jugador, comportamiento o partido indicado"
}
```


#### 409 Conflict (Regla de negocio violada / duplicación)
```json
{
  "error": "BEHAVIOR_ALREADY_ASSIGNED",
  "message": "El jugador ya tiene un comportamiento asignado para este partido o el partido ya finalizó"
}
```


---


### 6. `GET /api/v1/players`


Obtener listado general de jugadores registrados por el usuario en la plataforma.


**Query Parameters:**
* page (integer, opcional, por defecto: 1): Número de página para la paginación.
* limit (integer, opcional, por defecto: 20): Cantidad de resultados por página.


### Request Body:
(void)


### Responses:


#### 200 OK (Lista de jugadores obtenida exitosamente)
```json
{
  "data": [
    {
      "player_id": 1,
      "name": "Diego Maradona",
      "number": 10
    },
    {
      "player_id": 2,
      "name": "Mario Kempes",
      "number": 5
    }
  ],
  "pagination": {
    "current_page": 1,
    "per_page": 20,
    "total_items": 2,
    "total_pages": 1
  }
}
```


#### 400 Bad Request (Parámetros de consulta inválidos)
```json
{
  "error": "INVALID_QUERY_PARAMS",
  "message": "El parámetro 'page' o 'limit' debe ser un número entero positivo"
}
```


#### 401 Unauthorized (Usuario no autenticado)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere un token de sesión válido para acceder a la lista"
}
```


<!-- COMPORTAMIENTO -->


### 1. `POST /api/v1/behaviors`


Crea un nuevo comportamiento para un jugador.


### Request Body:
```json
{
  "name": "Atacar arco",
  "description": "El jugador corre hacia adelante y patea la pelota",
  "code": "..."
}
```


### Responses:


#### 201 Created
```json
{
  "behavior_id": 1,
  "name": "Atacar arco",
  "description": "El jugador corre hacia adelante y patea la pelota",
  "code": "..."
}
```


#### 400 Bad Request (Datos de entrada inválidos)
```json
{
  "error": "INVALID_DATA",
  "message": "Los datos ingresados para el comportamiento no son válidos"
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos para crear comportamientos"
}
```


---


### 2. `DELETE /api/v1/behaviors/{behaviorId}`


**Path Parameters:**
* behaviorId (integer, requerido): ID único del comportamiento a eliminar.


Elimina un comportamiento.


### Request Body:
(void)


### Responses:


#### 204 No Content
(void)


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos para eliminar el comportamiento"
}
```


#### 404 Not Found
```json
{
  "error": "BEHAVIOR_NOT_FOUND",
  "message": "No existe un comportamiento con el ID especificado."
}
```


---


### 3. `GET /api/v1/behaviors`


Ver lista de comportamientos.


### Request Body:
(void)


### Responses:


#### 200 OK (Lista obtenida)
```json
[
  {
    "id": 1,
    "name": "Ataque agresivo",
    "description": "Presión alta al poseedor del balón",
    "code": "..."
  }
]
```


#### 200 OK (Lista vacía)
```json
[ ]
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos de ver los comportamientos"
}
```


---


### 4. `PUT /api/v1/behaviors/{behavior_id}/name`


**Path Parameters:**
* behavior_id (integer, requerido): ID único de comportamiento.


Modifica el nombre del comportamiento.


### Request Body:
```json
{
  "name": "E_Pelota",
  "description": "Patea la pelota hacia una dirección",
  "code": "..."
}
```


### Responses:


#### 200 OK
```json
{
  "behavior_id": 1,
  "name": "E_Pelota",
  "description": "Patea la pelota hacia una dirección",
  "code": "..."
}
```


#### 400 Bad Request
```json
{
  "error": "BEHAVIOR_CODE_INVALID",
  "message": "El nombre ingresado contiene errores"
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos de modificar el nombre del comportamiento"
}
```


#### 404 Not Found
```json
{
  "error": "BEHAVIOR_NOT_FOUND",
  "message": "ID del comportamiento no encontrado."
}
```


#### 409 Conflict
```json
{
  "error": "BEHAVIOR_MODIFICATION_LOCKED",
  "message": "No se puede modificar el nombre del comportamiento mientras se está jugando un partido."
}
```


---


### 5. `PUT /api/v1/behaviors/{behavior_id}/description`


**Path Parameters:**
* behavior_id (integer, requerido): ID único de comportamiento.


Modifica la descripción del comportamiento.


### Request Body:
```json
{
  "behavior_id": 1,
  "name": "Atacar arco",
  "description": "El jugador corre lado lateral derecho y patea la pelota hacia delante al centro",
  "code": "..."
}
```


### Responses:


#### 200 OK
```json
{
  "behavior_id": 1,
  "name": "Atacar arco",
  "description": "El jugador corre lado lateral derecho y patea la pelota hacia delante al centro",
  "code": "..."
}
```


#### 400 Bad Request
```json
{
  "error": "BEHAVIOR_CODE_INVALID",
  "message": "La descripción ingresada contiene errores"
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos de modificar la descripción del comportamiento"
}
```


#### 404 Not Found
```json
{
  "error": "BEHAVIOR_NOT_FOUND",
  "message": "No existe un comportamiento con el ID especificado."
}
```


#### 409 Conflict
```json
{
  "error": "BEHAVIOR_MODIFICATION_LOCKED",
  "message": "No se puede modificar la descripción del comportamiento mientras se está jugando un partido."
}
```


---


### 6. `PUT /api/v1/behaviors/{behavior_id}/code`


**Path Parameters:**
* behavior_id (integer, requerido): ID único del comportamiento a cambiarle el código.


Modifica el código fuente (actividad) del comportamiento.


### Request Body:
```json
{
  "name": "D_Pelota",
  "description": "Patea la pelota hacia una dirección",
  "code": "(codigo_nuevo)"
}
```


### Responses:


#### 200 OK
```json
{
  "name": "D_Pelota",
  "description": "Patea la pelota hacia una dirección",
  "code": "(codigo_nuevo)"
}
```


#### 404 Not Found
```json
{
  "error": "BEHAVIOR_NOT_FOUND",
  "message": "ID del comportamiento no encontrado."
}
```


#### 409 Conflict


*Caso 1: Código no permitido / sintaxis inválida*:
```json
{
  "error": "BEHAVIOR_CODE_INVALID",
  "message": "El nuevo código ingresado es inválido o contiene sintaxis no permitida."
}
```


*Caso 2: Modificación durante un partido activo*:
```json
{
  "error": "BEHAVIOR_MODIFICATION_LOCKED",
  "message": "No se puede modificar el código del comportamiento mientras se está jugando un partido."
}
```


<!-- LIGAS -->


### 1. `POST /api/v1/leagues`


Crea una liga.


### Request Body:
```json
{
  "name": "Liga FAMAF Apertura",
  "match_duration": 20,
  "max_teams": 8,
  "password": "...",
  "team_id": 1,
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


### Responses:


#### 201 Created
```json
{
  "league_id": 3,
  "name": "Liga FAMAF Apertura",
  "status": "PENDING",
  "match_duration": 20,
  "max_teams": 8,
  "current_teams_count": 1,
  "host_team_id": 1
}
```


#### 400 Bad Request


*Caso 1: Cupo inválido*:
```json
{
   "error": "INVALID_LEAGUE_CAPACITY",
   "message": "El mínimo para jugar debe ser de al menos 3 equipos"
}
```


*Caso 2: Alineación/Formación inválida*:
```json
{
   "error": "INVALID_FORMATION_OR_LINEUP",
   "message": "La formación o la alineación seleccionados no es válida."
}
```


#### 401 Unauthorized (token inválido o vencido)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Token inválido o vencido"
}
```


#### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos de crear una liga"
}
```


#### 422 Unprocessable Entity
```json
{
  "error": "INSUFFICIENT_TEAM_SETUP",
  "message": "Necesitas al menos 6 jugadores con comportamientos asignados y un comportamiento creado para crear una liga"
}
```


---


### 2. `DELETE /api/v1/leagues/{leagueId}`


**Path Parameters:**
* leagueId (integer, requerido): ID único de la liga a eliminar.


Permite al anfitrión cancelar y eliminar por completo una liga no iniciada, junto con todas sus inscripciones asociadas.


### Request Body:
(void)


### Responses:


#### 204 No Content
(void)


(Operación exitosa. La liga y sus inscripciones fueron eliminadas correctamente de la base de datos. No devuelve cuerpo).


#### 401 Unauthorized (token inválido o vencido)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Token inválido o vencido"
}
```


#### 403 Forbidden (no es el anfitrión)
```json
{
  "error": "NOT_LEAGUE_HOST",
  "message": "Sólo el anfitrión puede eliminar la liga"
}
```


#### 404 Not Found
```json
{
   "error": "LEAGUE_NOT_FOUND",
   "message": "No existe una liga con el ID especificado."
}
```


#### 409 Conflict
```json
{
   "error": "LEAGUE_ALREADY_STARTED",
   "message": "No se puede eliminar una liga que ya fue iniciada"
}
```


---


### 3. `DELETE /api/v1/leagues/{leagueId}/teams/{teamId}`


**Path Parameters:**
* leagueId (integer, requerido): ID único de la liga.
* teamId (integer, requerido): ID único del equipo a remover.


Un equipo no anfitrión abandona la liga antes de que inicie.


### Request Body:
(void)


### Responses:


#### 204 No Content
*(Salida exitosa de la liga)*.


#### 400 Bad Request


*Caso 1: Liga ya iniciada*:
```json
{
  "error": "LEAGUE_STARTED",
  "message": "No se puede abandonar una liga que ya ha comenzado."
}
```


*Caso 2: Intento de salida por parte del anfitrión*:
```json
{
  "error": "HOST_CANNOT_LEAVE",
  "message": "El anfitrión no puede abandonar la liga."
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación para abandonar la liga"
}
```


#### 403 Forbidden (Intento de remover el equipo de otro usuario sin ser anfitrión)
```json
{
  "error": "FORBIDDEN",
  "message": "No tienes permisos para remover este equipo de la liga"
}
```


#### 404 Not Found
```json
{
   "error": "TEAM_NOT_IN_LEAGUE",
   "message": "El equipo no se encuentra inscripto en esta liga."
}
```


#### 409 Conflict (Conflicto por reglas de estado)
```json
{
  "error": "LEAGUE_ALREADY_STARTED",
  "message": "No se puede abandonar o remover equipos de una liga que ya ha comenzado"
}
```


---


### 4. `GET /api/v1/leagues/{leagueId}`


**Path Parameters:**
* leagueId (integer, requerido): ID único de la liga.


Ver todos los detalles de la liga (ranking de liga, fixture de liga, etc).


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "league_id": 3,
  "name": "Liga FAMAF Apertura",
  "status": "IN_PROGRESS",
  "ranking": [ ],
  "fixture": [ ]
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación para ver detalles de la liga"
}
```


#### 404 Not Found
```json
{
  "error": "LEAGUE_NOT_FOUND",
  "message": "No existe una liga con el ID especificado."
}
```


---


### 5. `GET /api/v1/leagues`


Ver lista de ligas.


**Query Parameters:**
* page (integer, opcional, por defecto: 1): Número de página.
* limit (integer, opcional, por defecto: 20): Cantidad de resultados.
* status (string, opcional): Filtrar por estado (PENDING, IN_PROGRESS, FINISHED).


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "leagues": [
    {
      "league_id": 1,
      "league_name": "Osobuco1",
      "max_teams": 10,
      "teams_current": 5,
      "status": "IN_PROGRESS"
    },
    {
      "league_id": 2,
      "league_name": "TheParrilleros",
      "max_teams": 7,
      "teams_current": 3,
      "status": "PENDING"
    }
  ]
}
```


#### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación para ver la lista de ligas"
}
```


---


### 6. `POST /api/v1/leagues/{leagueId}/teams`


**Path Parameters:**
* leagueId (integer, requerido): ID único de la liga a la que se desea unir.


Unirse a una liga.


### Request Body:


*Caso 1: (Liga Privada)*
```json
{
  "team_id": 42,
  "password": "Asado123",
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


*Caso 2: (Liga Pública)*
```json
{
  "team_id": 42,
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


### Responses:


#### 201 Created
```json
{
  "league_id": 1,
  "team_id": 42
}
```


#### 400 Bad Request


*Caso 1: Liga iniciada*:
```json
{
   "error": "LEAGUE_UNAVAILABLE",
   "message": "La liga ya está iniciada"
}
```


*Caso 2: Requiere contraseña*:
```json
{
   "error": "PASSWORD_UNAVAILABLE",
   "message": "La liga requiere una contraseña"
}
```


*Caso 3: Alineación/Formación inválida*:
```json
{
   "error": "INVALID_LINEUP",
   "message": "La formación o la alineación seleccionados no es válida."
}
```


#### 401 Unauthorized
```json
{
   "error": "PASSWORD_INCORRECT",
   "message": "La contraseña ingresada no es correcta"
}
```


#### 404 Not Found
```json
{
   "error": "NOT_FOUND",
   "message": "La liga no fue encontrada"
}
```


#### 409 Conflict
```json
{
   "error": "ALREADY_JOINED",
   "message": "El equipo ya se unió a esta liga"
}
```


---


### 7. `PUT /api/v1/leagues/{leagueId}`


**Path Parameters:**
* leagueId (integer, requerido): ID único de la liga a modificar.


Iniciar una liga.


### Request Body:
```json
{
  "status": "IN_PROGRESS"
}
```


### Responses:


#### 200 OK
```json
{
  "league_id": 2,
  "league_name": "TheParrilleros",
  "status": "IN_PROGRESS"
}
```


#### 400 Bad Request


*Caso 1: Cantidad insuficiente de equipos*:
```json
{
  "error": "INSUFFICIENT_TEAMS",
  "message": "La liga no contiene el mínimo de equipos"
}
```


*Caso 2: Liga ya iniciada*:
```json
{
  "error": "LEAGUE_ALREADY_STARTED",
  "message": "La liga ya está iniciada"
}
```


#### 403 Forbidden
```json
{
  "error": "UNAUTHORIZED_ACTION",
  "message": "El usuario no es el anfitrión de la liga y no puede iniciarla"
}
```


#### 404 Not Found
```json
{
  "error": "LEAGUE_NOT_FOUND",
  "message": "La liga seleccionada no existe"
}
```


<!-- PARTIDOS -->


### 1. `POST /api/v1/matches/friendly`


Crea un partido amistoso.


### Request Body:
```json
{
  "team_id": 1,
  "match_duration": 10,
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


### Responses:


#### 201 Created
```json
{
  "match_id": 5,
  "team_host_id": 5,
  "status": "PENDING",
  "match_duration": 10
}
```


#### 400 Bad Request


*Caso 1: Duración del partido inválida*:
```json
{
  "error": "INVALID_SETTINGS",
  "message": "La duración del partido es inválida"
}
```


*Caso 2: Alineación/Formación inválida*:
```json
{
  "error": "INVALID_LINEUP",
  "message": "La formación o la alineación seleccionados no es válida."
}
```


#### 422 Unprocessable Entity
```json
{
  "error": "PRECONDITION_FAILED",
  "message": "El equipo no cumple con el mínimo de jugadores y comportamientos creados"
}
```


---


### 2. `GET /api/v1/matches/friendly`


Lista los partidos amistosos.


**Query Parameters:**
* page (integer, opcional, por defecto: 1): Número de página.
* limit (integer, opcional, por defecto: 20): Cantidad de resultados.
* status (string, opcional): Filtrar por estado (PENDING, IN_PROGRESS, FINISHED).


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "matches": [
    {
      "match_id": 5,
      "team_host_id": 5,
      "team_away_id": 7,
      "status": "IN_PROGRESS",
      "match_duration": 10
    },
    {
      "match_id": 6,
      "team_host_id": 12,
      "team_away_id": null,
      "status": "PENDING",
      "match_duration": 15
    }
  ]
}
```


#### 200 OK (Lista vacía)
Si actualmente no existen partidos amistosos creados o disponibles:
```json
{
  "matches": [ ]
}
```


---


### 3. `DELETE /api/v1/matches/{matchId}`


**Path Parameters:**
* matchId (integer, requerido): ID único del partido a eliminar.


El anfitrión cancela y elimina un partido amistoso específico que aún no ha comenzado.


### Request Body:
(void)


### Responses:


#### 204 No Content
(void)


#### 400 Bad Request
```json
{
  "error": "MATCH_ALREADY_STARTED",
  "message": "No se puede cancelar el amistoso porque ya está iniciado"
}
```


#### 403 Forbidden
```json
{
  "error": "UNAUTHORIZED_ACTION",
  "message": "El usuario no es el anfitrión por lo que no puede cancelar el amistoso"
}
```


#### 404 Not Found (Partido inexistente)
```json
{
  "error": "MATCH_NOT_FOUND",
  "message": "El partido solicitado no existe."
}
```


---


### 4. `GET /api/v1/matches/{matchId}/result`


**Path Parameters:**
* matchId (integer, requerido): ID único del partido.


Obtiene el resultado final de un partido una vez que ha concluido.


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "match_id": 12,
  "status": "FINISHED",
  "home_team": {
    "team_id": 5,
    "name": "alfajoresFC",
    "goals": 3
  },
  "away_team": {
    "team_id": 8,
    "name": "BocaJuniors",
    "goals": 1
  }
}
```


#### 400 Bad Request (El partido no ha terminado)
```json
{
  "error": "MATCH_NOT_FINISHED",
  "message": "El resultado final aún no está disponible."
}
```


#### 404 Not Found
```json
{
  "error": "MATCH_NOT_FOUND",
  "message": "El partido solicitado no existe."
}
```


---


### 5. `POST /api/v1/matches/{matchId}/join`


**Path Parameters:**
* matchId (integer, requerido): ID único del partido amistoso al que se desea unir.


Permite a un usuario inscribir a su equipo en un partido amistoso en espera.


### Request Body:
```json
{
  "team_id": 8,
  "formation_type": "2-1",
  "starting_players": [
    {
      "player_id": 101,
      "role": "DEF_LEFT",
      "behavior_id": 1
    },
    {
      "player_id": 102,
      "role": "DEF_RIGHT",
      "behavior_id": 1
    },
    {
      "player_id": 103,
      "role": "FORWARD",
      "behavior_id": 2
    }
  ],
  "substitute_players": [
    {
      "player_id": 104,
      "behavior_id": 1
    },
    {
      "player_id": 105,
      "behavior_id": 3
    },
    {
      "player_id": 106,
      "behavior_id": 2
    }
  ]
}
```


### Responses:


#### 200 OK
```json
{
  "match_id": 12,
  "status": "IN_PROGRESS",
  "message": "Te has unido al partido exitosamente."
}
```


#### 400 Bad Request (Sala llena o equipo ya inscrito)


*Alineación/Formación inválida*:
```json
{
   "error": "INVALID_SETTING",
   "message": "La alineación y/o la formación es inválida."
}
```


#### 404 Not Found
```json
{
   "error": "MATCH_NOT_FOUND",
   "message": "El partido solicitado no existe o fue cancelado por el anfitrión."
}
```


#### 409 Conflict
```json
{
   "error": "MATCH_UNAVAILABLE",
   "message": "El partido ya está iniciado o su cupo ya fue ocupado por otro equipo"
}
```


#### 422 Unprocessable Entity
```json
{
   "error": "PRECONDITION_FAILED",
   "message": "El equipo no tiene el mínimo de jugadores y comportamientos creados"
}
```


<!-- RANKING -->


### 1. `GET /api/v1/rankings/global`


Devuelve la lista global de equipos ordenados de mayor a menor puntuación.


**Query Parameters:**
* page (integer, opcional, por defecto: 1): Número de página.
* limit (integer, opcional, por defecto: 50): Cantidad máxima de resultados.


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "ranking": [
    {
      "position": 1,
      "team_id": 5,
      "team_name": "alfajoresFC",
      "points": 120,
      "matches_played": 45
    },
    {
      "position": 2,
      "team_id": 8,
      "team_name": "BocaJuniors",
      "points": 115,
      "matches_played": 44
    }
  ]
}
```


#### 401 Unauthorized
```json
{
   "error": "UNAUTHORIZED_ACCESS",
   "message": "Debes iniciar sesión para ver el ranking global de equipos"
}
```


<!-- DENTRO DEL PARTIDO -->


### 1. `POST /api/v1/matches/{matchId}/teams/{teamId}/substitutions`


**Path Parameters:**
* matchId (integer, requerido): ID único del partido que se está jugando.
* teamId (integer, requerido): ID único del equipo donde se hará el cambio.


Intercambio de jugador en el descanso.


### Request Body:
```json
{
  "player_in_id": 7,
  "player_out_id": 10
}
```


### Responses:


#### 201 Created
```json
{
  "substitution_id": 9,
  "match_id": 4,
  "team_id": 17,
  "player_in_id": 7,
  "player_out_id": 10
}
```


#### 400 Bad Request


*Caso 1: Partido no está en descanso*:
```json
{
   "error": "MATCH_NOT_IN_BREAK",
   "message": "El partido no está en descanso para realizar el cambio de jugador"
}
```


*Caso 2: Selección de jugador inválida*:
```json
{
  "error": "INVALID_PLAYER_SELECTION",
  "message": "El jugador seleccionado no está en cancha o el jugador seleccionado no está en el banco de suplentes"
}
```


#### 401 Unauthorized (Sin token válido)
```json
{
  "error": "UNAUTHORIZED",
  "message": "Se requiere autenticación para realizar este cambio"
}
```


#### 403 Forbidden
```json
{
   "error": "UNAUTHORIZED_ACTION",
   "message": "No está permitido realizar cambios en este equipo"
}
```


#### 404 Not Found
```json
{
   "error": "RESOURCE_NOT_FOUND",
   "message": "El partido, el equipo o el jugador solicitado no existe"
}
```


#### 409 Conflict
```json
{
  "error": "SUBSTITUTION_LIMIT_EXCEEDED",
  "message": "El equipo ya ha realizado un cambio"
}
```


---


### 2. `PUT /api/v1/matches/{match_id}/players/{player_id}/behavior`


**Path Parameters:**
* match_id (integer, requerido): ID único del partido.
* player_id (integer, requerido): ID único del jugador.


Intercambia el comportamiento de un jugador durante un partido.


### Request Body:
```json
{
  "behavior_id": 8
}
```


### Responses:


#### 200 OK
```json
{
  "match_id": 12,
  "player_id": 1,
  "behavior_id": 8
}
```


#### 400 Bad Request
```json
{
  "error": "MATCH_FINISHED",
  "message": "El comportamiento no se puede cambiar porque el partido ya ha finalizado"
}
```


#### 403 Forbidden
```json
{
  "error": "UNAUTHORIZED_ACTION",
  "message": "No tienes permisos para modificar el comportamiento de un jugador del equipo rival."
}
```


#### 404 Not Found (Partido, jugador o comportamiento inexistente)
```json
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "No se encontró el partido, jugador o comportamiento indicado"
}
```


#### 422 Unprocessable Entity
```json
{
  "error": "INVALID_BEHAVIOR",
  "message": "El comportamiento seleccionado no pertenece a tu equipo"
}
```


---


### 3. `GET /api/v1/matches/{matchId}/state`


**Path Parameters:**
* matchId (integer, requerido): ID único del partido en curso.


Obtiene el estado actual del partido, incluyendo estadísticas de todos los jugadores. El backend filtrará los `behavior_id` de los rivales según el usuario que realice la petición.


### Request Body:
(void)


### Responses:


#### 200 OK
```json
{
  "match_id": 12,
  "current_tick": 450,
  "score": {
    "home": 1,
    "away": 0
  },
  "players": [
    {
      "player_id": 101,
      "team_id": 5,
      "name": "Lionel Messi",
      "number": 10,
      "attributes": {
        "power": 80,
        "agility": 75,
        "control": 90,
        "speed": 35,
        "strength": 20
      },
      "behavior_id": 3
    },
    {
      "player_id": 205,
      "team_id": 8,
      "name": "Cristiano Ronaldo",
      "number": 7,
      "attributes": {
        "power": 85,
        "agility": 70,
        "control": 80,
        "speed": 40,
        "strength": 25
      },
      "behavior_id": null
    }
  ]
}
```


#### 400 Bad Request


*Caso 1: Partido finalizado*:
```json
{
   "error": "MATCH_FINISHED",
   "message": "El partido ya ha finalizado."
}
```


*Caso 2: Partido aún no iniciado*:
```json
{
   "error": "MATCH_PENDING",
   "message": "El partido aún no ha comenzado."
}
```


#### 403 Forbidden
```json
{
   "error": "UNAUTHORIZED_ACTION",
   "message": "No se permite ver el estado de los partidos en los que no participas"
}
```


#### 404 Not Found
```json
{
  "error": "MATCH_NOT_FOUND",
  "message": "El partido solicitado no existe."
}
```
<!-- WEBSOCKETS -->
**Arquitectura de Notificaciones:** El sistema utiliza **Event Bus** en el servidor para interceptar los eventos generados por las acciones HTTP REST y retransmitirlos de forma asíncrona hacia las conexiones WebSocket activas.


### 1. POST /api/v1/live-token/global


Solicita autorización y obtiene el token de acceso para la conexión global de eventos del sistema (lobbies, cambios de estado y notificaciones en tiempo real).


### Request Body:
(void)


### Responses:
200 OK
```json
{
 "websocket_url": "wss://futbot.famaf.unc.edu.ar/ws/v1/global?token=eyJhbGciOiJIUzI1NiIn...",
 "expires_in_seconds": 60
}
```


## 2. WS /ws/v1/global


Canal unidireccional (Servidor $\rightarrow$ Cliente) alimentado por el Event Bus para enviar eventos del sistema, notificaciones de lobbies y actualizaciones de partidos/ligas.


**Query Parameters:**


* token (string, requerido): Token temporal obtenido mediante *POST /api/v1/live-token/global*.


Evento Servidor $\rightarrow$ Cliente (Estructura Genérica):


```json
{
 "event": "NOMBRE_DEL_EVENTO",
 "data": {}
}
```


### Catálogo de Eventos Globales:


* **FRIENDLY_CREATED:** Un usuario anfitrión creó un partido amistoso.
```json
{
 "event": "FRIENDLY_CREATED",
 "data": { "match_id": 12, "team_host_id": 5, "match_duration": 10, "status": "PENDING"}
}
```


* **FRIENDLY_CANCELLED:** El usuario anfitrión eliminó el partido amistoso.
```json
{
 "event": "FRIENDLY_CANCELLED",
 "data": { "match_id": 12 }
}
```


* **GUEST_JOINED_FRIENDLY:** Un rival se unió al amistoso.
```json
{
 "event": "GUEST_JOINED_FRIENDLY",
 "data": { "match_id": 12, "team_host_id": 5, "team_away_id": 8}
}
```


* **LEAGUE_CREATED:** Se creó una nueva liga.
```json
{
 "event": "LEAGUE_CREATED",
 "data": { "league_id": 3, "name": "Copa FAMAF", "max_teams": 8, "status": "PENDING"}
}
```


* **LEAGUE_CANCELLED:** El creador eliminó la liga.
```json
{
 "event": "LEAGUE_CANCELLED",
 "data": { "league_id": 3 }
}
```


* **TEAM_JOINED_LEAGUE:** Un equipo se inscribió en la liga.
```json
{
 "event": "TEAM_JOINED_LEAGUE",
 "data": { "league_id": 3, "team_id": 14, "registered_teams": 6, "max_teams": 8 }
}
```


* **MATCH_STARTED:** Notifica el inicio de un partido.
```json
{
 "event": "MATCH_STARTED",
 "data": { "match_id": 12, "team_host_id": 5, "team_away_id": 8, "status": "IN_PROGRESS"}
}
```


* **LEAGUE_MATCH_FINISHED:** Notifica la finalización de un partido del fixture de la liga para refrescar la tabla de posiciones.
```json
{
 "event": "LEAGUE_MATCH_FINISHED",
 "data": { "league_id": 3, "match_id": 12, "score": { "home": 2, "away": 1 }, "status": "FINISHED"}
}
```


### 3. POST /api/v1/matches/{matchId}/live-token


Solicita autorización y obtiene el token de acceso/URI para conectarse a la transmisión en vivo del partido mediante WebSocket.


**Path Parameters:**


* matchId (integer, requerido): ID único del partido en curso.


### Request Body:
(void)


### Responses:
200 OK
```json
{
 "match_id": 12,
 "websocket_url": "wss://futbot.famaf.unc.edu.ar/ws/v1/matches/12?token=eyJhbGciOiJIUzI1NiIn...",
 "expires_in_seconds": 60
}
```


### 404 Not Found:
```json
{
  "error": "MATCH_NOT_FOUND",
  "message": "El partido solicitado no existe o no se encuentra en ejecución."
}
```


### 4. WS /ws/v1/matches/{matchId}


Canal bidireccional en tiempo real para ejecución tick a tick del partido y el envío de comandos tácticos.


**Path Parameters:**


* matchId (integer, requerido): ID único del partido en curso.


**Query Parameters:**


* token (string, requerido): Token temporal obtenido a través de *POST /api/v1/matches/{matchId}/live-token*.


Evento Servidor $\rightarrow$ Cliente (Transmisión por Tick)
```json
{
 "event": "TICK_UPDATE",
 "match_id": 12,
 "current_tick": 45,
 "total_ticks": 1200,
 "score": { "home": 1, "away": 0 },
 "ball": { "x": 52.4, "y": 30.1 },
 "players": [
   {
     "player_id": 101,
     "team_id": 5,
     "position": { "x": 50.0, "y": 28.5 },
     "current_action": "RUN"
   }
 ]
}
```


### Diferencias:
* **Event Bus Global:** Notificaciones y eventos de control (baja frecuencia).


* **Canal de Partido:** Telemetría del simulador (alta frecuencia / baja latencia).


>Aclaración: El evento Cliente $\rightarrow$ Servidor que conectaría el canal para, por ejemplo: cambios de comportamientos, se omite pues ya hay un endpoint HTTP que realiza el intercambio de comportamientos.



# PRIMITIVAS de Comportamientos


## 1. Visión General


*Este contrato especifica la interfaz que los comportamientos escritos en Python deben implementar para interactuar con el motor del juego FutBot. Define las primitivas permitidas, las estructuras de datos expuestas y el ciclo de validación del código.*


## 1.1 Tecnologías utilizadas:
* Python
* Enum
* Dataclass


## 2. Tipos de Datos y Estructuras Básicas


### Vector2D


Representa una **dirección, desplazamiento o velocidad**:


```python
from dataclasses import dataclass


@dataclass(frozen=True)
class Vector2D:
   x: float
   y: float


   def __init__(self, x: float, y: float): ...


   def magnitude(self) -> float:
       """
       @brief Devuelve la rapidez o fuerza del vector (su longitud/escalar).
              Útil para calcular la velocidad independientemente de su dirección
       """
  
   def normalize(self) -> 'Vector2D':
       """
       @brief Devuelve un vector de magnitud 1 en la misma dirección (vector unitario).
              Útil para calcular hacia donde va un objetivo
       """
```


### Point2D


Representa un **punto espacial o posición fija** en el plano de la cancha:


```python
@dataclass(frozen=True)
class Point2D:
   x: float
   y: float


   def __init__(self, x: float, y: float): ...


   def distance_to(self, other: 'Point2D') -> float:
       """
       @brief Calcula la distancia hacia otro Point2D.
       @param other Point2D a calcular la distancia.
       """
       ...


   def direction_to(self, target: 'Point2D') -> Vector2D:
       """
       @brief Retorna el Vector2D de dirección que apunta hacia 'target'.
       """
       ...
```


### AttributeType (Tipo de Atributo)


Identificamos cada atributo de jugador con un tipo.


```python
from enum import Enum


class AttributeType(Enum):
   POWER = "POWER"
   AGILITY = "AGILITY"
   CONTROL = "CONTROL"
   SPEED = "SPEED"
   STRENGTH = "STRENGTH"
```


### GameState (Vista de Contexto)


Representa la información del juego visible para el jugador en el tick actual.


```python
@dataclass(frozen=True)
class GameState:
   ball_position: Point2D        # Posición en cancha de la pelota
   ball_velocity: Vector2D       # Velocidad inmediata de la pelota
   my_position: Point2D          # Posición en cancha del jugador
   teammates: list[Point2D]      # Posiciones de los compañeros del jugador
   opponents: list[Point2D]      # Posiciones de los rivales del jugador
   team_goal: Point2D            # Posición del arco aliado
   opponent_goal: Point2D        # Posición del arco rival
```


## 3. Primitivas Disponibles (PlayerAPI)


Instancia que se inyecta al comportamiento en cada ciclo para ejecutar acciones sobre el jugador.


*Observación: Estos son unos ejemplos de Primitivas a modo de prototipo, se puede cambiar*


```python


class PlayerAPI:
   """
   Interfaz inyectada por el servidor.
   Toda consulta o acción pasa exclusivamente a través de esta clase.
   """


   # --- PRIMITIVAS DE CONSULTA (GETTERS / LECTURA) ---
   # Solicitan traer información al servidor


   def get_game_state(self) -> GameState:
       """
       @brief Solicita y retorna la fotografía real del partido en este tick.
       @return Instancia inmutable de GameState traída desde el servidor.
       """
       ...


   def get_attribute(self, attribute_name: AttributeType) -> int:
       """
       @brief Devuelve el valor numérico (20-100) del atributo consultado.
       @param attribute_name Tipo de atributo (AttributeType).
       @return Entero de solo lectura desde la base de datos del servidor.
       """
       ...


   def get_reach_distance(self) -> float:
       """
       @brief Devuelve la distancia máxima de interacción (en metros/unidades)
              calculada dinámicamente por el servidor según el atributo CONTROL.
       """
       ...


   # --- PRIMITIVAS DE ACCIÓN (COMANDOS / INTENCIONES) ---
   # Informa al servidor sobre la intención que tiene el usuario en ese tick


   def run_to(self, target: Point2D, speed_percentage: float = 1.0) -> None:
       """
       @brief Registra la intención de desplazarse hacia una posición objetivo.
       @param target Posición geográfica Point2D dentro de los límites de la cancha.
       @param speed_percentage Porcentaje de velocidad deseado (0.0 a 1.0).
       """
       ...


   def kick_ball(self, target_direction: Vector2D, power_percentage: float = 1.0) -> None:
       """
       @brief Registra la intención de patear la pelota en una dirección específica.
       @param target_direction Vector2D de orientación del disparo.
       @param power_percentage Porcentaje de fuerza máxima a aplicar (0.0 a 1.0).
       """
       ...


   def idle(self) -> None:
       """
       @brief Registra la intención de permanecer detenido en el lugar.
       """
       ...
```


## 4. Punto de Entrada del Comportamiento (Entry Point)


Todo Script subido por el usuario debe exponer una función principal denominada on_tick, sobre la cuál se procesa el ciclo de intenciones del usuario en un tick.


### Firma de la Función


```python
def on_tick(api: PlayerAPI) -> None:


   """
   @brief Invocado por el servidor en cada tick.
   @param api Métodos de acción que puede llamar el usuario.
   """
   pass
```


### Ejemplo de Comportamiento Válido - PATEAR AL ARCO


```python
# Comportamiento simple: Patear al Arco
def on_tick(api: PlayerAPI) -> None:
   # 1. Traer la "foto real" del estado desde el servidor
   state = api.get_game_state()


   # 2. Consultar al servidor la distancia física de alcance
   alcance_maximo = api.get_reach_distance()


   # 3. Evaluar distancia espacial
   distancia_a_pelota = state.my_position.distance_to(state.ball_position)


   # 4. Decisión y Registro de Intención
   if distancia_a_pelota <= alcance_maximo:
       # Orientar hacia el arco rival y patear
       direccion_arco = state.my_position.direction_to(state.opponent_goal)
       api.kick_ball(target_direction=direccion_arco, power_percentage=1.0)
   else:
       # Desplazarse hacia la pelota
       api.run_to(target=state.ball_position, speed_percentage=1.0)
```


## 5. Especificación del Validador de Código


Para garantizar la seguridad del cliente/servidor y la validez funcional del script, el código Python subido debe pasar por una validación antes de guardarse en la base de datos.


### 5.1 Reglas de Validación


**Función principal on_tick(api):** Todo comportamiento debe iniciar mediante la función que evalúa el ciclo de un tick, siendo cada llamada a la primitiva dentro de on_tick las peticiones de intención al servidor.


Luego, el pipeline de seguridad de FutBot se divide en dos fases independientes:


1. **Fase de Validación Previa (Static Analysis / AST):**
  Antes de almacenar un comportamiento en la Base de Datos, el servidor analiza el árbol de sintaxis abstracta (AST) del código Python.
  * **Rechazo Inmediato:** Se deniega la persistencia si se detectan asignaciones a propiedades de objetos inmutables, bucles infinitos no acotados (`while True`) o importación de librerías del sistema (`sys`, `os`, `socket`).


2. **Fase de Simulación (Runtime Fault Tolerance):**
  * **Inmutabilidad (`frozen=True`):** Actúa como segunda capa de protección.
  * **Manejo de Excepciones:** La ejecución de `on_tick(api)` ocurre dentro de un entorno aislado. Si un script arroja una excepción no controlada durante un partido, el motor captura el fallo, asigna la intención `idle()` para ese jugador en dicho tick y el partido continúa sin interrupciones.

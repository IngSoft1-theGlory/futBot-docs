# CONTRATO API

**USUARIO:**  
 + post crear usuario  
 + post iniciar sesión y autenticar  
 + post cerrar sesión  
 + put cambiar nombre de usuario  
 + delete eliminar usuario

**EQUIPO:**  
 + put modificar nombre de equipo  
 + put modificar logo de equipo  
 + get ver perfil de un equipo

**RANKING:**  
 + get ver ranking global

**JUGADOR:**  
 + post crear jugador  
 + delete eliminar jugador  
 + put cambiar nombre de jugador  
 + put cambiar número de jugador  
 + get obtener lista de jugadores

**COMPORTAMIENTO:**  
 + post crear comportamientos  
 + delete eliminar comportamiento  
 + get ver lista de comportamientos  
 + get ver detalle de comportamientos

**LIGAS:**  
 + post crear liga  
 + delete cancelar liga  
 + delete abandonar liga  
 + get ver todos los detalles de la liga (ranking de liga, fixture de liga)  
 + get listar ligas  
 + post verificar contraseña al unirse a una liga privada  
 + post unirse a una liga  
 + put iniciar la liga

**PARTIDO:**  
 + post crear partido amistoso  
 + get listar partidos amistosos  
 + delete eliminar partido  
 + get ver resultado final de un partido  
 + post unirse a un amistoso

+ get detalles del partido amistoso

**DENTRO DEL PARTIDO:**  
 + post intercambiar jugadores en partido (requiere estar en una pausa)  
 + put intercambiar comportamiento de un jugador en partido  
 + get ver detalles dentro del partido

<!-- WEBSOCKETS -->  
 **WEBSOCKETS:**  
 + post solicitar autorización mediante websockets  
 + WS canal unidireccional en tiempo real para eventos del sistema  
 + post solicitar autorización para conectarse al partido  
 + WS canal unidireccional en tiempo real para la simulación tick a tick

---

**Errores generales de validación**  
 Además de los errores de negocio documentados en cada endpoint, cualquier endpoint puede devolver:

422 Unprocessable Entity — el body del request no tiene el formato o los tipos esperados (campo faltante, tipo incorrecto, formato inválido según el schema). También aplica a parámetros de la URL con un tipo o valor inválido, salvo en los endpoints que documentan su propio 400 `INVALID_QUERY_PARAMS` (`GET /api/v1/players` y `GET /api/v1/behaviors`).

```json  
 {  
 "error": "VALIDATION_ERROR",  
 "message": "Los datos enviados no son válidos."  
 }  
 ```

---

**Configuración táctica (formaciones y roles)**  
 Los endpoints de crear/unirse a liga o amistoso reciben `formation_type`, 3 `starting_players` (cada uno con `role` y `behavior_id`) y 3 `substitute_players` (sin `role`). Formaciones y roles válidos:

* `"2-1"`: `DEF_LEFT`, `DEF_RIGHT`, `FORWARD`  
 * `"1-2"`: `DEFENDER`, `FORWARD_LEFT`, `FORWARD_RIGHT`

Reglas de validación de la alineación (si alguna no se cumple, se devuelve 400 `INVALID_CONFIGURATION`):

* Exactamente 3 titulares y 3 suplentes.  
 * Los roles de los titulares tienen que ser exactamente los de la formación elegida, uno de cada uno.  
 * Un mismo jugador no puede aparecer dos veces en la alineación.  
 * Todos los jugadores tienen que pertenecer al equipo del usuario autenticado.  
 * Todos los `behavior_id` tienen que existir y estar disponibles para el usuario.

Además, el equipo tiene que tener al menos 6 jugadores creados; si no, se devuelve 422 `PRECONDITION_FAILED`.

El comportamiento se asigna a cada jugador dentro de la alineación de ese partido (no queda asignado al jugador de forma permanente).

---

**Duración de los partidos**  
 El campo `match_duration` (crear liga y crear amistoso) es la duración de cada partido en minutos. Solo se aceptan los valores `2` o `5`. Internamente, el backend la convierte en una cantidad fija de ticks. Cualquier otro valor devuelve 400 `INVALID_SETTINGS`.

**Duración de un tick:** cada tick representa 0,5 segundos de juego y el servidor emite un tick cada 0,5 segundos reales (el partido se transmite en tiempo real). Por lo tanto:

* `match_duration: 2` → `total_ticks: 240`  
 * `match_duration: 5` → `total_ticks: 600`

El tiempo transcurrido de un partido se calcula como `current_tick × 0,5` segundos.

---

**Cancha y sistema de coordenadas**  
 Todas las posiciones (`TICK_UPDATE`, primitivas de comportamiento) usan las mismas unidades (metros) y el mismo sistema de coordenadas:

* La cancha mide 105 de largo (eje `x`, de `0` a `105`) por 60 de ancho (eje `y`, de `0` a `60`). El centro de la cancha es `(52.5, 30)`, que es donde arranca la pelota.  
 * Los arcos están sobre las líneas de fondo (`x = 0` y `x = 105`), centrados en `y = 30`, con un ancho de 7,32 (entre `y = 26.34` e `y = 33.66`).  
 * El anfitrión del partido juega de local (`home`): defiende el arco de `x = 0` y ataca hacia `x = 105`. El visitante (`away`) es el espejo.  
 * En el marcador (`score`), `home` son los goles del anfitrión y `away` los del visitante.  
 * El eje `y` crece hacia la izquierda del equipo local mirando hacia el arco rival: por eso `DEF_LEFT` y `FORWARD_LEFT` arrancan con `y` alto. Si el cliente dibuja la cancha con `y` creciendo hacia abajo (por ejemplo en SVG), tiene que invertir `y` (`60 - y`) para que la izquierda quede arriba.  
 * No hay áreas ni punto penal (no hay arquero ni faltas).

Posiciones iniciales de los titulares del local (el visitante usa las mismas, espejadas: `(105 - x, 60 - y)`):

| Formación | Rol | Posición inicial |
| :---- | :---- | :---- |
| `2-1` | `DEF_LEFT` | `(25, 15)` |
| `2-1` | `DEF_RIGHT` | `(25, 45)` |
| `2-1` | `FORWARD` | `(45, 30)` |
| `1-2` | `DEFENDER` | `(25, 30)` |
| `1-2` | `FORWARD_LEFT` | `(45, 15)` |
| `1-2` | `FORWARD_RIGHT` | `(45, 45)` |



Los suplentes no están en cancha y no tienen posición.

---

<!-- USUARIO -->

### 1. `POST /api/v1/auth/register`

Registra un nuevo usuario en la plataforma junto con su equipo. El usuario y el equipo se crean en una única operación: si algún dato es inválido, no se crea ninguno de los dos.

**Sobre `team_logo`:** el logo es un archivo de imagen adjuntado por el usuario, que el cliente convierte a *data URL* en base64 (`data:image/<tipo>;base64,<contenido>`). Formatos permitidos: PNG, JPG/JPEG y SVG (`image/png`, `image/jpeg`, `image/svg+xml`). Tamaño máximo del archivo: 1 MB. En los ejemplos el contenido base64 aparece recortado con `...`.

### Request Body:  
 ```json  
 {  
 "username": "Maradona10",  
 "email": "diego@famaf.unc.edu.ar",  
 "password": "Password123!",  
 "team_name": "alfajoresFC",  
 "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."  
 }  
 ```

### Responses:

#### 201 Created  
 ```json  
 {  
 "user_id": 1,  
 "team_id": 1,  
 "username": "Maradona10",  
 "email": "diego@famaf.unc.edu.ar",  
 "team_name": "alfajoresFC",  
 "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."  
 }  
 ```

#### 400 Bad Request (Datos de entrada inválidos o duplicados)

*Caso 1: Email ya pertenece a otro usuario*:  
 ```json  
 {  
 "error": "DUPLICATE_EMAIL",  
 "message": "El email ingresado se encuentra en uso. Intentar de nuevo"  
 }  
 ```

*Caso 2: Nombre de usuario vacío*:  
 ```json  
 {  
 "error": "VOID_USERNAME",  
 "message": "El campo nombre de usuario no puede estar vacío."  
 }  
 ```

*Caso 3: Formato del Email inválido*:  
 ```json  
 {  
 "error": "INVALID_FORMAT",  
 "message": "El email ingresado no tiene un formato válido."  
 }  
 ```

*Caso 4: Nombre de equipo vacío*:  
 ```json  
 {  
 "error": "VOID_TEAM_NAME",  
 "message": "El nombre del equipo no puede quedar vacío."  
 }  
 ```

*Caso 5: Logo no proporcionado / campo de logo vacío*:  
 ```json  
 {  
 "error": "VOID_TEAM_LOGO",  
 "message": "Debes proporcionar un logo para el equipo."  
 }  
 ```

*Caso 6: Formato de logo inválido*:  
 ```json  
 {  
 "error": "INVALID_LOGO_FORMAT",  
 "message": "El logo debe ser una imagen .png, .jpg, .jpeg o .svg codificada en base64 (data URL)."  
 }  
 ```

*Caso 7: Logo demasiado grande*:  
 ```json  
 {  
 "error": "LOGO_TOO_LARGE",  
 "message": "El logo no puede superar 1 MB."  
 }  
 ```

---

### 2. `POST /api/v1/auth/login`

Autentica las credenciales de un usuario y genera una sesión mediante un token de acceso (JWT). El token vence a los 120 minutos y no se renueva: al vencer, cualquier endpoint protegido devuelve 401 y el usuario tiene que volver a iniciar sesión.

Si el email no tiene un formato válido, se devuelve 422 `VALIDATION_ERROR` (ver *Errores generales de validación*).

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
 "team_id": 1,  
 "username": "Maradona10",  
 "email": "diego@famaf.unc.edu.ar"  
 }  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para modificar el usuario."  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para eliminar el usuario."  
 }  
 ```

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

### 1. `PUT /api/v1/teams/{teamId}/name`

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
 "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."  
 }  
 ```

#### 400 Bad Request (Nombre de equipo vacío o inválido)  
 ```json  
 {  
 "error": "VOID_TEAM_NAME",  
 "message": "El nombre del equipo no puede quedar vacío o contener sólo espacios."  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para modificar el equipo."  
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

### 2. `PUT /api/v1/teams/{teamId}/logo`

**Path Parameters:**  
 * teamId (integer, requerido): ID único del equipo a modificar el logo.

Cambia el logo del equipo especificado. El nuevo logo sigue el mismo formato que en el registro (data URL en base64, PNG/JPG/JPEG/SVG, máximo 1 MB).

### Request Body:  
 ```json  
 {  
 "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."  
 }  
 ```

### Responses:

#### 200 OK  
 ```json  
 {  
 "team_id": 1,  
 "team_name": "sacachispasFC",  
 "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."  
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

*Caso 2: Formato de logo inválido*:  
 ```json  
 {  
 "error": "INVALID_LOGO_FORMAT",  
 "message": "El logo debe ser una imagen .png, .jpg, .jpeg o .svg codificada en base64 (data URL)."  
 }  
 ```

*Caso 3: Logo demasiado grande*:  
 ```json  
 {  
 "error": "LOGO_TOO_LARGE",  
 "message": "El logo no puede superar 1 MB."  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para modificar el equipo."  
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

### 3. `GET /api/v1/teams/{teamId}`

**Path Parameters:**  
 * teamId (integer, requerido): ID único del equipo a consultar.

Muestra las estadísticas de un equipo.

### Request Body:  
 (void)

### Responses:  
 #### 200 OK  
 ```json  
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

<!-- JUGADOR -->

### 1. `POST /api/v1/players`

Crea un nuevo jugador y lo asigna al equipo del usuario autenticado. El equipo se toma siempre del token de sesión: si el body trae un `team_id`, se ignora.

Reglas:

* `name`: obligatorio, entre 1 y 50 caracteres (se ignoran los espacios al principio y al final).  
 * `number`: entero positivo, único dentro del equipo (dos equipos distintos pueden usar el mismo número).  
 * `power`, `agility`, `control`, `speed`, `strength`: enteros entre 20 y 100 que suman exactamente 300.  
 * `number` y los atributos tienen que ser enteros: un texto (`"50"`), un decimal (`50.5`) o un booleano devuelven 422 `VALIDATION_ERROR`.

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

*Caso 4: Número de camiseta inválido (no positivo)*:  
 ```json  
 {  
 "error": "INVALID_PLAYER_DATA",  
 "message": "El número de camiseta debe ser un entero positivo."  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere un token de sesión válido para crear jugadores."  
 }  
 ```

---

### 2. `DELETE /api/v1/players/{playerId}`

**Path Parameters:**  
 * playerId (integer, requerido): ID único del jugador a eliminar.

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

### 3. `PUT /api/v1/players/{playerId}/name`  
 **Path Parameters:**  
 * playerId (integer, requerido): ID único del jugador a cambiarle el nombre.

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

### 4. `PUT /api/v1/players/{playerId}/number`

**Path Parameters:**  
 * playerId (integer, requerido): ID único del jugador.

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

### 5. `GET /api/v1/players`

Obtener listado general de jugadores registrados por el usuario en la plataforma. Los jugadores se devuelven ordenados por número de camiseta, de menor a mayor.

**Query Parameters:**  
 * page (integer, opcional, por defecto: 1): Número de página para la paginación.  
 * limit (integer, opcional, por defecto: 20, máximo: 100): Cantidad de resultados por página.  
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
 `page` o `limit` no son enteros positivos, o `limit` supera 100:  
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

Crea un nuevo comportamiento personalizado para un jugador. Los comportamientos creados por usuarios no son predeterminados.

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
 "user_id": 1,  
 "behavior_id": 1,  
 "name": "Atacar arco",  
 "description": "El jugador corre hacia adelante y patea la pelota",  
 "code": "...",  
 "is_default": false  
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

Elimina un comportamiento creado por el usuario.

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

#### 409 Conflict

*Caso 1: Comportamiento predeterminado*:  
 ```json  
 {  
 "error": "DEFAULT_BEHAVIOR",  
 "message": "No se puede eliminar un comportamiento predeterminado."  
 }  
 ```

*Caso 2: Comportamiento en uso*:  
 ```json  
 {  
 "error": "BEHAVIOR_IN_USE",  
 "message": "No se puede eliminar el comportamiento porque está asignado en una liga o partido activo."  
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

Obtiene los comportamientos disponibles para el usuario autenticado.

### Query Parameters:

* `is_default` (boolean, opcional): Filtra los comportamientos según su tipo.  
 * Si es `true`, devuelve únicamente los comportamientos predeterminados del sistema.  
 * Si es `false`, devuelve únicamente los comportamientos personalizados creados por el usuario autenticado.  
 * Si no se especifica, devuelve todos los comportamientos disponibles para el usuario autenticado: los predeterminados y sus comportamientos personalizados.  
 * El valor no distingue mayúsculas (`TRUE` también es válido). Cualquier otro valor devuelve 400 `INVALID_QUERY_PARAMS`.

La lista se devuelve con los predeterminados primero y después los personalizados, cada grupo ordenado por `behavior_id`. Nunca incluye comportamientos personalizados de otros usuarios.

### Request Body:

(void)

### Ejemplos:  
 **Obtener únicamente comportamientos predeterminados:**

`GET /api/v1/behaviors?is_default=true`

**Obtener únicamente comportamientos personalizados del usuario:**

`GET /api/v1/behaviors?is_default=false`

**Obtener todos los comportamientos disponibles:**

`GET /api/v1/behaviors`

### Responses:

#### 200 OK (Lista obtenida)

```json  
 [  
 {  
 "behavior_id": 1,  
 "name": "Patear pelota",  
 "description": "El jugador patea la pelota",  
 "code": "...",  
 "is_default": true  
 },  
 {  
 "behavior_id": 15,  
 "name": "Mi comportamiento",  
 "description": "Comportamiento personalizado",  
 "code": "...",  
 "is_default": false  
 }  
 ]  
 ```

#### 200 OK (Lista vacía)  
 ```json  
 [ ]  
 ```

#### 400 Bad Request (Parametros de consulta invalidos)  
 ```json  
 {  
 "error": "INVALID_QUERY_PARAMS",  
 "message": "El parámetro 'is_default' debe ser un booleano válido."  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación"  
 }  
 ```

---

### 4. `GET /api/v1/behaviors/{behaviorId}`

**Path Parameters:**  
 * behaviorId (integer, requerido): ID único del comportamiento a consultar.

Obtiene el detalle de un comportamiento disponible para el usuario autenticado.

### Request Body:  
 (void)

### Responses:

#### 200 OK (Comportamiento obtenido)

```json  
 {  
 "behavior_id": 1,  
 "name": "Patear pelota",  
 "description": "El jugador patea la pelota",  
 "code": "...",  
 "is_default": true  
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
 "message": "No tienes permisos de ver el comportamiento"  
 }  
 ```

#### 404 Not Found

```json  
 {  
 "error": "BEHAVIOR_NOT_FOUND",  
 "message": "No existe un comportamiento con el ID especificado."  
 }  
 ```

<!-- LIGAS -->

### 1. `POST /api/v1/leagues`

Crea una liga. Si `password` es `null` (o no se envía) la liga es pública; si es un string, es privada (`is_private: true`).

**Evento publicado en el Event Bus (WS global):** `LEAGUE_CREATED`.

### Request Body:  
 ```json  
 {  
 "league_name": "Liga FAMAF Apertura",  
 "match_duration": 5,  
 "max_teams": 8,  
 "password": null,  
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
 "league_name": "Liga FAMAF Apertura",  
 "status": "PENDING",  
 "is_private" : false,  
 "match_duration": 5,  
 "max_teams": 8,  
 "current_teams_count": 1,  
 "host_team_id": 1  
 }  
 ```

#### 400 Bad Request

*Caso 1: Duración del partido inválida*:  
 ```json  
 {  
 "error": "INVALID_SETTINGS",  
 "message": "La duración del partido debe ser de 2 o 5 minutos."  
 }  
 ```

*Caso 2: Formación o alineación inválida*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "La formación o la alineación seleccionados no es válida."  
 }  
 ```

*Caso 3: Jugadores que no pertenecen al equipo*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los jugadores seleccionados no pertenecen al equipo del usuario autenticado"  
 }  
 ```

*Caso 4: Comportamientos no válidos para el usuario*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los comportamientos seleccionados no son válidos para el usuario autenticado"  
 }  
 ```

*Caso 5: Cupo inválido*:  
 ```json  
 {  
 "error": "INVALID_LEAGUE_CAPACITY",  
 "message": "El mínimo para jugar debe ser de al menos 3 equipos"  
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
 "message": "Necesitas al menos 6 jugadores para crear una liga"  
 }  
 ```  
 ---

### 2. `DELETE /api/v1/leagues/{leagueId}`

**Path Parameters:**  
 * leagueId (integer, requerido): ID único de la liga a eliminar.

Permite al anfitrión cancelar y eliminar por completo una liga no iniciada, junto con todas sus inscripciones asociadas.

**Evento publicado en el Event Bus (WS global):** `LEAGUE_CANCELLED`.

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

**Evento publicado en el Event Bus (WS global):** `LEAVE_LOBBY_LEAGUE`.

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
 "league_name": "Liga FAMAF Apertura",  
 "status": "IN_PROGRESS",  
 "is_private": false,  
 "host_team_id": 1,  
 "max_teams": 8,  
 "current_teams_count": 2,  
 "match_duration": 5,  
 "teams": [  
 { "team_id": 1, "team_name": "alfajoresFC" },  
 { "team_id": 8, "team_name": "BocaJuniors" }  
 ],  
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
 "message": "No existe la liga con el ID especificado."  
 }  
 ```

---

### 5. `GET /api/v1/leagues`  
 Ver lista de ligas disponibles.

**Query Parameters:**  
 * page (integer, opcional, por defecto: 1): Número de página.  
 * limit (integer, opcional, por defecto: 20): Cantidad de resultados.  
 * status (string, opcional): Filtrar por estado PENDING.

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
 "current_teams_count": 5,  
 "status": "PENDING",  
 "is_private": false  
 },  
 {  
 "league_id": 2,  
 "league_name": "TheParrilleros",  
 "max_teams": 7,  
 "current_teams_count": 3,  
 "status": "PENDING",  
 "is_private": true  
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
 ### 6. POST /api/v1/leagues/{leagueId}/verify-password

**Path Parameters:**  
 * leagueId (integer, requerido): ID único de la liga a la que se desea verificar la contraseña.

Verifica la contraseña de liga privada al intentar unirse.

### Request Body:  
 ```json  
 {  
 "password": "Asado123"  
 }  
 ```

### Responses:

#### 200 OK  
 ```json  
 {  
 "join_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",  
 "expires_in_seconds": 300  
 }  
 ```

#### 400 Bad Request  
 ```json  
 {  
 "error": "LEAGUE_NOT_PRIVATE",  
 "message": "La liga qué se está intentando verificar no es privada"  
 }  
 ```

#### 401 Unauthorized

*Caso 1: Contraseña incorrecta*:  
 ```json  
 {  
 "error": "PASSWORD_INCORRECT",  
 "message": "La contraseña ingresada no es correcta"  
 }  
 ```

*Caso 2: Sin sesión o token vencido*:  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para verificar la contraseña de la liga."  
 }  
 ```

#### 404 Not Found  
 ```json  
 {  
 "error": "NOT_FOUND",  
 "message": "La liga no fue encontrada"  
 }  
 ```

---  
 ### 7. `POST /api/v1/leagues/{leagueId}/teams`

**Path Parameters:**  
 * leagueId (integer, requerido): ID único de la liga a la que se desea unir.

Unirse a una liga.

**Evento publicado en el Event Bus (WS global):** `TEAM_JOINED_LEAGUE`.

### Request Body:

*Caso 1: (Liga Privada) - manda el join token en vez de la contraseña*  
 ```json  
 {  
 "team_id": 42,  
 "join_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",  
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

*Caso 2: (Liga Pública) no manda ni el join ni la contraseña*  
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

*Caso 2: Liga llena*:  
 ```json  
 {  
 "error": "LEAGUE_FULL",  
 "message": "La liga ha alcanzado la cantidad máxima de equipos permitidos."  
 }  
 ```

*Caso 3: Formación o alineación inválida*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "La formación o la alineación seleccionados no es válida."  
 }  
 ```

*Caso 4: Jugadores que no pertenecen al equipo*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los jugadores seleccionados no pertenecen al equipo del usuario autenticado"  
 }  
 ```

*Caso 5: Comportamientos no válidos para el usuario*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los comportamientos seleccionados no son válidos para el usuario autenticado"  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para unirse a la liga."  
 }  
 ```

#### 403 Forbidden

*Caso 1: Token de acceso vencido*:  
 ```json  
 {  
 "error": "JOIN_TOKEN_EXPIRED",  
 "message": "La verificación de la contraseña expiró. Volvé a ingresarla."  
 }  
 ```

*Caso 2: Token de acceso inválido*:  
 ```json  
 {  
 "error": "INVALID_JOIN_TOKEN",  
 "message": "El token de acceso no es válido para esta liga."  
 }  
 ```

*Caso 3: Liga privada sin token de acceso*:  
 ```json  
 {  
 "error": "JOIN_TOKEN_REQUIRED",  
 "message": "Esta liga es privada y requiere verificar la contraseña."  
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
 ### 8. `PUT /api/v1/leagues/{leagueId}`

**Path Parameters:**  
 * leagueId (integer, requerido): ID único de la liga a modificar.

Iniciar una liga.

**Evento publicado en el Event Bus (WS global):** `LEAGUE_INITIATED`.

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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para iniciar la liga."  
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

### 1. `POST /api/v1/friendly`

Crea un partido amistoso. El equipo que lo crea es el anfitrión (juega de local) y el partido queda en estado `PENDING` hasta que se une un rival. La alineación sigue las reglas de *Configuración táctica*.

**Evento publicado en el Event Bus (WS global):** `FRIENDLY_CREATED`.

### Request Body:  
 ```json  
 {  
 "team_id": 1,  
 "match_duration": 5,  
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
 "team_host_id": 1,  
 "status": "PENDING",  
 "match_duration": 5  
 }  
```

#### 400 Bad Request

*Caso 1: Duración del partido inválida*:

```json

{

  "error": "INVALID_SETTINGS",

  "message": "La duración del partido debe ser de 2 o 5 minutos."

}

```

*Caso 2: Formación o alineación inválida*:

```json

{

  "error": "INVALID_CONFIGURATION",

  "message": "La formación o la alineación seleccionados no es válida."

}

```

*Caso 3: Jugadores que no pertenecen al equipo*:

```json

{

  "error": "INVALID_CONFIGURATION",

  "message": "Los jugadores seleccionados no pertenecen al equipo del usuario autenticado"

}

```

*Caso 4: Comportamientos no válidos para el usuario*:

```json

{

  "error": "INVALID_CONFIGURATION",

  "message": "Los comportamientos seleccionados no son válidos para el usuario autenticado"

}

```

#### 401 Unauthorized (Sin token válido)

```json

{

  "error": "UNAUTHORIZED",

  "message": "El token de sesión no es válido o ha expirado"

}

```

#### 403 Forbidden

```json

{

  "error": "UNAUTHORIZED_ACTION",

  "message": "El equipo no pertenece al usuario autenticado."

}

```

#### 422 Unprocessable Entity

*Caso 1: Equipo sin el mínimo de jugadores*:

```json

{

  "error": "PRECONDITION_FAILED",

  "message": "El equipo no cumple con el mínimo de jugadores para jugar un partido"

}

```

*Caso 2: Datos enviados inválidos*:

```json

{

  "error": "VALIDATION_ERROR",

  "message": "Los datos enviados no son válidos."

}

```

---

### 2. `GET /api/v1/friendly`

Lista los partidos amistosos disponibles, del más nuevo al más viejo (por `match_id` descendente).

**Query Parameters:**  
 * page (integer, opcional, por defecto: 1, mínimo: 1): Número de página.  
 * limit (integer, opcional, por defecto: 20, mínimo: 1, máximo: 100): Cantidad de resultados.  
 * status (string, opcional, por defecto: `PENDING`): Filtrar por estado. Valores posibles: `PENDING`, `IN_PROGRESS`, `FINISHED`.

Si algún query parameter es inválido (por ejemplo `page=0`, `limit=500` o un `status` que no existe), se devuelve 422 `VALIDATION_ERROR`.  
 ### Request Body:  
 (void)

### Responses:

#### 200 OK  
 ```json  
 {  
 "matches": [  
 {  
 "match_id": 6,  
 "team_host_id": 12,  
 "team_away_id": null,  
 "status": "PENDING",  
 "match_duration": 2  
 },  
 {  
 "match_id": 5,  
 "team_host_id": 5,  
 "team_away_id": null,  
 "status": "PENDING",  
 "match_duration": 5  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para ver la lista de amistosos."  
 }  
 ```

#### 422 Unprocessable Entity (Query parameters inválidos)  
 ```json  
 {  
 "error": "VALIDATION_ERROR",  
 "message": "Los datos enviados no son válidos."  
 }  
 ```

---

### 3. `GET /api/v1/friendly/{matchId}`

**Path Parameters:**

* matchId (integer, requerido): ID único del partido amistoso.

Obtiene el detalle de un partido amistoso, con los datos de ambos equipos. Lo usa la sala de espera (lobby) para mostrar el estado inicial del partido pues este todavía no esta iniciado. Devuelve el partido en cualquier estado (`PENDING`, `IN_PROGRESS` o `FINISHED`), así que también sirve para obtener los nombres y logos de los equipos en la pantalla del partido. `team_host` es el equipo local.

### Request Body:

(void)

### Responses:

#### 200 OK (Amistoso a la espera de un rival)

```json

{

 "match_id": 5,

 "status": "PENDING",

 "match_duration": 5,

 "team_host": {

     "team_id": 1,

     "team_name": "alfajoresFC",

     "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."

 },

 "team_away": null

}

```

#### 200 OK (Amistoso con rival)

```json

{

 "match_id": 5,

 "status": "IN_PROGRESS",

 "match_duration": 5,

 "team_host": {

     "team_id": 1,

     "team_name": "alfajoresFC",

     "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."

 },

 "team_away": {

     "team_id": 8,

     "team_name": "BocaJuniors",

     "team_logo": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."

 }

}

```

#### 401 Unauthorized

```json

{

 "error": "UNAUTHORIZED",

 "message": "Se requiere autenticación para ver el amistoso."

}

```

#### 404 Not Found

```json

{

 "error": "MATCH_NOT_FOUND",

 "message": "El partido solicitado no existe o fue cancelado por el anfitrión."

}

```

---

### 4. `DELETE /api/v1/friendly/{matchId}`

**Path Parameters:**  
 * matchId (integer, requerido): ID único del partido a eliminar.

El anfitrión cancela y elimina un partido amistoso específico que aún no ha comenzado.

**Evento publicado en el Event Bus (WS global):** `FRIENDLY_CANCELLED`.

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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para cancelar el amistoso."  
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

### 5. `GET /api/v1/matches/{matchId}/result`  
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
 "team_name": "alfajoresFC",  
 "goals": 3  
 },  
 "away_team": {  
 "team_id": 8,  
 "team_name": "BocaJuniors",  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para ver el resultado del partido."  
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

### 6. `POST /api/v1/friendly/{matchId}/teams`

**Path Parameters:**  
 * matchId (integer, requerido): ID único del partido amistoso al que se desea unir.

Permite a un usuario inscribir a su equipo en un partido amistoso en espera. La alineación sigue las reglas de *Configuración táctica*. Al unirse el rival, el partido pasa a `IN_PROGRESS` y comienza automáticamente; el rival juega de visitante.

Si dos equipos intentan unirse al mismo amistoso al mismo tiempo, solo uno lo logra: el otro recibe 409 `MATCH_UNAVAILABLE`.

**Evento publicado en el Event Bus (WS global):** `GUEST_JOINED_FRIENDLY` y, a continuación, `MATCH_STARTED`.

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
 "team_host_id": 5,  
 "team_away_id": 8,  
 "status": "IN_PROGRESS",  
 "match_duration": 5  
 }  
 ```

#### 400 Bad Request

*Caso 1: Formación o alineación inválida*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "La formación o la alineación seleccionados no es válida."  
 }  
 ```

*Caso 2: Jugadores que no pertenecen al equipo*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los jugadores seleccionados no pertenecen al equipo del usuario autenticado"  
 }  
 ```

*Caso 3: Comportamientos no válidos para el usuario*:  
 ```json  
 {  
 "error": "INVALID_CONFIGURATION",  
 "message": "Los comportamientos seleccionados no son válidos para el usuario autenticado"  
 }  
 ```

*Caso 4: El equipo intenta unirse a su propio amistoso*:  
 ```json  
 {  
 "error": "INVALID_SETTINGS",  
 "message": "El equipo no puede unirse a su propio partido."  
 }  
 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para unirse al amistoso."  
 }  
 ```

#### 403 Forbidden  
 ```json  
 {  
 "error": "UNAUTHORIZED_ACTION",  
 "message": "El equipo no pertenece al usuario autenticado."  
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
 "message": "El equipo no tiene el mínimo de jugadores creados para poder participar del partido"  
 }  
 ```

---

### 7. `GET /api/v1/matches/{matchId}`   
 **Path Parameters:**  
 * matchId (integer, requerido): ID único del partido.

Ver los detalles de un partido amistoso en curso.

### Request Body:  
 (void)

### Responses:

#### 200 OK  
 ```json  
 {  
 "match_id": 3,  
 "status": "IN_PROGRESS",  
 "team_host_id": 1,  
 "team_away_id": 2,  
 "match_duration": 5  
 }

 ```

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para ver detalles del amistoso"  
 }  
 ```

#### 404 Not Found  
 ```json  
 {  
 "error": "MATCH_NOT_FOUND",  
 "message": "No existe el amistoso con el ID especificado."  
 }  
 ```

---

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
 **Nota:** Los endpoints bajo `**/api/v1/matches/{matchId}**` aplican indistintamente a partidos amistosos y de liga. El match_id se obtiene de la respuesta de POST **/friendly** (amistosos) o del fixture devuelto por **GET /leagues/{leagueId}** (ligas), y a partir de ahí es el único identificador necesario.

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

### 2. `PUT /api/v1/matches/{matchId}/players/{playerId}/behavior`

**Path Parameters:**  
 * matchId (integer, requerido): ID único del partido.  
 * playerId (integer, requerido): ID único del jugador.

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
 #### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para cambiar el comportamiento."  
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

Obtiene el estado actual del partido, incluyendo estadísticas los jugadores que se encuentran actualmente en cancha. El backend filtrará los `behavior_id` de los rivales según el usuario que realice la petición.

### Request Body:  
 (void)

### Responses:

#### 200 OK  
 ```json  
 {  
 "match_id": 12,  
 "current_tick": 450,  
 "total_ticks": 600,  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para consultar el estado del partido."  
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

#### 401 Unauthorized  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "Se requiere autenticación para conectarse a los eventos del sistema."  
 }  
 ```

## 2. WS /ws/v1/global

Canal unidireccional (Servidor → Cliente) alimentado por el Event Bus para enviar eventos del sistema, notificaciones de lobbies y actualizaciones de partidos/ligas. Todos los usuarios conectados reciben todos los eventos (no se filtran por usuario): cada cliente decide cuáles le interesan, por ejemplo comparando el `match_id`.  
 **Query Parameters:**

* token (string, requerido): Token temporal obtenido mediante *POST /api/v1/live-token/global*.

Evento Servidor → Cliente (Estructura Genérica):

```json  
 {  
 "event": "NOMBRE_DEL_EVENTO",  
 "data": {}  
 }  
 ```

### Catálogo de Eventos Globales:

* **FRIENDLY_CREATED:** Un usuario anfitrión creó un partido amistoso. *Lo publica: `POST /api/v1/friendly`.*  
 ```json  
 {  
 "event": "FRIENDLY_CREATED",  
 "data": { "match_id": 12, "team_host_id": 5, "match_duration": 5, "status": "PENDING"}  
 }  
 ```  
 * **FRIENDLY_CANCELLED:** El usuario anfitrión eliminó el partido amistoso. *Lo publica: `DELETE /api/v1/friendly/{matchId}`.*  
 ```json  
 {  
 "event": "FRIENDLY_CANCELLED",  
 "data": { "match_id": 12 }  
 }  
 ```

* **GUEST_JOINED_FRIENDLY:** Un rival se unió al amistoso. *Lo publica: `POST /api/v1/friendly/{matchId}/teams`.*  
 ```json  
 {  
 "event": "GUEST_JOINED_FRIENDLY",  
 "data": { "match_id": 12, "team_host_id": 5, "team_away_id": 8}  
 }  
 ```

* **LEAGUE_CREATED:** Se creó una nueva liga. *Lo publica: `POST /api/v1/leagues`.*  
 ```json  
 {  
 "event": "LEAGUE_CREATED",  
 "data": { "league_id": 3, "league_name": "Copa FAMAF", "max_teams": 8, "status": "PENDING"}  
 }  
 ```

* **LEAGUE_INITIATED:** Se inició una liga. *Lo publica: `PUT /api/v1/leagues/{leagueId}`.*  
 ```json  
 {  
 "event": "LEAGUE_INITIATED",  
 "data": { "league_id": 3, "league_name": "Copa FAMAF", "max_teams": 8, "status": "IN_PROGRESS"}  
 }  
 ```

* **LEAGUE_CANCELLED:** El creador eliminó la liga. *Lo publica: `DELETE /api/v1/leagues/{leagueId}`.*  
 ```json  
 {  
 "event": "LEAGUE_CANCELLED",  
 "data": { "league_id": 3 }  
 }  
 ```

* **TEAM_JOINED_LEAGUE:** Un equipo se inscribió en la liga. *Lo publica: `POST /api/v1/leagues/{leagueId}/teams`.*  
 ```json  
 {  
 "event": "TEAM_JOINED_LEAGUE",  
 "data": { "league_id": 3, "team_id": 14, "current_teams_count": 6, "max_teams": 8 }  
 }  
 ```

* **MATCH_STARTED:** Notifica el inicio de un partido. *Lo publica: `POST /api/v1/friendly/{matchId}/teams` (amistosos) y el motor al iniciar cada partido de liga.*  
 ```json  
 {  
 "event": "MATCH_STARTED",  
 "data": { "match_id": 12, "team_host_id": 5, "team_away_id": 8, "status": "IN_PROGRESS"}  
 }  
 ```

* **MATCH_FINISHED:** Notifica el final de un partido. *Lo publica: el motor de simulación.*  
 ```json  
 {  
 "event": "MATCH_FINISHED",  
 "data": { "match_id": 12, "status": "FINISHED"}  
 }  
 ```  
 * **LEAGUE_MATCH_FINISHED:** Notifica la finalización de un partido del fixture de la liga para refrescar la tabla de posiciones. *Lo publica: el motor de simulación.*  
 ```json  
 {  
 "event": "LEAGUE_MATCH_FINISHED",  
 "data": { "league_id": 3, "match_id": 12, "score": { "home": 2, "away": 1 }, "status": "FINISHED"}  
 }  
 ```

* **LEAGUE_FINISHED:** Notifica el final de una liga. *Lo publica: el motor de simulación.*  
 ```json  
 {  
 "event": "LEAGUE_FINISHED",  
 "data": { "league_id": 3, "status": "FINISHED"}  
 }  
 ```

* **LEAVE_LOBBY_LEAGUE:** Un equipo no anfitrion abandona una liga estando en la sala de espera. *Lo publica: `DELETE /api/v1/leagues/{leagueId}/teams/{teamId}`.*  
 ```json  
 {  
 "event": "LEAVE_LOBBY_LEAGUE",  
 "data": { "league_id": 3, "team_id": 14, "current_teams_count": 5, "max_teams": 8, "status": "PENDING"}  
 }  
 ```

### 3. POST /api/v1/matches/{matchId}/live-token

Solicita autorización y obtiene el token de acceso/URI para conectarse a la transmisión en vivo del partido mediante WebSocket.

El token devuelto es del mismo tipo que el de POST /api/v1/live-token/global: no está atado a un partido. La autorización (partido en curso y usuario participante) se valida en este endpoint y otra vez al abrir la conexión WebSocket.

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
expires_in_seconds es la ventana para abrir la conexión. Una vez establecida, la conexión no se corta cuando el token vence. Para reconectar hay que pedir un token nuevo.

#### 401 Unauthorized:  
 ```json  
 {  
 "error": "UNAUTHORIZED",  
 "message": "El token de sesión no es válido o ha expirado"  
 }  
 ```
### 403 Unauthorized Action:
{
  "error": "UNAUTHORIZED_ACTION",
  "message": "No se permite conectarse a partidos en los que no participas"
}

### 404 Not Found:  
 ```json  
 {  
 "error": "MATCH_NOT_FOUND",  
 "message": "El partido solicitado no existe o no se encuentra en ejecución."  
 }  
 ```
se devuelve también si el partido está PENDING o FINISHED


### 4. WS /ws/v1/matches/{matchId}

Canal unidireccional (Servidor → Cliente) en tiempo real para la transmisión tick a tick del partido.

**Path Parameters:**

* matchId (integer, requerido): ID único del partido en curso.

**Query Parameters:**

* token (string, requerido): Token temporal obtenido a través de POST /api/v1/matches/{matchId}/live-token. Es del mismo tipo que el del canal global y no va atado al partido*.

Evento Servidor → Cliente (Transmisión por Tick)  
 ```json  
 {  
 "event": "TICK_UPDATE",  
 "match_id": 12,  
 "current_tick": 45,  
 "total_ticks": 600,  
 "score": { "home": 1, "away": 0 },  
 "ball": { "x": 52.4, "y": 30.1 },  
 "players": [  
 {  
 "player_id": 101,  
 "team_id": 5,  
 "position": { "x": 50.0, "y": 28.5 },  
 "current_action": "run_to"  
 }  
 ]  
 }  
 ```

**`current_action`:** primitiva de acción que el jugador está ejecutando en ese tick (ver *PlayerAPI* en Primitivas de Comportamientos). Valores posibles: `run_to`, `kick_ball`, `idle`. Si el comportamiento falla en un tick, el motor le asigna `idle`.

**Ritmo y contenido de los ticks:**

* Se envía un `TICK_UPDATE` cada 0,5 segundos, después de procesar cada tick (ver *Duración de los partidos*). El primer evento llega con `current_tick: 1` y el último con `current_tick` igual a `total_ticks`.  
 * `ball` y `players[].position` usan el sistema de coordenadas de la cancha (ver *Cancha y sistema de coordenadas*).  
 * `players` incluye solo a los jugadores en cancha (3 por equipo); los suplentes no se envían.  
 * En `score`, `home` son los goles del anfitrión y `away` los del visitante.  
 * El evento no trae nombres ni logos de los equipos: el cliente los obtiene una sola vez con `GET /api/v1/friendly/{matchId}`.

### Diferencias:  
 * **Event Bus Global:** Notificaciones y eventos de control (baja frecuencia).

* **Canal de Partido:** Telemetría del simulador (alta frecuencia / baja latencia).

### Conexiones: 

* un mismo usuario puede tener varias conexiones simultáneas al mismo partido (por ejemplo, varias pestañas). El token solo se valida al abrir la conexión. Es un canal unidireccional: los mensajes que envíe el cliente se ignoran.

> Aclaración: El evento Cliente → Servidor que conectaría el canal para, por ejemplo: cambios de comportamientos, se omite pues ya hay un endpoint HTTP que realiza el intercambio de comportamientos.

Rechazo de la conexión: el servidor rechaza el handshake (HTTP 403, cierre con código 1008) si falta el token, es inválido o está vencido; si el partido no existe o no está IN_PROGRESS; o si el usuario no participa en el partido o ya no existe. El navegador no expone la causa, solo ve que la conexión falló. El cliente debe pedir un nuevo live-token y, si persiste, mostrar el error.

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

 def magnitude(self) -> float:  
 """  
 @brief Devuelve la rapidez o fuerza del vector (su longitud/escalar).  
 Útil para calcular la velocidad independientemente de su dirección  
 """  
 def normalize(self) -> 'Vector2D':  
 """  
 @brief Devuelve un vector de magnitud 1 en la misma dirección (vector unitario).  
 Útil para calcular hacia donde va un objetivo  
 Si el vector es (0, 0), devuelve Vector2D(0, 0) en lugar de lanzar un error.  
 """  
 ```

### Point2D

Representa un **punto espacial o posición fija** en el plano de la cancha:

```python  
 @dataclass(frozen=True)  
 class Point2D:  
 x: float  
 y: float
 
 def distance_to(self, other: 'Point2D') -> float:  
 """  
 @brief Calcula la distancia hacia otro Point2D.  
 @param other Point2D a calcular la distancia.  
 """  
 ...

def direction_to(self, target: 'Point2D') -> Vector2D:  
 """  
 @brief Retorna el Vector2D de dirección (de magnitud 1) que apunta hacia 'target'.  
 Si 'target' es el mismo punto, devuelve Vector2D(0, 0).  
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
 ball_position: Point2D # Posición en cancha de la pelota  
 ball_velocity: Vector2D # Velocidad inmediata de la pelota  
 my_position: Point2D # Posición en cancha del jugador  
 teammates: list[Point2D] # Posiciones de los compañeros del jugador  
 opponents: list[Point2D] # Posiciones de los rivales del jugador  
 team_goal: Point2D # Posición del arco aliado  
 opponent_goal: Point2D # Posición del arco rival  
 ```

Aclaraciones sobre `GameState`:

* Todas las posiciones usan el sistema de coordenadas de la cancha (ver *Cancha y sistema de coordenadas* en el contrato de API).  
 * `teammates` y `opponents` contienen solo las posiciones de los jugadores en cancha, sin identificar a quién pertenece cada una; `teammates` no incluye al propio jugador. Los suplentes no aparecen.  
 * `team_goal` y `opponent_goal` son el punto central de cada arco: `(0, 30)` y `(105, 30)`, según el lado del equipo.  
 * `ball_velocity` está en metros por segundo.

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
 @return Entero de solo lectura, cargado por el servidor al iniciar el partido.  
 """  
 ...

def get_reach_distance(self) -> float:  
 """  
 @brief Devuelve la distancia máxima de interacción (en metros/unidades)  
 calculada dinámicamente por el servidor según el atributo CONTROL:  
 reach_distance = 0.05625 * CONTROL - 0.625  
 (0.5 con CONTROL 20 y 5.0 con CONTROL 100).  
 """  
 ...

# --- PRIMITIVAS DE ACCIÓN (COMANDOS / INTENCIONES) ---  
 # Informa al servidor sobre la intención que tiene el usuario en ese tick

def run_to(self, target: Point2D, speed_percentage: float) -> None:  
 """  
 @brief Registra la intención de desplazarse hacia una posición objetivo.  
 @param target Posición geográfica Point2D, con coordenadas finitas. Si está fuera  
 de la cancha, el jugador se detiene en el borde.  
 @param speed_percentage Porcentaje de velocidad deseado (0.0 a 1.0). Obligatorio.  
 Velocidad máxima (m/s) = 8 * SPEED / 100, así que en un tick (0,5 s) el jugador  
 avanza como máximo 4 * SPEED / 100 * speed_percentage metros.  
 @raises ValueError si target no es un Point2D finito o speed_percentage no está en [0.0, 1.0].  
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

### 3.1 Reglas de resolución de las intenciones

* Las primitivas de acción solo registran una intención; el servidor las aplica después de ejecutar el `on_tick` de todos los jugadores, todas sobre la misma foto del estado del tick (ningún jugador ve el movimiento de otro en el mismo tick).  
 * Si `on_tick` llama a varias primitivas de acción en el mismo tick, vale la última. Si no llama a ninguna, la intención es `idle()`.  
 * Si una primitiva recibe argumentos inválidos, lanza `ValueError` y la intención de ese jugador queda en `idle()` para ese tick, aunque el comportamiento atrape la excepción (salvo que después llame a otra primitiva válida).  
 * Hay gol cuando la pelota llega a una línea de fondo entre los dos postes del arco.

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



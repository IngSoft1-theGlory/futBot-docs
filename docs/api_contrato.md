# API - CONTRATO

### 1. `POST /api/v1/auth/register`

Registra un nuevo usuario en la plataforma.

### Request Body:

```json
{ 
	"username": "Maradona10", 
	"email": "diego@famaf.unc.edu.ar", 
	"password": "Password123!", 
}
```

### Responses:

#### 201 Created

```json
{
  "user_id": 1,
  "username": "Maradona10",
  "email": "diego@famaf.unc.edu.ar",
}
```

#### 400 Bad Request (Datos de entrada inválidos o duplicados)

*Caso 1: Email existente*:

```json
{
	"error" : "DUPLICATE_EMAIL",
	"message" : "El email ingresado se encuentra en uso. Intentar de nuevo"
}
```

#### 500 Internal Server Error (Error no controlado por el servidor)

```json
{
	"error" : "INTERNAL_SERVER_ERROR",
	"message" : "Ocurrió un error inesperado al procesar el registro"
}
```
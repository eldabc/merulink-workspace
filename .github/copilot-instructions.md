# API Response Standards (Backend)

Todos los endpoints del backend deben usar el helper `ApiResponseHelper::createResponse()` para estandarizar las respuestas JSON.

## Estructura de respuesta

```json
{
    "status":    "ok | fail",
    "code":      "codigo_interno_de_la_operacion",
    "message":   "Mensaje legible para el usuario",
    "data":      { ... },
    "timestamp": "2026-07-23T..."
}
```

### Campos

| Campo      | Tipo   | Requerido | Descripción |
|-----------|--------|-----------|-------------|
| `status`  | string | sí        | `"ok"` o `"fail"` |
| `code`    | string | sí        | Código interno que identifica la operación (ej: `created_role`, `updated_user`) |
| `message` | string | sí        | Mensaje legible para el usuario final |
| `data`    | mixed  | no        | Datos adicionales de la respuesta (objeto, array, etc.) |
| `timestamp` | string | sí      | Fecha/hora ISO 8601 generada automáticamente |

## Cómo usarlo

Importar el helper:

```php
use App\Helpers\ApiResponseHelper;
```

Llamar el método estático:

```php
return ApiResponseHelper::createResponse(
    'ok',                          // status
    'created_role',                // code
    'Rol creado exitosamente',     // message
    $optionalData,                 // data (opcional, null por defecto)
    200                            // httpCode (opcional, 200 por defecto)
);
```

### Ejemplos

**Respuesta exitosa sin datos:**

```php
return ApiResponseHelper::createResponse(
    'ok',
    'created_role',
    'Rol creado exitosamente'
);
```

**Respuesta exitosa con datos:**

```php
return ApiResponseHelper::createResponse(
    'ok',
    'user_found',
    'Usuario encontrado',
    ['id' => 1, 'email' => 'user@example.com']
);
```

**Respuesta de error:**

```php
return ApiResponseHelper::createResponse(
    'fail',
    'user_not_found',
    'No se encontró el usuario solicitado',
    null,
    404
);
```

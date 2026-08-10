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

---

# Estándar de Inputs (Frontend)

Todos los inputs de texto del frontend (`merulink-front`) deben usar la clase `input-dark` como estilo base.

## Regla

- Todo **nuevo** `<input>` de texto (`type="text"`, `type="date"`, `type="password"`, `type="number"`, etc.) debe incluir la clase `input-dark` en su `className`.
- La clase `input-dark` está definida en `src/index.css` (dentro de `@layer components`) y normaliza el estilo oscuro: fondo, borde, texto, placeholder y focus.
- Si un input necesita estilos extra (ancho, centrado, etc.), se agregan como **clases adicionales junto a** `input-dark`, nunca en reemplazo.
- No se deben usar estilos inline ni repetir las utilidades de `input-dark` manualmente en componentes nuevos.

```jsx
{/* Correcto */}
<input type="text" className="input-dark" />
<input type="text" className="input-dark w-40 text-center" />

{/* Incorrecto: repetir las utilidades a mano */}
<input type="text" className="w-full px-3 py-2.5 rounded-lg bg-[#252729] ..." />
```

---

# Estándar de Botones (Frontend)

Los botones del frontend (`merulink-front`) tienen un estilo base global definido en `src/index.css`. Ese estilo es la base; no se deben añadir clases tailwind que sobreescriban este comportamiento, ni redefinir las propiedades base en cada botón nuevo.

## Regla

- La regla global `button:not(.skip-style-btn)` (y `.generic-btn`) en `src/index.css` ya define el estilo base: radio, borde, padding, fuente, fondo, cursor y hover/focus.
- Todo **nuevo** `<button>` debe dejarse con ese estilo base por defecto. Solo se agregan clases extra (utilidades Tailwind) **cuando sean necesarias** para algo puntual (colores, ancho, estado, etc.), y nunca para repetir lo que ya cubre el base.
- No se debe usar la clase `skip-style-btn` a menos que el botón necesite desactivar intencionalmente el estilo base.
- No usar estilos inline para estilizar botones nuevos.

```jsx
{/* Correcto: botón con estilo base */}
<button type="button" onClick={...}>Guardar</button>

{/* Correcto: se agrega solo lo extra necesario */}
<button type="button" className="flex items-center gap-2" onClick={...}>
  <Search className="w-4 h-4" /> Buscar
</button>

{/* Incorrecto: redefinir el estilo base a mano con muchas utilidades */}
<button type="button" className="bg-[#1a1a1a] rounded-lg px-3 py-2 ..." />
```

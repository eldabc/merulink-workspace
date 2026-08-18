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

---

# Estándar de Labels (Frontend)

Todos los labels de formulario del frontend (`merulink-front`) deben usar el componente `LabelFieldForm` como estilo por defecto.

## Regla

- Todo **nuevo** label de un formulario debe usar el componente `LabelFieldForm` (ubicado en `src/components/Shared/LabelFieldForm.jsx`).
- El componente ya incluye el estilo y la estructura base (texto, tamaño, color, margen y marca de campo requerido).
- Props:
  - `field`: texto del label (requerido).
  - `simbol`: símbolo que se muestra en rojo para campos obligatorios (ej: `"*"`). Omitir si el campo no es obligatorio.
  - `dinamicClasses`: clases adicionales opcionales para **modificar el estilo base** si el componente lo necesita (por ejemplo, en un modal la letra debe verse más pequeña → pasar una clase como `text-sm`).
- No se deben escribir `<label>` con utilidades a mano ni repetir el estilo del componente en componentes nuevos.

```jsx
import LabelFieldForm from '../Shared/LabelFieldForm';

{/* Correcto: campo obligatorio */}
<LabelFieldForm field="Nombre" simbol="*" />

{/* Correcto: campo opcional (sin símbolo) */}
<LabelFieldForm field="Dirección" />

{/* Correcto: con clases extra si se necesita algo puntual (ej: letra más pequeña en un modal) */}
<LabelFieldForm field="Cédula" simbol="*" dinamicClasses="text-sm" />

{/* Incorrecto: reescribir el estilo del label a mano */}
<label className="block text-lg font-medium text-gray-300 mt-1">Nombre: <span className="text-red-400">*</span></label>
```

---

# Estándar de Errores de Formulario (Frontend)

Todos los mensajes de error de los formularios del frontend (`merulink-front`) deben mostrarse con el componente `ErrorMessage` como estilo por defecto.

## Regla

- Todo **nuevo** mensaje de error de validación debe usar el componente `ErrorMessage` (ubicado en `src/components/Shared/ErrorMessage.jsx`).
- El componente ya incluye el estilo base (texto rojo pequeño con margen superior) y recibe una única prop:
  - `msg`: texto del error a mostrar (requerido).
- Se usa normalmente junto a `react-hook-form` para mostrar `errors.<campo>.message`.
- No se deben escribir `<p>` de error a mano repitiendo el estilo ni usar estilos inline.

```jsx
import ErrorMessage from '../Shared/ErrorMessage';

{/* Correcto: mostrar error de validación de react-hook-form */}
{errors.email && <ErrorMessage msg={errors.email.message} />}

{/* Correcto: error condicional con mensaje directo */}
{errorMsg && <ErrorMessage msg={errorMsg} />}

{/* Incorrecto: escribir el error a mano */}
{errors.email && <p className="text-red-400 text-xs mt-1">{errors.email.message}</p>}
```

---

# Estándar de Botón Cancelar en Modales (Frontend)

Los botones para cancelar/cerrar **modales** del frontend (`merulink-front`) deben usar el componente `ButtonCancel` como estilo por defecto.

## Regla

- Todo **nuevo** botón de cancelar **dentro de un modal** debe usar el componente `ButtonCancel` (ubicado en `src/components/Shared/ButtonCancel.jsx`).
- El componente ya incluye el estilo base (texto gris, hover a blanco, padding y tamaño de fuente).
- Props:
  - `onClose`: función que cierra el modal (requerida).
  - `text`: texto del botón (opcional, por defecto `"Cancelar"`).
- No se deben escribir `<button>` de cancelar a mano dentro de un modal repitiendo el estilo ni usar estilos inline.
- Esta regla aplica **solo a modales**; los botones de cancelar de formularios/páginas siguen el estándar general de botones.

```jsx
import ButtonCancel from '../Shared/ButtonCancel';

{/* Correcto: botón de cancelar por defecto */}
<ButtonCancel onClose={onClose} />

{/* Correcto: con texto personalizado */}
<ButtonCancel onClose={onClose} text="Volver" />

{/* Incorrecto: escribir el botón de cancelar a mano */}
<button onClick={onClose} className="px-4 py-2 text-sm font-medium text-gray-400 hover:text-white transition-colors">Cancelar</button>
```

---

# Estándar de Iconos de Loading (Frontend)

Todos los spinners/indicadores de carga del frontend (`merulink-front`) deben usar el componente `LoadingSpinner` como estilo por defecto.

## Regla

- Todo **nuevo** spinner/indicador de carga debe usar el componente `LoadingSpinner` (ubicado en `src/components/Shared/LoadingSpinner.jsx`).
- El componente ya incluye el estilo base (icono `Loader` de `lucide-react` con `animate-spin`) y recibe una única prop opcional:
  - `className`: clases extra para el contenedor (por defecto `py-8`).
- No se deben escribir spinners a mano con iconos de `lucide-react` (`Loader`, `Loader2`, etc.) en componentes nuevos.

```jsx
import LoadingSpinner from '../Shared/LoadingSpinner';

{/* Correcto: por defecto */}
<LoadingSpinner />

{/* Correcto: sin padding extra si ya está dentro de un contenedor */}
<LoadingSpinner className="py-0" />

{/* Incorrecto: escribir el spinner a mano con lucide-react */}
<Loader2 className="w-6 h-6 animate-spin" />
```

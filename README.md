# StockPulse: Control de Inventario y Reabastecimiento

Sistema interactivo para la gestión de inventario en almacenes, prevención de quiebres de stock y automatización de alertas a proveedores mediante **Supabase Realtime** y **N8N Webhooks**.

---

## 🚀 Estructura del Proyecto

```plaintext
ProyectoAntigravity/
│
├── index.html                  # Interfaz principal (HTML5 + Tailwind CSS CDN + Responsive)
├── schema.sql                  # Script SQL para crear la tabla y políticas RLS en Supabase
├── README.md                   # Instrucciones completas de configuración y uso
│
└── js/
    ├── config.js               # Constantes SUPABASE_URL, SUPABASE_ANON_KEY y N8N_WEBHOOK_URL
    ├── app.js                  # Orquestador del DOM, filtros en vivo, métricas y eventos
    ├── ui/
    │   └── notifications.js    # Sistema reactivo de Toasts y validación visual (sin alert())
    └── services/
        ├── supabaseService.js  # Integración con el SDK oficial (@supabase/supabase-js) y Realtime
        └── n8nService.js       # Despachador HTTP POST hacia el Webhook de N8N
```

---

## 🛠️ Paso 1: Configurar la Base de Datos en Supabase

1. Inicia sesión en tu panel de [Supabase](https://supabase.com).
2. Ve a la sección **SQL Editor**.
3. Copia y pega el contenido del archivo [`schema.sql`](file:///c:/Users/USER/Documents/ProyectoAntigravity/schema.sql) y ejecuta el script:
   - Crea la tabla `inventario_productos`.
   - Habilita **Row Level Security (RLS)** y sus políticas de lectura/escritura con la anon key.
   - Habilita **Supabase Realtime** para la tabla.
   - Inserta registros de prueba iniciales (con ejemplos en estado crítico).

---

## 🔑 Paso 2: Configurar las Credenciales en `js/config.js`

Abre el archivo [`js/config.js`](file:///c:/Users/USER/Documents/ProyectoAntigravity/js/config.js) y reemplaza los valores de las constantes:

```javascript
const SUPABASE_URL = "https://tu-proyecto.supabase.co"; // De Project Settings -> API
const SUPABASE_ANON_KEY = "tu-clave-anonima-publica";  // Project API Keys (anon public)
const N8N_WEBHOOK_URL = "https://tu-instancia-n8n.com/webhook/stockpulse-alerta"; // Nodo Webhook en N8N
```

---

## ⚡ Paso 3: Flujo de Automatización en N8N

Cuando el encargado de bodega actualiza un producto desde la aplicación web:

1. **Si `stock_actual <= stock_minimo`**, el sistema marca la transacción como **Crítica** y emite una alerta roja en el DOM.
2. Se envía un payload JSON al Webhook de N8N con la siguiente estructura:

```json
{
  "evento": "ACTUALIZACION_STOCK",
  "nivel_prioridad": "CRITICA",
  "requiere_reabastecimiento": true,
  "timestamp": "2026-11-17T21:40:00.000Z",
  "producto": {
    "sku": "SKU-102",
    "nombre_producto": "Válvula de Presión 2\"",
    "bodega": "Bodega Norte",
    "stock_actual": 3,
    "stock_minimo": 10,
    "deficit": 7,
    "proveedor_email": "suministros.valvulas@empresa.com"
  },
  "origen": "StockPulse Web Client v1.0"
}
```

3. En tu flujo de N8N puedes agregar un nodo **IF** (`requiere_reabastecimiento === true`) y enviar un correo electrónico automático (nodo **Gmail** o **SendGrid**) o mensaje a **Slack/Telegram** con la orden de compra inmediata.

---

## 🖥️ Paso 4: Ejecutar y Probar la Aplicación

Puedes abrir directamente el archivo [`index.html`](file:///c:/Users/USER/Documents/ProyectoAntigravity/index.html) en tu navegador preferido o servirlo mediante un servidor local:

- **Con Live Server**: Clic derecho en `index.html` -> *Open with Live Server* en VS Code / Antigravity.
- **Con Python**:
  ```bash
  python -m http.server 8080
  ```
- **Con Node / npx**:
  ```bash
  npx serve .
  ```


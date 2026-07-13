# Handoff — Sitio web psicólogo

Documento de referencia para adaptar este sitio a un nuevo cliente. Leer antes de arrancar.

---

## Stack técnico

- **HTML/CSS/JS estático** — un solo `index.html` + `style.css`
- **Deploy**: Vercel (conectado a GitHub, auto-deploy en cada push a `main`)
- **DNS**: GoDaddy (dominio del cliente apuntado a Vercel vía CNAME)
- **Repo base**: este mismo repositorio — duplicarlo en GitHub para cada cliente nuevo

---

## Integraciones configuradas — qué reemplazar por cliente

### 1. Google Tag Manager (GTM)
- **Dónde**: `index.html` líneas ~9-10 (snippet en `<head>`) y línea ~83 (noscript en `<body>`)
- **ID actual**: `GTM-KD44ZNV3`
- **Qué hacer**: crear un contenedor GTM nuevo para el cliente → reemplazar el ID en ambos lugares
- **Dentro de GTM**: configurar GA4 y Google Ads como tags (no están hardcodeados en el HTML, viven dentro del contenedor GTM)

### 2. PostHog (analytics + session replay)
- **Dónde**: `index.html` líneas ~72-80
- **Clave actual**: `phc_mSbEUB7Bqy9QVXr9YCfZnPZcyXr7mwJLA6MxMUFz4iBY`
- **API host**: `https://us.i.posthog.com`
- **Qué hacer**: crear proyecto nuevo en posthog.com → reemplazar la clave `phc_...` en el snippet
- **CSP**: el dominio `us.i.posthog.com` y `us-assets.i.posthog.com` ya están en `vercel.json` — no tocar si se mantiene PostHog

### 3. Web3Forms (formulario de contacto)
- **Dónde**: `index.html` línea ~401 (`<input type="hidden" name="access_key">`)
- **Clave actual**: `6f19ae7f-4534-4355-9f3e-f03092177150`
- **Qué hacer**: crear cuenta en web3forms.com con el email del cliente → obtener nueva `access_key` → reemplazarla
- **Plan free**: solo 1 destinatario. Si el cliente quiere reenvío automático, configurarlo desde Gmail (Configuración → Reenvío)
- **Spam**: el cliente debe crear un filtro en Gmail para `noreply@web3forms.com` → "No enviarlo nunca a spam"
- **Subject**: cambiar el texto en `<input name="subject">` con el nombre del cliente

### 4. WhatsApp (botón flotante + link alternativo en el form)
- **Número actual**: `541150478457` (Argentina +54 11 5047-8457)
- **Dónde**: aparece 2 veces en `index.html` (buscar `wa.me`)
- **Qué hacer**: reemplazar el número y el texto del mensaje en ambos lugares
- **Formato URL**: `https://wa.me/[código país sin +][número sin espacios]`

### 5. Newsletter / Blog (botón NeoPsy)
- **Dónde**: `index.html` línea ~465
- **URL actual**: `https://neopsy-newsletter.beehiiv.com`
- **Qué hacer**: reemplazar por la URL del blog/newsletter del cliente, o eliminar la sección si no aplica

---

## Colores — cómo cambiar el look

El sitio usa variables CSS centralizadas en `style.css` (primeras ~30 líneas). Cambiar estas variables cambia todo el sitio:

```css
--ink: #1a1a18;          /* texto principal */
--paper: #faf9f6;        /* fondo */
--terracotta: #c4613a;   /* color de acento (links, highlights) */
--muted: #6b6b60;        /* texto secundario */
--hairline: #e8e4dc;     /* bordes suaves */
```

Para un cliente nuevo: definir paleta → reemplazar estos valores → todo el sitio se actualiza.

---

## Content Security Policy (CSP)

Está en `vercel.json`. **Importante**: cada vez que se agrega un CDN o servicio externo nuevo hay que agregarlo a la CSP o el browser lo va a bloquear silenciosamente.

Servicios ya incluidos:
- `googletagmanager.com` — GTM
- `google-analytics.com`, `region1.google-analytics.com` — GA4
- `us.i.posthog.com`, `us-assets.i.posthog.com` — PostHog
- `api.web3forms.com` — formulario
- `cdn.jsdelivr.net` — intl-tel-input (selector de teléfono con banderas)
- `fonts.googleapis.com`, `fonts.gstatic.com` — Google Fonts

Si se reemplaza algún servicio por otro, actualizar la CSP en consecuencia.

---

## Campo de teléfono (intl-tel-input)

- **Librería**: `intl-tel-input@23` vía CDN (jsdelivr)
- **Config**: país inicial Argentina, preferidos `['ar', 'es', 'uy', 'cl', 'mx', 'co', 'pe']`
- **Dónde**: script al final del `<body>` y `<link>` en el `<head>`
- **Para otro país**: cambiar `initialCountry` y `preferredCountries` en la inicialización
- **Validación**: el form valida dígitos correctos según el país seleccionado antes de enviar

---

## Anchors para sitelinks de Google

Secciones con ID para sitelinks en Google Search Console:

| Anchor | Sección |
|--------|---------|
| `#enfoque` | "Esto es lo que buscamos construir" |
| `#para-quien` | "¿Sos la persona correcta?" |
| `#sobre` | Sobre mí |
| `#emigrar` | Si vivís fuera de tu país |
| `#faq` | Preguntas frecuentes |
| `#neopsy` | NeoPsy / Blog |
| `#contacto` | Formulario de contacto |

Para un cliente nuevo: mantener los anchors que apliquen, renombrar o eliminar los que no.

---

## Deploy — paso a paso para cliente nuevo

1. Duplicar este repo en GitHub (fork o repo nuevo con los archivos)
2. Editar `index.html` y `style.css` con el contenido del cliente
3. Crear proyecto en Vercel → conectar al nuevo repo → auto-deploy configurado
4. En GoDaddy (o el DNS del cliente): agregar CNAME del dominio apuntando a Vercel
5. En Vercel: agregar el dominio custom del cliente
6. Configurar filtro anti-spam en Gmail del cliente para Web3Forms
7. Verificar el sitio en Google Search Console con el nuevo dominio

---

## Archivos clave

| Archivo | Qué contiene |
|---------|-------------|
| `index.html` | Todo el HTML, scripts inline, inicialización de integraciones |
| `style.css` | Variables de color + todos los estilos |
| `vercel.json` | Headers de seguridad + Content Security Policy |
| `images/` | Fotos del profesional (reemplazar todas para cliente nuevo) |

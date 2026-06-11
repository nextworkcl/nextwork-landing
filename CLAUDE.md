# Nextwork — Contexto del Proyecto

## ¿Qué es Nextwork?
Plataforma chilena para conectar fundadores, cofundadores, desarrolladores, creativos y emprendedores con proyectos que necesitan equipo. El mecanismo core es un **AI de matching basado en perfiles** (en desarrollo por el cofundador).

- **Web:** https://nextwork.cl
- **Instagram:** @nextworkcl
- **Email de contacto:** bastycuevas0@gmail.com
- **Lanzamiento objetivo:** Diciembre 2026

## Equipo
- **Pablo (pmora)** — fundador, trabaja en marketing, landing, contenido
- **Cofundador** — trabaja en el AI de matching y la plataforma principal

## Los 4 tipos de usuario (core del producto)
1. 🧑‍💻 **El Técnico Solo** — puede construir todo pero busca socio comercial
2. 💡 **El Visionario Sin Equipo** — tiene la idea validada, busca quien ejecute técnicamente
3. 🎨 **El Creativo Trabado** — diseña y vende pero no puede escalar sin técnico
4. 🚀 **El que Arrancó Solo** — ya tiene usuarios/ingresos, busca socio que comparta la carga

---

## URLs de producción

| Página | URL | Proyecto Cloudflare |
|--------|-----|---------------------|
| **Lista de espera (landing)** | https://nextwork.cl | `nextwork-landing` |
| **Plataforma principal** | https://nextwork-55o.pages.dev | `nextwork` |

> La plataforma también es accesible por deployment URLs como `https://080952f4.nextwork-55o.pages.dev/`

---

## Stack Técnico

### Landing (nextwork.cl)
- **HTML/CSS/JS puro** — sin frameworks
- **Hosting:** Cloudflare Pages
- **Proyecto Cloudflare:** `nextwork-landing`
- **Carpeta local:** `C:\Users\pmora\OneDrive\Escritorio\NextWork-Landing\`
- **Deploy:** `npx wrangler pages deploy . --project-name nextwork-landing --branch main`

### Plataforma principal (nextwork-55o.pages.dev)
- **HTML/CSS/JS puro** — sin frameworks
- **Hosting:** Cloudflare Pages
- **Proyecto Cloudflare:** `nextwork`
- **Carpeta local:** `C:\Users\pmora\OneDrive\Escritorio\NextWork\`
- **Deploy:** `npx wrangler pages deploy . --project-name nextwork --branch main`

### Base de datos
- **Supabase** — proyecto: `vkewxmrutpjmdrxsqdea`
- **URL:** `https://vkewxmrutpjmdrxsqdea.supabase.co`
- **Anon key:** `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InZrZXd4bXJ1dHBqbWRyeHNxZGVhIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Nzg1MjUwMTYsImV4cCI6MjA5NDEwMTAxNn0.Ety8tIitQKW_3hEaH0obDnmewPx2Opx_ZPmUmIP9ZU0`
- **Tabla:** `waitlist` — columnas: `name`, `email`, `created_at`
- **RLS:** SELECT habilitado para anon (para el contador en vivo)

### Email
- **EmailJS** — service: `service_kr7yft1`, public key: `qCCiB5C-ktvGFIFyt`
- **Template equipo** (notificación interna): `template_mec1fwa`
- **Template bienvenida** (al usuario): `dgguwu8` — ⚠️ pendiente diseño HTML elegante

### Cloudflare
- **Account ID:** `7157792545d788fd662b97a838f23770`
- **Email:** `bastycuevas0@gmail.com`
- **Auth (jun 2026):** API token `claude-deploy-pages` (scope: Cloudflare Pages Edit) guardado como variable de entorno de usuario `CLOUDFLARE_API_TOKEN` en el PC de Pablo — **nunca en el repo**. `CLOUDFLARE_ACCOUNT_ID` también está como variable de entorno.
- **Deploy plataforma:** `npx wrangler pages deploy . --project-name nextwork --branch main` desde carpeta limpia (solo html/css/js/png — NUNCA desde la carpeta raíz directa, contiene zips y carpetas internas).
- **Deploy landing:** automático — push a GitHub (`nextworkcl/nextwork-landing`) despliega solo.
- **Bug SCL:** el 502 del datacenter Santiago no apareció en los deploys de jun 2026 — posiblemente resuelto, pero si reaparece: hotspot del celular o Dashboard.

### Seguridad (aplicada junio 2026)
- **`_headers`** en la landing: HSTS, CSP, X-Frame-Options, Permissions-Policy
- **SRI:** scripts CDN fijados — Supabase `2.108.1`, EmailJS `4.4.1` con hash sha384
- **Honeypot** (`#wl-website`) + **time-gate 3s** + regex de email en el formulario waitlist
- **Validación typos** de email con Levenshtein (aporte cofundador, integrada)
- **EmailJS domain whitelist:** solo nextwork.cl, www.nextwork.cl y nextwork-55o.pages.dev
- **HSTS activado en Cloudflare** (12 meses, include subdomains)
- **Rate limiting:** regla `limite-formulario` — bloquea IPs que excedan el límite en `/`
- ⚠️ Pendiente verificación: políticas RLS de INSERT en Supabase tabla `waitlist`

---

## Archivos del proyecto

```
C:\Users\pmora\OneDrive\Escritorio\NextWork-Landing\
├── index.html          — Landing page completa (waitlist)
├── logo.png            — Logo circular con fondo transparente (150x150px)
├── og.png              — OG image 1200x630px (⚠️ pendiente deploy)
└── wrangler.toml       — Config Cloudflare Pages

C:\Users\pmora\Downloads\
├── og-image.html       — Generador del OG image con html2canvas
├── carrusel-3.html     — Carrusel Instagram #3
├── carrusel-4.html     — Carrusel Instagram #4 "¿Qué es Nextwork?"
└── carrusel-5.html     — Carrusel Instagram #5 "Los 4 tipos" (el más reciente)
```

---

## Landing page — Estado actual

### Secciones existentes
1. **Nav** — logo circular + "nextwork", fondo verde oscuro `#132b1c`
2. **Hero** — badge, h1, subtítulo, countdown a diciembre 2026
3. **Formulario** — nombre + email → Supabase + EmailJS
4. **Contador en vivo** — cuenta real desde Supabase, polling cada 10s, toast notification
5. **¿Qué es Nextwork?** — 3 cards (cofundadores, equipo, proyectos)
6. **FAQ** — 6 preguntas con acordeón animado
7. **Footer** — link Instagram, copyright

### Variables CSS principales
```css
--bg: #ede9e2        /* beige cálido */
--dark: #132b1c      /* verde oscuro nav/footer */
--accent: #38dc78    /* verde brillante */
--surface: #ffffff   /* cards blancas */
--text: #0d1f14
```

### Lógica JS importante
- `joinWaitlist()` — inserta en Supabase, obtiene posición, envía 2 emails (equipo + bienvenida), muestra éxito
- `loadCount()` — consulta COUNT de Supabase, anima el número, muestra toast si hay nuevo registro
- `toggleFaq()` — acordeón: abre uno, cierra el resto
- Countdown a `2026-12-01T00:00:00`

### Meta tags OG (apuntan a og.png pendiente)
```html
<meta property="og:image" content="https://nextwork.cl/og.png">
<meta property="og:title" content="Tu proyecto merece un equipo.">
```

---

## Deploy — Cómo hacerlo

### Deploy normal (sin og.png para evitar bug SCL)
```powershell
# Token guardado fuera del repo — pedirlo a Pablo o crear uno nuevo en
# dash.cloudflare.com -> My Profile -> API Tokens (plantilla "Edit Cloudflare Pages")
$env:CLOUDFLARE_API_TOKEN = "<TOKEN_AQUI>"

# Crear carpeta temporal sin og.png
$tmpDir = "C:\Users\pmora\AppData\Local\Temp\nw-deploy"
New-Item -ItemType Directory $tmpDir -Force | Out-Null
Copy-Item "C:\Users\pmora\OneDrive\Escritorio\NextWork-Landing\index.html" $tmpDir -Force
Copy-Item "C:\Users\pmora\OneDrive\Escritorio\NextWork-Landing\logo.png" $tmpDir -Force
Copy-Item "C:\Users\pmora\OneDrive\Escritorio\NextWork-Landing\wrangler.toml" $tmpDir -Force

Set-Location $tmpDir
npx wrangler pages deploy . --project-name nextwork-landing --branch main
```

### Para subir og.png
Usar el Dashboard de Cloudflare directamente (evita el bug del API):
1. dash.cloudflare.com → Pages → nextwork-landing
2. "Create new deployment" → "Upload assets"
3. Arrastrar todos los archivos incluyendo og.png

---

## Instagram @nextworkcl — Estado actual

### Métricas (mayo 2026)
- **Seguidores:** 19 (era 13 hace 2 días → +6 orgánicos)
- **Posts:** 3 carruseles publicados
- **Post 1** ("¿Qué es Nextwork?"): 441 views, 224 alcance, 10 envíos DM, 13 likes — tuvo un repost en historia que amplificó el alcance
- **Post 2** ("Por qué tu proyecto no avanza"): 124 views, 18 alcance, 9 likes — orgánico puro
- **Stories:** 19 vistas (100% de seguidores)

### Carruseles creados
- `carrusel-3.html` — "Por qué tu proyecto no avanza" (5 razones)
- `carrusel-4.html` — "¿Qué es Nextwork?" (portada, problema, cómo funciona, CTA)
- `carrusel-5.html` — "Los 4 tipos de emprendedor" (listo para publicar)

### Estética de los carruseles
- Fondo: `#091a0d` (verde muy oscuro)
- Accent: `#38dc78` (verde brillante)
- Font: Inter 900 para títulos
- Logo: hexágono verde con ícono de red + texto "nextwork"
- html2canvas a scale:3 para exportar PNG de alta calidad

---

## Waitlist — Estado actual
- **~11 personas reales** (se limpiaron perfiles de prueba en Supabase)
- Los registros incluyen nombre, email y fecha

---

## Pendientes por prioridad

### 🔴 Urgente
- [ ] Diseñar HTML del email de bienvenida (`dgguwu8`) con estética elegante
- [ ] Publicar carrusel 5 en Instagram
- [ ] Entrar a comunidades de emprendedores chilenos (Discord, WhatsApp universitario)
- [ ] Crear perfil y primer post en LinkedIn

### 🟠 Próximas 2 semanas
- [ ] Agregar campo "¿Qué perfil eres?" al formulario de waitlist
- [ ] Sistema de referidos post-registro (link único, sube posición)
- [ ] Sección "Para quién es" con los 4 tipos en la landing
- [ ] Sección "Cómo funciona" (3 pasos) en la landing
- [ ] Mejorar estado de éxito post-registro + botón compartir WhatsApp
- [ ] Subir og.png cuando SCL esté estable
- [ ] Activar Cloudflare Web Analytics

### 🟡 Próximo mes
- [ ] Carrusel 6 — "Para quién es Nextwork"
- [ ] Primer Reel de Instagram (texto animado, sin cara)
- [ ] Newsletter quincenal para la lista de espera
- [ ] Sección equipo/fundadores en la landing
- [ ] Evento presencial pequeño con los primeros registrados

### 🟢 Antes del lanzamiento
- [ ] Sistema de referidos completo
- [ ] 300-500 personas en lista
- [ ] PR en medios del ecosistema (Chócale, Startup Chile blog)
- [ ] Términos de uso y política de privacidad
- [ ] CI/CD con GitHub Actions
- [ ] Dominio de email propio (hola@nextwork.cl)

---

## Notas importantes
- La validación del mecanismo core (matching por perfiles) ya está validada por experiencia del equipo
- El AI de matching está siendo construido por el cofundador — es el corazón del producto
- Hay una segunda plataforma en desarrollo: `nextwork-55o.pages.dev` (la app real, separada de la landing)
- No usar `wrangler pages deploy` con `og.png` incluida hasta que se resuelva el bug SCL
- El anon key de Supabase no tiene permisos para crear buckets de Storage (requiere cuenta owner)

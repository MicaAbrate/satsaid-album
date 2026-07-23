# 📺 Álbum SATSAID — Consejo Directivo Nacional

Aplicación web gamificada para coleccionar figuritas digitales de los integrantes del Consejo Directivo Nacional del **SATSAID** (Sindicato Argentino de Televisión, Servicios Audiovisuales, Interactivos y de Datos).

Pensada para hijos e hijas de trabajadores del sindicato, con rango de edad de 5 a 13 años.

---

## 🌐 Demo en vivo

> **URL:** `https://satsaid-album.vercel.app` *(reemplazá con tu URL real de Vercel)*

---

## ✨ Funcionalidades

### 🃏 Álbum de figuritas
- **35 figuritas** con fotos reales de los integrantes del CDN
- Agrupadas en: Secretarías Generales, Secretarías, Prosecretarías, Vocales y Comisión Revisora de Cuentas
- 4 niveles de rareza: Común, Raro, Épico y Legendario
- Al tocar una figurita se ve el nombre, cargo y descripción de la función
- Barra de progreso con porcentaje de completado

### ⭐ Tareas diarias (hasta 3 figuritas por día)
| Tarea | Figuritas |
|-------|-----------|
| Entrar a la app (primer acceso del día) | 1 figurita automática |
| Responder correctamente la pregunta del día | +1 figurita |
| Resolver el acertijo del día | +1 figurita |

- La pregunta y el acertijo rotan cada día automáticamente
- Las preguntas son sobre el SATSAID, sus integrantes y la industria audiovisual

### 👤 Usuarios
- Registro e inicio de sesión con email y contraseña
- Contraseñas encriptadas con SHA-256
- Sesión persistente (30 días sin necesidad de volver a loguearse)
- Recuperación de contraseña por email

### 🎁 Regalo de figuritas
- Podés regalar figuritas **repetidas** a otros usuarios
- El destinatario recibe una notificación en la app (polling cada 30 segundos)
- También recibe un **email automático** con el nombre del remitente y la figurita recibida

### 📤 Compartir colección
- Generá un resumen de tu colección para compartir por WhatsApp o copiar al portapapeles

### 🏆 Álbum completo
- Al completar las 35 figuritas se dispara una pantalla especial con confetti y sonido

### 🛡️ Panel de administración
- Solo visible para usuarios con rol `admin`
- Dar figuritas a cualquier usuario (por email)
- Ver lista de todos los usuarios registrados
- Asignar o quitar el rol admin a otros usuarios

---

## 🛠️ Stack tecnológico

| Tecnología | Uso |
|------------|-----|
| **React 18** | UI y lógica de componentes |
| **Vite 5** | Bundler y servidor de desarrollo |
| **Supabase** | Base de datos PostgreSQL en la nube |
| **Vercel** | Hosting y deploy continuo |
| **Resend** | Envío de emails transaccionales |
| **Web Audio API** | Sonidos generados sin archivos externos |
| **Web Crypto API** | Encriptación de contraseñas (SHA-256) |

> Sin librerías de UI externas — todo el diseño está hecho con estilos inline en React.

---

## 🗂️ Estructura del proyecto

```
satsaid-album/
├── index.html              # Entry point HTML
├── vite.config.js          # Configuración de Vite
├── vercel.json             # Configuración de Vercel (SPA routing)
├── package.json            # Dependencias
├── .gitignore
└── src/
    ├── main.jsx            # Punto de entrada de React
    └── App.jsx             # Toda la aplicación (1834 líneas)
```

### Arquitectura de `App.jsx`

```
DATOS
├── PHOTOS           → Imágenes base64 de los integrantes
├── STICKERS[]       → 35 figuritas con nombre, cargo, rareza, foto y descripción
├── RARITY_CONFIG    → Colores y probabilidades por rareza
├── TRIVIA[]         → 17 preguntas sobre el SATSAID
├── PUZZLES[]        → 7 acertijos temáticos
└── ALBUM_GROUPS[]   → Agrupación del álbum

SERVICIOS (Supabase REST API)
├── sb()             → Fetch wrapper para Supabase
├── loadUser()       → Cargar usuario por email
├── saveUser()       → Guardar/actualizar usuario
├── loadAllUsers()   → Listar todos los usuarios (solo admin)
├── sendGift()       → Transferir figurita entre usuarios
├── createSession()  → Crear token de sesión persistente
├── loadSession()    → Restaurar sesión desde localStorage
└── clearSession()   → Cerrar sesión

SERVICIOS (Resend)
├── sendEmail()         → Wrapper genérico de email
├── sendGiftEmail()     → Notificación de regalo recibido
└── sendResetEmail()    → Link de recuperación de contraseña

COMPONENTES
├── SplashScreen         → Pantalla de carga inicial (2s)
├── AuthScreen           → Login / Registro / Olvidé contraseña / Reset
├── App (default)        → Shell principal con navegación
│   ├── Tab: Álbum       → Grid de figuritas agrupadas
│   ├── Tab: Tareas      → Racha + Trivia + Acertijo diarios
│   ├── Tab: Perfil      → Stats + Compartir + Cerrar sesión
│   └── Modales:
│       ├── NewStickerModal     → Animación al ganar figurita
│       ├── StickerDetailModal  → Info del integrante
│       ├── GiftScreen          → Flujo para regalar figurita
│       ├── InboxModal          → Buzón de figuritas recibidas
│       ├── ShareModal          → Compartir colección
│       ├── AlbumCompleteModal  → Celebración al completar
│       └── AdminPanel          → Gestión de usuarios
```

---

## 🗄️ Base de datos (Supabase)

### Tabla `users`

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `email` | text (PK) | Email del usuario |
| `password` | text | Contraseña hasheada (SHA-256) |
| `name` | text | Nombre del usuario |
| `is_admin` | boolean | Si tiene acceso al panel admin |
| `stickers` | jsonb | `{ "1": 2, "3": 1, ... }` — id: cantidad |
| `tasks_today` | jsonb | `{ trivia, puzzle, dailySticker }` |
| `last_tasks` | text | Fecha última (YYYY-MM-DD) para reset diario |
| `last_login` | text | Fecha del último acceso |
| `total_earned` | integer | Total de figuritas ganadas históricamente |
| `join_date` | text | Fecha de registro |
| `inbox` | jsonb | Array de regalos recibidos |

### Tabla `sessions`

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `token` | text (PK) | Token UUID único |
| `email` | text (FK) | Usuario dueño de la sesión |
| `created_at` | timestamptz | Fecha de creación |
| `expires_at` | timestamptz | Expira en 30 días |

### Tabla `password_resets`

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `token` | text (PK) | Token UUID único |
| `email` | text | Email del usuario |
| `expires_at` | timestamptz | Expira en 1 hora |
| `used` | boolean | Si ya fue utilizado |

---

## 🚀 Setup local

### Requisitos
- Node.js 18+
- Cuenta en [Supabase](https://supabase.com)
- Cuenta en [Resend](https://resend.com) (para emails)

### Instalación

```bash
# Clonar el repositorio
git clone https://github.com/TU_USUARIO/satsaid-album.git
cd satsaid-album

# Instalar dependencias
npm install

# Correr en modo desarrollo
npm run dev
```

La app corre en `http://localhost:5173`

### Variables a configurar en `src/App.jsx`

```js
const SUPABASE_URL = "https://TU_PROYECTO.supabase.co";
const SUPABASE_KEY = "eyJ...";   // anon/public key de Supabase
const RESEND_KEY   = "re_...";   // API key de Resend
```

### Crear las tablas en Supabase

Ejecutar en **SQL Editor** de Supabase:

```sql
-- Tabla de usuarios
create table users (
  email text primary key,
  password text not null,
  name text not null,
  is_admin boolean default false,
  stickers jsonb default '{}',
  tasks_today jsonb default '{"trivia":false,"puzzle":false,"dailySticker":false}',
  last_tasks text default '',
  last_login text default '',
  total_earned integer default 0,
  join_date text default '',
  inbox jsonb default '[]'
);
alter table users enable row level security;
create policy "allow all" on users for all using (true) with check (true);

-- Tabla de sesiones
create table sessions (
  token text primary key,
  email text not null references users(email) on delete cascade,
  created_at timestamptz default now(),
  expires_at timestamptz default (now() + interval '30 days')
);
alter table sessions enable row level security;
create policy "allow all" on sessions for all using (true) with check (true);

-- Tabla de reset de contraseña
create table password_resets (
  token text primary key,
  email text not null,
  expires_at timestamptz default (now() + interval '1 hour'),
  used boolean default false
);
alter table password_resets enable row level security;
create policy "allow all" on password_resets for all using (true) with check (true);
```

### Build para producción

```bash
npm run build
```

Genera la carpeta `dist/` lista para deployar.

---

## 🔧 Deploy en Vercel

1. Subir el código a GitHub
2. Conectar el repo en [vercel.com](https://vercel.com)
3. Framework preset: **Vite** (se detecta automáticamente)
4. Click en **Deploy**

Vercel redeploya automáticamente en cada push a `main`.

---

## 🛡️ Dar rol de administrador

Desde **Supabase → SQL Editor**:

```sql
update users set is_admin = true where email = 'admin@ejemplo.com';
```

O desde dentro de la app, si ya sos admin: **Panel Admin → Usuarios → "Hacer admin"**.

---

## 📋 Lógica de figuritas

### Rareza y probabilidades

| Rareza | Color | Probabilidad |
|--------|-------|-------------|
| Legendario | Dorado | 3% |
| Épico | Violeta | 12% |
| Raro | Azul | 25% |
| Común | Gris | 60% |

### Integrantes por rareza

- **Legendarios (2):** Secretario General y Secretario General Adjunto
- **Épicos (3):** Sec. Gremial, Sec. Administrativa, Sec. de Finanzas
- **Raros (13):** resto de las Secretarías
- **Comunes (17):** Prosecretarías, Vocales y CRC

### Sistema de regalo

Solo se pueden regalar figuritas **repetidas** (cantidad ≥ 2). Al regalar:
1. Se descuenta 1 del remitente
2. Se suma 1 al destinatario
3. Aparece en el buzón del destinatario
4. Se envía un email de notificación automático

---

## 📁 Agregar nuevas figuritas

En `src/App.jsx`, agregar al array `STICKERS`:

```js
{
  id: 36,                           // ID único
  name: "Nombre Apellido",
  photoKey: "Nombre_Apellido",      // debe coincidir con la key en PHOTOS
  role: "Cargo en el sindicato",
  initials: "NA",                   // se muestra si no hay foto
  color: "#1565C0",                 // color del avatar
  rarity: "common",                 // common | rare | epic | legendary
  group: "secretarias",             // sg | secretarias | prosecretarias | vocales | crc
  desc: "Descripción del cargo...",
}
```

Para agregar la foto, convertirla a base64 y agregarla al objeto `PHOTOS` al inicio del archivo.

---

## 📝 Agregar preguntas de trivia

En `src/App.jsx`, agregar al array `TRIVIA`:

```js
{
  q: "¿Texto de la pregunta?",
  opts: ["Opción A", "Opción B", "Opción C", "Opción D"],
  a: 0,        // índice de la respuesta correcta (0-3)
  fun: "Dato curioso que se muestra al acertar 🎉"
}
```

Las preguntas rotan automáticamente por día del año.

---

## 🔒 Consideraciones de seguridad

- Las contraseñas se hashean con **SHA-256** antes de guardarse en la base de datos
- La API key de Resend y la key anon de Supabase están en el código cliente — es aceptable para un MVP pero en producción deberían moverse a variables de entorno o Edge Functions
- Las sesiones expiran a los **30 días** automáticamente
- Los links de reset de contraseña expiran en **1 hora** y son de un solo uso

---

## 📄 Licencia

Uso interno del SATSAID. Todos los derechos reservados.

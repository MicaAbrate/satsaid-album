# Guía para contribuir al proyecto

Esta guía explica cómo hacer cambios al Álbum SATSAID sin romper nada.

---

## Flujo de trabajo recomendado

1. Hacé los cambios en el código
2. Probalo en local con `npm run dev`
3. Subí los cambios a GitHub
4. Vercel redeploya automáticamente en ~1 minuto

---

## Tareas frecuentes

### ➕ Agregar una nueva figurita

**1. Agregar al array `STICKERS` en `App.jsx`:**

```js
{
  id: 36,                        // número siguiente al último
  name: "Nombre Apellido",
  photoKey: "Nombre_Apellido",   // sin acentos, con guión bajo
  role: "Cargo completo",
  initials: "NA",                // iniciales para el avatar de respaldo
  color: "#1565C0",              // color hex del avatar
  rarity: "common",             // common | rare | epic | legendary
  group: "crc",                 // sg | secretarias | prosecretarias | vocales | crc
  desc: "Descripción clara del cargo en lenguaje accesible para chicos...",
}
```

**2. Agregar la foto al objeto `PHOTOS`:**

Convertí la imagen a base64 (podés usar [base64.guru](https://base64.guru/converter/encode/image)) y agregala:

```js
const PHOTOS = {
  // ... fotos existentes ...
  "Nombre_Apellido": "data:image/png;base64,iVBORw0KGgo...",
};
```

Las fotos deben ser circulares o cuadradas, idealmente 300×300px o más.

---

### ❓ Agregar preguntas de trivia

En el array `TRIVIA`:

```js
{
  q: "¿Texto de la pregunta?",
  opts: ["Opción A", "Opción B", "Opción C", "Opción D"],
  a: 2,       // índice de la respuesta correcta (0, 1, 2 o 3)
  fun: "Dato curioso que se muestra al acertar 🎉"
},
```

**Tips para las preguntas:**
- Adaptadas para chicos de 5 a 13 años → lenguaje simple
- Que tengan una sola respuesta claramente correcta
- El campo `fun` debe ser un dato interesante, no solo repetir la respuesta

---

### 🧩 Agregar acertijos

En el array `PUZZLES`:

```js
{
  q: "Texto del acertijo...",
  a: "respuesta",      // en minúsculas, sin acentos (se normaliza automáticamente)
  hint: "Pista si no lo adivinan 💡"
},
```

**Tips para acertijos:**
- La respuesta se valida ignorando mayúsculas y acentos
- Deben ser resolubles por un nene de 5 años con la pista
- Relacionados con televisión, sindicato o trabajo audiovisual

---

### 🛡️ Dar rol de administrador a un usuario

**Opción 1 — Desde Supabase SQL Editor:**
```sql
update users set is_admin = true where email = 'email@ejemplo.com';
```

**Opción 2 — Desde el panel admin de la app:**
Entrá con tu cuenta admin → ícono 🛡️ en la barra → pestaña Usuarios → "Hacer admin"

---

### 🔑 Cambiar las credenciales de servicios

Las claves están en las primeras líneas de `App.jsx`:

```js
const SUPABASE_URL = "https://...supabase.co";
const SUPABASE_KEY = "eyJ...";
const RESEND_KEY   = "re_...";
```

> ⚠️ Nunca subas claves `service_role` de Supabase al frontend. Solo la clave `anon/public`.

---

## Estructura de datos del usuario

Así se ve un usuario completo en Supabase:

```json
{
  "email": "usuario@ejemplo.com",
  "password": "a665a45920422...",  // SHA-256 hash
  "name": "Juan Pérez",
  "is_admin": false,
  "stickers": {
    "1": 2,    // tiene 2 copias de la figurita id=1
    "5": 1,    // tiene 1 copia de la figurita id=5
    "12": 3    // tiene 3 copias de la figurita id=12
  },
  "tasks_today": {
    "trivia": true,
    "puzzle": false,
    "dailySticker": true
  },
  "last_tasks": "2025-07-23",
  "last_login": "2025-07-23",
  "total_earned": 15,
  "join_date": "2025-06-01",
  "inbox": [
    {
      "from": "otro@email.com",
      "fromName": "María García",
      "stickerId": 3,
      "date": "23/7/2025"
    }
  ]
}
```

---

## Rareza de las figuritas

| Rareza | Probabilidad | Quiénes |
|--------|-------------|---------|
| Legendario | 3% | Sec. General y Sec. General Adjunto |
| Épico | 12% | Sec. Gremial, Administrativa, Finanzas |
| Raro | 25% | Resto de Secretarías (13 integrantes) |
| Común | 60% | Prosecretarías, Vocales, CRC (17 integrantes) |

Para cambiar las probabilidades, modificar la función `rollSticker()`:

```js
function rollSticker() {
  const rand = Math.random() * 100;
  let rarity = rand < 3 ? "legendary" : rand < 15 ? "epic" : rand < 40 ? "rare" : "common";
  //                   ↑ 3%               ↑ 12%              ↑ 25%           ↑ 60%
  const pool = STICKERS.filter(s => s.rarity === rarity);
  return pool[Math.floor(Math.random() * pool.length)];
}
```

---

## Comandos útiles

```bash
# Desarrollo local
npm run dev

# Build de producción (para verificar que no hay errores)
npm run build

# Preview del build
npm run preview
```

---

## Contacto

Para dudas sobre el proyecto, contactar al área de sistemas del SATSAID.

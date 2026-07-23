# Changelog

## [1.4.0] - 2025

### Agregado
- 🛡️ Edge Functions en Supabase para validar figuritas en el servidor (anti-trampa)
- 🔒 RLS estricta en Supabase — el cliente no puede modificar stickers directamente
- 📧 Emails de verificación y reset van por servidor (Resend key fuera del frontend)
- ✅ Verificación de email al registrarse — cuenta inactiva hasta confirmar
- 🔐 Variables de entorno en Vercel — keys fuera del código fuente
- 🎨 Header rediseñado: logo centrado, saludo en negrita, sin contador de figuritas
- 📊 Contador "X de 35" en las stats del perfil

### Modificado
- Las figuritas ahora las otorga el servidor (Edge Function earn-sticker), no el cliente
- Los emails se envían desde Edge Function send-email, no desde el navegador
- El regalo de figuritas pasa por Edge Function send-gift con validaciones server-side

---

## [1.3.0] - 2025

### Agregado
- 🔐 Contraseñas encriptadas con SHA-256 (Web Crypto API), migración automática al login
- 💾 Sesión persistente — no requiere login al volver a la app (duración 30 días)
- 🔑 "Olvidé mi contraseña" con link de reset por email (expira en 1 hora, uso único)
- 📧 Email automático al recibir una figurita de regalo (vía Resend)
- 📬 Notificaciones en tiempo real de regalos recibidos (polling cada 30 segundos)
- 🏆 Pantalla de álbum completo con confetti y fanfarria al coleccionar las 35 figuritas
- 📤 Compartir colección desde el perfil (WhatsApp o copiar texto)
- 🗄️ Tablas `sessions` y `password_resets` en Supabase

---

## [1.2.0] - 2025

### Agregado
- 🌐 Migración de `localStorage` a **Supabase** (PostgreSQL en la nube)
- 👥 Los datos persisten entre dispositivos y navegadores
- 🎁 Sistema de regalo de figuritas entre usuarios con buzón de recibidos
- 🛡️ Panel de administración (dar figuritas, gestionar usuarios, asignar roles admin)
- 🗄️ Tabla `users` en Supabase con todos los datos del álbum

---

## [1.1.0] - 2025

### Agregado
- 🎬 Splash screen con logo oficial del SATSAID y animación de carga (2 segundos)
- 🎴 Animación al ganar figurita: efecto flip 3D, destello de color según rareza, partículas para épicas y legendarias
- 🔊 Sonidos generados con Web Audio API según rareza (sin archivos externos)
- 📅 Pregunta del día y acertijo del día rotan automáticamente según el día del año
- 📛 Las figuritas descubiertas muestran nombre y cargo en la tarjeta del álbum
- 🎁 Hasta 3 figuritas por día: 1 automática al entrar + 1 por trivia + 1 por acertijo
- 📊 Barra de progreso en el header muestra % de completado del álbum

### Modificado
- Eliminado el sistema de racha diaria
- La pregunta diaria ahora es única por día en lugar de aleatoria

---

## [1.0.0] - 2025

### Inicial
- 📖 Álbum con 35 figuritas del Consejo Directivo Nacional del SATSAID
- 👤 Sistema de usuarios con registro e inicio de sesión (email + contraseña)
- ⭐ Tareas diarias: trivia sobre el SATSAID y acertijos temáticos
- 🃏 4 niveles de rareza: Común (60%), Raro (25%), Épico (12%), Legendario (3%)
- 🖼️ Fotos reales de 29 integrantes del CDN embebidas en base64
- 💬 Modal de detalle al tocar una figurita con descripción del cargo
- 📱 Diseño responsivo optimizado para móvil
- 🗂️ Álbum agrupado en: Secretarías Generales, Secretarías, Prosecretarías, Vocales y CRC
- 📊 Progreso por grupo en el perfil del usuario
- 🔵 Deploy en Vercel con dominio público

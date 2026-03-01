# 🌐 Guía definitiva: obtener un dominio GRATIS con GitHub Student + Name.com

> Esta guía te ayuda a **canjear el dominio gratuito** incluido en el **[GitHub Student Developer Pack](https://education.github.com/pack)** usando **Name.com**.
> Al final verás cómo dejarlo listo para **DNS + SSL** (por ejemplo, para Vercel).

---

## ✅ Requisitos previos

1. 🧑‍🎓 Tener **[GitHub Student Developer Pack](https://education.github.com/pack)** aprobado (cuenta verificada como estudiante).
2. 🔐 Tener acceso a tu **cuenta de GitHub** (idealmente la misma vinculada a tu correo principal).
![screenshot-1772387734544](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772387734544-1772387734791.png)
3. 📧 Recomendación: usar el **mismo Gmail** asociado a tu GitHub para evitar problemas de elegibilidad (esto coincide con tu nota original).
4. 💳 Tener una tarjeta para agregar como método de pago (**no te deberían cobrar** por el dominio gratuito, pero Name.com **exige un método de pago** para registrar dominios).

---
## 🧭 Vista general del flujo (diagrama)
```mermaid
flowchart TD
  A[GitHub Education Pack] --> B[Buscar oferta de Name.com]
  B --> C[Conectar GitHub con Name.com]
  C --> D[Buscar dominio + elegir extensión elegible]
  D --> E[Checkout a $0]
  E --> F[Agregar método de pago (requerido)]
  F --> G[Dominio activo]
  G --> H[Configurar DNS + SSL (ej. Vercel)]
```
---

## 🧩 Sección 1: Entrar al Student Developer Pack

### Paso 1. Inicia sesión en GitHub Education
1. Abre la página del pack:
   - **GitHub Student Developer Pack** → https://education.github.com/pack
2. Inicia sesión con tu cuenta de GitHub.

![screenshot-1772387807528](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772387807528-1772387807776.png)
---

## 🎁 Sección 2: Encontrar la oferta de Name.com

### Paso 2. Ir a las ofertas del pack
1. Dentro del pack, busca la categoría **Domains** (o usa el filtro de dominios).
2. Ubica la tarjeta de **Name.com** (beneficio oficial: https://www.name.com/partner/github-students) (oferta de dominio gratis con +25 extensiones).

![screenshot-1772387961347](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772387961347-1772387961516.png)

### Paso 3. Entrar a “Explore more offers”
En algunos casos verás el botón **“Explore more offers”** (tal como indicaste).
Haz clic ahí y navega hasta **Name.com**.

![screenshot-1772387994527](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772387994527-1772387994669.png)

---

## 🔗 Sección 3: Conectar GitHub con Name.com (canjear el beneficio)

### Paso 4. Conectar tu cuenta de GitHub
1. En la tarjeta de Name.com, pulsa algo similar a:
   - **“Get access by connecting your GitHub account on Name.com”**
2. Te redirigirá a Name.com para autorizar la conexión con GitHub.

![screenshot-1772388070318](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772388070318-1772388070622.png)

### Paso 5. Login en Name.com con GitHub
1. En Name.com, elige **Login with GitHub** (o equivalente).
2. Autoriza el acceso si GitHub te lo solicita.

✅ Tip: si no aparece el beneficio, vuelve al pack y entra otra vez desde el enlace oficial de canje.

![screenshot-1772388200858](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772388200858-1772388201033.png)

---

## 🔎 Sección 4: Buscar tu dominio y elegir una extensión elegible

### Paso 6. Buscar el nombre del dominio
1. En Name.com, ve al buscador de dominios.
2. Escribe el nombre que quieres (ej: `miproyecto`).
3. Revisa las extensiones **elegibles del pack** (suelen ser más de 25, por ejemplo: `.live`, `.studio`, `.software`, `.app`, `.dev`, etc.).

![screenshot-1772388271958](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772388271958-1772388272147.png)

---

## 🧾 Sección 5: Checkout sin costo (y por qué pide tarjeta)

### Paso 7. Verificar que el total sea $0
1. Agrega tu dominio elegible al carrito.
2. En el checkout, confirma que el **costo del dominio** esté en **$0**.

![screenshot-1772388375500](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772388375500-1772388375600.png)

### Paso 8. Agregar método de pago (sin miedo 😅)
Aunque el total sea $0, **Name.com requiere un método de pago para registrar dominios**.
Esto es normal y es una duda común reportada por estudiantes.

✅ Recomendaciones:
- Asegúrate de que el total siga en **$0** antes de confirmar.
- Revisa si está activado **Auto-renew** (renovación automática).
  El dominio y/o seguridad avanzada **renuevan al año** a precio estándar si dejas la renovación automática activa.

---

## 🎉 Sección 6: Confirmación y acceso al panel del dominio

### Paso 9. Confirmar compra / registro
1. Finaliza el checkout.
2. Al terminar, ve a **My Domains** en Name.com y confirma que tu dominio aparece activo.

![screenshot-1772388430651](https://raw.githubusercontent.com/MILLERMARRU/mi_docs/main/docs/images/screenshot-1772388430651-1772388430738.png)

---

## 🛠️ Sección 7: Prepararte para DNS y SSL (para Vercel u otro hosting)

Una vez tengas el dominio, ya puedes:
- Configurar **DNS** (A, CNAME, TXT, etc.)
- Activar/gestionar **SSL** desde tu proveedor de hosting (Vercel, Netlify, Cloudflare, etc.)

El siguiente paso sera registrar el dominio en Vercel.
---

## 🧯 Solución rápida de problemas (lo más común)

- **No veo el dominio gratis / no aplica el descuento**
  ➜ Asegúrate de entrar desde el enlace del pack (https://education.github.com/pack) y que estés logueado con GitHub.

- **Me pide tarjeta aunque el total sea $0**
  ➜ Es normal: Name.com requiere método de pago para registrar dominios.

- **Me preocupa que me cobren luego**
  ➜ Revisa la renovación automática. El dominio renueva en 1 año a precio estándar si está activada.

---

### 📎 Próximo archivo 
📄 **Guía 2:** “Configurar DNS de Name.com para Vercel + SSL”

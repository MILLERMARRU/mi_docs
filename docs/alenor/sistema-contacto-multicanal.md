# 📬 PRD — Sistema de Contacto Multicanal · ALENOR

> **Versión:** 1.0.0 · **Fecha:** 2026-03-24 · **Autor:** Miller Zamora  
> **Proyecto:** [alenor.store](https://alenor.store) — E-commerce de jabones artesanales naturales

---

## 🎯 Resumen Ejecutivo

El sistema de contacto de ALENOR está diseñado para reducir la fricción entre el cliente y el equipo de atención al mínimo absoluto. En vez de un formulario tradicional (que requiere esperar respuesta), se optó por canales directos e inmediatos: **WhatsApp** y **Email**, más un punto físico visible en el mapa. La arquitectura está compuesta por **3 componentes independientes** que cubren distintos contextos de uso dentro del sitio.

---

## 🏗️ Arquitectura General del Sistema

```
app/
├── components/
│   ├── widgets/
│   │   └── ContactWidget.tsx       ← Widget flotante global (toda la app)
│   ├── sections/
│   │   └── CTA/
│   │       └── CTASection.tsx      ← Sección de contacto en landing page
│   └── pedido/
│       └── WhatsAppCTA.tsx         ← CTA de confirmación de pedido
├── lib/
│   ├── constants.ts                ← Datos del negocio centralizados
│   └── format.ts                   ← Builders de URLs y mensajes WhatsApp
```

### 📋 Mapa de componentes

| Componente | Ubicación en la App | Propósito |
|---|---|---|
| `ContactWidget` | Global — `app/page.tsx` (footer del layout) | Botón flotante persistente en toda la landing |
| `CTASection` | `#contacto` — sección de la landing | Información de contacto + mapa de ubicación |
| `WhatsAppCTA` | `app/pedido/[orderId]/page.tsx` | Confirmación de pedido vía WhatsApp |

---

## 🔧 Componente 1 — `ContactWidget.tsx`

### 📍 Ubicación
```
app/components/widgets/ContactWidget.tsx
```

### 📖 Descripción
Widget flotante global que aparece en la esquina inferior derecha de la pantalla. Es un **botón circular con animación pulse** que al hacer clic abre un modal con dos canales de contacto. Implementa un flujo de **doble confirmación** antes de redirigir al usuario.

### 🔄 Flujo de Estados (State Machine)

```
Estado inicial
     │
     ▼
[isVisible=true] ──── Scroll hacia abajo ────► [isVisible=false]
     │                                               │
     │              Scroll hacia arriba ◄────────────┘
     │
     ▼
[isOpen=false] ──── Click en botón ────► [isOpen=true] → Modal principal
                                                │
                                    ┌───────────┴───────────┐
                                    ▼                       ▼
                           Click WhatsApp             Click Email
                                    │                       │
                                    ▼                       ▼
                     [confirmingChannel='whatsapp'] [confirmingChannel='email']
                                    │                       │
                              Confirmar                Confirmar
                                    │                       │
                                    ▼                       ▼
                           wa.me/51933248169        mailto:aleidanoriegamunoz
                           (nueva pestaña)          (cliente de correo)
```

### 🗂️ Estado React

```typescript
type ContactChannel = 'whatsapp' | 'email' | null;

const [isOpen, setIsOpen] = useState(false);
const [confirmingChannel, setConfirmingChannel] = useState<ContactChannel>(null);
const [isVisible, setIsVisible] = useState(true);
const lastScrollYRef = useRef(0);  // ref para evitar re-renders en scroll
```

### 📏 Lógica de Visibilidad por Scroll

```typescript
useEffect(() => {
  let ticking = false;  // Throttle con requestAnimationFrame

  const handleScroll = () => {
    if (!ticking) {
      requestAnimationFrame(() => {
        const currentScrollY = window.scrollY;

        // Mostrar: scroll hacia arriba O posición < 100px
        if (currentScrollY < lastScrollYRef.current || currentScrollY < 100) {
          setIsVisible(true);
        }
        // Ocultar: scroll hacia abajo Y posición > 100px
        else if (currentScrollY > lastScrollYRef.current && currentScrollY > 100) {
          setIsVisible(false);
        }

        lastScrollYRef.current = currentScrollY;
        ticking = false;
      });
      ticking = true;
    }
  };

  window.addEventListener('scroll', handleScroll, { passive: true });
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

> **💡 Decisión técnica:** Se usa `{ passive: true }` en el listener para no bloquear el thread principal, y `requestAnimationFrame` para sincronizar con el ciclo de renderizado del navegador, evitando layout thrashing.

### 🎬 Animaciones con Framer Motion

#### Botón flotante
```typescript
<motion.button
  initial={{ scale: 0, opacity: 0, y: 100 }}
  animate={{
    scale: isVisible ? 1 : 0,
    opacity: isVisible ? 1 : 0,
    y: isVisible ? 0 : 100
  }}
  transition={{ duration: 0.3, ease: 'easeInOut' }}
  whileHover={{ scale: 1.1 }}
  whileTap={{ scale: 0.95 }}
>
```

#### Modal principal
```typescript
<motion.div
  initial={{ opacity: 0, scale: 0.9, y: 20 }}
  animate={{ opacity: 1, scale: 1, y: 0 }}
  exit={{ opacity: 0, scale: 0.9, y: 20 }}
  transition={{ type: 'spring', duration: 0.5 }}
>
```

#### Modal de confirmación
```typescript
<motion.div
  initial={{ opacity: 0, scale: 0.9 }}
  animate={{ opacity: 1, scale: 1 }}
  exit={{ opacity: 0, scale: 0.9 }}
  transition={{ type: 'spring', duration: 0.4 }}
>
```

> **💡 Decisión técnica:** Se usa `AnimatePresence` para gestionar el montaje/desmontaje con animaciones de salida (`exit`). Sin esto, los modales desaparecerían abruptamente.

### 📲 Construcción de URLs de Contacto

#### WhatsApp
```typescript
const WHATSAPP_NUMBER = '51933248169';  // Código de Perú (+51) + número

const MESSAGE = `Hola bienvenid@s 👋
Este es nuestro canal de atención al cliente...
Esa Care
Atención al Cliente`;

const handleConfirmWhatsApp = () => {
  const encodedMessage = encodeURIComponent(MESSAGE);
  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${encodedMessage}`, '_blank');
};
```

#### Email
```typescript
const EMAIL = 'aleidanoriegamunoz@gmail.com';

const handleConfirmEmail = () => {
  const subject = encodeURIComponent('Consulta - ALENOR');
  const body = encodeURIComponent(MESSAGE);
  window.location.href = `mailto:${EMAIL}?subject=${subject}&body=${body}`;
};
```

> **💡 Decisión técnica:** WhatsApp abre en nueva pestaña (`window.open`) para no interrumpir la navegación. Email usa `window.location.href` porque el protocolo `mailto:` no genera navegación real — simplemente delega al SO.

### 🎨 Diseño Visual

| Elemento | Tamaño Mobile | Tamaño Desktop | Color |
|---|---|---|---|
| Botón flotante | 56×56px (`w-14`) | 64×64px (`w-16`) | Gradiente `#25D366 → #128C7E` |
| Pulse ring | — | — | `#25D366` con `animate-ping` al 20% de opacidad |
| Tooltip | Oculto (`hidden`) | Visible en hover | Blanco con sombra |
| Modal ancho | `calc(100% - 2rem)` | `max-w-md (448px)` | — |

### ⚙️ Carga en `app/page.tsx`

```typescript
// Dynamic import — no afecta el bundle inicial
const ContactWidget = dynamic(() => import('./components/widgets/ContactWidget'));

// Renderizado fuera del <main>, al nivel del layout
<ContactWidget />
```

---

## 🏔️ Componente 2 — `CTASection.tsx`

### 📍 Ubicación
```
app/components/sections/CTA/CTASection.tsx
```
Renderizado en: `app/page.tsx` → `<div id="contacto">`

### 📖 Descripción
Sección de la landing page con un **layout de dos columnas** (stack en mobile, side-by-side en desktop) que combina información de la marca, CTAs de conversión, elementos de confianza, y un **mapa de Google Maps** embebido con la ubicación física del negocio.

### 🗺️ Layout de Columnas

```
┌─────────────────────────────────────────────────────────┐
│  DESKTOP (lg: grid-cols-2)                              │
│  ┌──────────────────────┐  ┌──────────────────────────┐ │
│  │  Columna Izquierda   │  │   Columna Derecha        │ │
│  │  • Título            │  │   • Pin de ubicación     │ │
│  │  • Descripción       │  │   • Descripción          │ │
│  │  • 2 botones CTA     │  │   • Google Maps iframe   │ │
│  │  • Trust badges      │  │     (400px altura)       │ │
│  └──────────────────────┘  └──────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

MOBILE (stack vertical)
┌─────────────────────────┐
│  Columna izquierda      │
│  (centrado)             │
├─────────────────────────┤
│  Mapa                   │
└─────────────────────────┘
```

### 📌 Google Maps Embed

```typescript
// Coordenadas: Rioja, San Martín, Perú
// Lat: -5.986204, Lng: -77.282693

<iframe
  src="https://maps.google.com/maps?q=-5.986204,-77.282693&t=&z=16&ie=UTF8&iwloc=&output=embed"
  width="100%"
  height="400"
  style={{ border: 0 }}
  allowFullScreen
  loading="lazy"
  referrerPolicy="no-referrer-when-downgrade"
  title="Ubicación de Alenor"
/>
```

> **💡 Decisión técnica:** Se usa `loading="lazy"` para que el iframe no bloquee la carga inicial de la página. El atributo `referrerPolicy="no-referrer-when-downgrade"` es el estándar recomendado por Google Maps para embeds.

### 🏷️ Trust Badges (Elementos de Confianza)

```typescript
// Tres badges que refuerzan la propuesta de valor
const trustElements = [
  'Envío Gratuito +S/ 100',
  '100% Natural',
  'Hecho en Perú',
];
```

### 🎬 Animaciones de Entrada (Framer Motion)

```typescript
// Columna izquierda — entra desde la izquierda
initial={{ opacity: 0, x: -30 }}
whileInView={{ opacity: 1, x: 0 }}
viewport={{ once: true }}
transition={{ duration: 0.8 }}

// Columna derecha (mapa) — entra desde la derecha con delay
initial={{ opacity: 0, x: 30 }}
whileInView={{ opacity: 1, x: 0 }}
viewport={{ once: true }}
transition={{ duration: 0.8, delay: 0.2 }}

// Trust badges — fade in con delay mayor
initial={{ opacity: 0 }}
whileInView={{ opacity: 1 }}
viewport={{ once: true }}
transition={{ duration: 0.8, delay: 0.3 }}
```

> **💡 Decisión técnica:** `viewport={{ once: true }}` asegura que la animación solo se ejecuta una vez al hacer scroll, no cada vez que el elemento entra/sale del viewport. Esto mejora la percepción de fluidez.

### 🖌️ Fondo de la Sección

```css
/* Gradiente diagonal de colores de la marca */
bg-linear-to-br from-sage-primary via-sage-primary/95 to-tan-primary

/* Margin negativo para eliminar gap entre secciones */
-mt-1
```

---

## 📦 Componente 3 — `WhatsAppCTA.tsx`

### 📍 Ubicación
```
app/components/pedido/WhatsAppCTA.tsx
```

### 📖 Descripción
Botón contextual que aparece en la página de confirmación de pedido (`/pedido/[orderId]`). Genera dinámicamente un mensaje WhatsApp **estructurado y detallado** con toda la información del pedido para que el cliente lo envíe al equipo de ALENOR y confirme el pago.

### 🔗 Integración con `format.ts`

```typescript
import { buildWhatsAppUrl, buildOrderWhatsAppMessage } from '@/app/lib/format';
import type { Order } from '@/app/types/orders';

export default function WhatsAppCTA({ order }: WhatsAppCTAProps) {
  const baseUrl = typeof window !== 'undefined' ? window.location.origin : '';
  const message = buildOrderWhatsAppMessage(order, baseUrl);
  const url = buildWhatsAppUrl(message);
  // ...
}
```

> **💡 Decisión técnica:** `typeof window !== 'undefined'` protege contra errores de SSR — el componente es `'use client'` pero Next.js puede pre-renderizar en servidor.

### 📝 Estructura del Mensaje WhatsApp de Pedido

El mensaje se construye en `app/lib/format.ts` con `buildOrderWhatsAppMessage()`:

```
🌿 *ALENOR — Jabones Artesanales Naturales*
━━━━━━━━━━━━━━━━━━━━━━━

¡Hola! 👋 Acabo de realizar un pedido...

📋 *DETALLE DEL PEDIDO*
🔖 Pedido: *#A1B2C3D4*
📅 Fecha: 24 de marzo de 2026

🛒 *PRODUCTOS (2 artículos)*
───────────────────
   1. 🧼 *Jabón de Lavanda*
      Cant: 1 · Precio unit: S/ 15.00 · Subtotal: S/ 15.00
      🔗 https://alenor.store/images/...

   2. 🧼 *Jabón de Avena*
      Cant: 1 · Precio unit: S/ 18.00 · Subtotal: S/ 18.00
───────────────────

💰 *RESUMEN DE PAGO*
   Subtotal: S/ 33.00
   Envío: ✅ *Gratis*
   *TOTAL: S/ 33.00*

💳 *MÉTODO DE PAGO*
   📲 Yape

👤 *DATOS DEL CLIENTE*
   Nombre: Juan Pérez
   DNI: 12345678
   📞 Teléfono: 987654321
   📧 Email: juan@email.com

📦 *DIRECCIÓN DE ENVÍO*
   📍 Av. Principal 123
   Miraflores, Lima
   Lima

━━━━━━━━━━━━━━━━━━━━━━━
Quedo atento/a a la confirmación del pago. ¡Gracias! 🙏✨
```

### 🔤 Sistema de Emojis Unicode (Sin archivos externos)

```typescript
// Los emojis se generan en runtime desde code points para evitar
// problemas de encoding de archivos
const E = {
  leaf:      String.fromCodePoint(0x1F33F),  // 🌿
  wave:      String.fromCodePoint(0x1F44B),  // 👋
  clipboard: String.fromCodePoint(0x1F4CB),  // 📋
  soap:      String.fromCodePoint(0x1F9FC),  // 🧼
  money:     String.fromCodePoint(0x1F4B0),  // 💰
  // ...
};

const DASH = String.fromCodePoint(0x2501);   // ━
const LINE = String.fromCodePoint(0x2500);   // ─
const SEP_THICK = DASH.repeat(22);           // ━━━━━━━━━━━━━━━━━━━━━━━
const SEP_THIN  = LINE.repeat(19);           // ───────────────────
```

> **💡 Decisión técnica:** En lugar de escribir emojis directamente en el string, se usan `String.fromCodePoint()` para garantizar compatibilidad con cualquier configuración de encoding del proyecto, evitando posibles caracteres corruptos en distintos editores o sistemas operativos.

---

## 📐 Constantes Centralizadas — `constants.ts`

Toda la información de contacto del negocio está **centralizada en un único lugar**:

```typescript
// app/lib/constants.ts
export const BUSINESS = {
  name:       'ALENOR',
  slogan:     'Cuidamos tu piel, cuidamos el planeta',
  whatsapp:   '51933248169',   // Código Perú (+51) + número
  email:      'aleidanoriegamunoz@gmail.com',
  location:   'Rioja, San Martín, Perú',
  courier:    'Shalom',
  courierUrl: 'https://shalom.com.pe/',
} as const;
```

> **💡 Decisión técnica:** El `as const` convierte el objeto a un tipo readonly con valores literales, lo que permite usar los valores como tipos en TypeScript y evita mutaciones accidentales.

> ⚠️ **Nota:** `ContactWidget.tsx` aún tiene el número de WhatsApp y email hardcodeados (`WHATSAPP_NUMBER = '51933248169'`). La refactorización pendiente es importar desde `BUSINESS` para unificar la fuente de verdad.

---

## 📱 Responsividad

### ContactWidget

| Viewport | Botón | Tooltip | Modal Ancho |
|---|---|---|---|
| Mobile (`< sm`) | 56×56px | Oculto | `calc(100% - 2rem)` |
| Tablet (`sm`) | 64×64px | Oculto | `max-w-md` |
| Desktop (`lg+`) | 64×64px | Visible en hover | `max-w-md` |

### CTASection

| Viewport | Layout | Mapa |
|---|---|---|
| Mobile | 1 columna, centrado | 400px altura |
| Desktop (`lg+`) | 2 columnas, alineadas | 400px altura |

---

## ⚡ Performance

| Optimización | Implementación |
|---|---|
| **Code splitting** | `ContactWidget` cargado con `dynamic()` — no afecta el bundle inicial |
| **Scroll throttle** | `requestAnimationFrame` + flag `ticking` — evita cálculos en cada frame |
| **Passive listener** | `{ passive: true }` en scroll — no bloquea el thread principal |
| **Lazy map** | `loading="lazy"` en iframe — carga Google Maps solo cuando está visible |
| **SSR guard** | `typeof window !== 'undefined'` en `WhatsAppCTA` — protege pre-rendering |
| **`once: true`** | Animaciones Framer Motion solo se ejecutan una vez por elemento |

---

## 🔐 Seguridad

| Punto | Implementación |
|---|---|
| **`rel="noopener noreferrer"`** | En el link de WhatsAppCTA — evita que la nueva pestaña acceda a `window.opener` |
| **`encodeURIComponent()`** | Todos los mensajes y subjects son codificados antes de incluirse en URLs |
| **`referrerPolicy="no-referrer-when-downgrade"`** | Estándar recomendado para iframes de Maps — no expone URL completa en peticiones cross-origin |

---

## 🧩 Dependencias

| Librería | Uso en el sistema de contacto |
|---|---|
| `framer-motion` | Animaciones de botón, modales, confirmación, entradas de sección |
| `lucide-react` | Iconos: `MessageCircle`, `Mail`, `X`, `ExternalLink`, `ArrowRight`, `Zap`, `Sparkles`, `MapPin` |
| `next/image` | Logo de ALENOR en el header del modal |
| `next/dynamic` | Code splitting del ContactWidget |

---

## 🗺️ Diagrama de Flujo Completo — Usuario

```
Usuario visita alenor.store
         │
         ▼
¿Tiene duda durante navegación?
    │               │
   SÍ              NO
    │               │
    ▼               ▼
Hace clic en    Llega a sección
botón verde     #contacto (CTA)
flotante            │
    │               ├── Lee info de la marca
    ▼               ├── Ve mapa de ubicación
Abre modal          └── Botón "Contactar"
de canales              │
    │                   │
    ▼                   ▼
Elige canal:
 ┌──────────┐     ┌──────────┐
 │ WhatsApp │     │  Email   │
 └──────────┘     └──────────┘
      │                │
      ▼                ▼
  Confirma         Confirma
      │                │
      ▼                ▼
 wa.me/51...      mailto:...
 (nueva pestaña)  (cliente correo)
      │
      ▼
¿Realizó un pedido?
    │
   SÍ
    │
    ▼
Página /pedido/[id]
    │
    ▼
Botón "Confirmar pago por WhatsApp"
    │
    ▼
Mensaje pre-cargado con
TODOS los datos del pedido
    │
    ▼
Envía a +51 933 248 169
```

---

## ✅ Checklist de Implementación

- [x] Widget flotante con detección de scroll bidireccional
- [x] Modal de selección de canal con animaciones spring
- [x] Flujo de doble confirmación antes de redirigir
- [x] Generación dinámica de URLs con `encodeURIComponent`
- [x] Mensaje de WhatsApp pre-escrito para consultas generales
- [x] Sección CTA con mapa de Google Maps embebido
- [x] Animaciones de entrada con `whileInView` y `once: true`
- [x] Mensaje de pedido estructurado con emojis Unicode
- [x] Protección SSR en `WhatsAppCTA`
- [x] `passive: true` + `requestAnimationFrame` para scroll optimizado
- [x] Constants centralizadas en `BUSINESS` object
- [ ] ⚠️ Pendiente: Unificar número/email en `ContactWidget` desde `BUSINESS` (actualmente hardcodeados)

---

*Documentado por Miller Zamora · ALENOR · 2026-03-24*

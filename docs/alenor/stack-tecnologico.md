# 🧱 Stack Tecnológico – ALENOR

> **Documento:** Product Requirements Document (PRD) – Tecnologías  
> **Proyecto:** ALENOR – Tienda de Jabones Artesanales Naturales  
> **URL producción:** [alenor.store](https://alenor.store)  
> **Fecha:** Marzo 2026

---

## 📋 Resumen ejecutivo

ALENOR es una tienda e-commerce de jabones artesanales 100% naturales dirigida al mercado peruano. El stack fue elegido priorizando **rendimiento**, **experiencia visual inmersiva** y **SEO técnico avanzado**, sin requerir base de datos ni backend complejo.

---

## 🗂️ Tabla de dependencias

### Producción (`dependencies`)

| Paquete | Versión | Rol |
|---|---|---|
| `next` | `16.1.6` | Framework principal – App Router |
| `react` | `19.2.3` | UI declarativa |
| `react-dom` | `19.2.3` | Renderizado DOM |
| `three` | `^0.182.0` | Renderizado 3D WebGL |
| `@types/three` | `^0.182.0` | Tipos TypeScript para Three.js |
| `gsap` | `^3.14.2` | Animaciones de alta performance |
| `framer-motion` | `^12.33.0` | Animaciones de UI / transiciones |
| `zustand` | `^5.0.11` | Estado global del carrito |
| `react-hook-form` | `^7.71.1` | Gestión de formularios |
| `@hookform/resolvers` | `^5.2.2` | Bridge RHF ↔ Zod |
| `zod` | `^4.3.6` | Validación de esquemas (client + server) |
| `lucide-react` | `^0.563.0` | Sistema de íconos SVG |
| `@vercel/analytics` | `^1.6.1` | Analytics de visitas |
| `@vercel/speed-insights` | `^1.3.1` | Métricas de performance |

### Desarrollo (`devDependencies`)

| Paquete | Versión | Rol |
|---|---|---|
| `typescript` | `^5` | Tipado estático estricto |
| `tailwindcss` | `^4` | Utility-first CSS framework |
| `@tailwindcss/postcss` | `^4` | Integración PostCSS para Tailwind v4 |
| `eslint` | `^9` | Linting (flat config) |
| `eslint-config-next` | `16.1.6` | Reglas Next.js + core-web-vitals |
| `@types/node` | `^20` | Tipos Node.js |
| `@types/react` | `^19` | Tipos React 19 |
| `@types/react-dom` | `^19` | Tipos ReactDOM |

---

## ⚙️ Core Framework

### Next.js 16 – App Router

```bash
# Comandos del proyecto
npm run dev      # Servidor de desarrollo → http://localhost:3000
npm run build    # Build de producción (SWC compiler)
npm run start    # Servidor de producción
npm run lint     # ESLint con flat config
```

**Características usadas:**

- **App Router** (`app/`) con layouts anidados por ruta
- **Route Handlers** (`app/api/orders/route.ts`) — API REST nativa
- **File-based metadata** (`sitemap.ts`, `robots.ts`, `opengraph-image.tsx`)
- **Next Image** — optimización automática con AVIF/WebP, caché de 1 año
- **Dynamic imports** con `ssr: false` para componentes Three.js
- **`next/font/google`** — fuentes con zero layout shift

```ts
// next.config.ts — configuración de imágenes optimizada
images: {
  formats: ['image/avif', 'image/webp'],
  minimumCacheTTL: 60 * 60 * 24 * 365, // 1 año
  deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
}
```

---

## 🎨 Estilos – Tailwind CSS v4

La versión 4 de Tailwind introduce cambios fundamentales respecto a v3:

- **Sin `tailwind.config.js`** — configuración enteramente en CSS
- **Tokens del tema** definidos con `@theme` en `globals.css`
- **Integración vía PostCSS** (`@tailwindcss/postcss`)

```css
/* globals.css — tema custom con @theme */
@theme {
  --color-primary: ...;
  --font-sans: var(--font-geist-sans);
}
```

---

## 🔤 Tipografía – Geist (Vercel)

```ts
// app/layout.tsx
import { Geist, Geist_Mono } from "next/font/google";

const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
  display: "swap",
});
```

| Variable CSS | Fuente | Uso |
|---|---|---|
| `--font-geist-sans` | Geist Sans | Texto general, UI |
| `--font-geist-mono` | Geist Mono | Código, etiquetas técnicas |

---

## 📦 Visualizador 3D – `packaging-3d/`

El componente más complejo del proyecto: una simulación interactiva de packaging con PBR rendering.

### Módulos del sistema 3D

| Archivo | Responsabilidad |
|---|---|
| `PackagingViewer.tsx` | Wrapper con dynamic import, guard SSR, fallback WebGL |
| `PackagingScene.tsx` | Componente cliente principal — inicializa escena, parallax mouse |
| `SceneManager.ts` | Renderer, cámara, loop de animación, resize handler |
| `Lights.ts` | Iluminación multi-capa: key/fill/accent/hemisphere/rim + presets |
| `PackagingBox.ts` | Geometría dieline con pivot groups por panel + sistema de flaps |
| `Soap.ts` | Jabón glicerina translúcido (MeshPhysicalMaterial con transmission) |
| `FoldAnimation.ts` | Timeline GSAP: plano → caja ensamblada con movimientos de cámara |
| `PostProcessing.ts` | EffectComposer: bloom + viñeta |
| `ProceduralHDRI.ts` | Genera environment map proceduralmente (sin archivos HDRI externos) |
| `ControlsOverlay.tsx` | Overlay Framer Motion: tarjetas skin, presets de luz, botón animar |

### Decisiones de diseño clave

```
Box dieline → nested THREE.Group pivots
  └─ Cada panel tiene su group con origen en el borde de doblez (hinge)
  └─ Side flaps anidados dentro de pivots front/back (se mueven con su padre)
  └─ Top panel anidado dentro del pivot back (se dobla desde el borde superior)

Texturas → Canvas procedural (sin assets externos)
Environment → PMREMGenerator procedural
Rendering → Solo client-side (dynamic import ssr:false)
```

### Stack 3D

| Tecnología | Versión | Uso |
|---|---|---|
| `three` | `^0.182.0` | Escena, geometrías, materiales PBR |
| `gsap` | `^3.14.2` | Timeline de animación de plegado |
| `framer-motion` | `^12.33.0` | Overlay de controles UI |

---

## 🛒 Estado Global – Zustand

Carrito persistido en `localStorage` mediante middleware `persist`:

```ts
// app/lib/cart-store.ts
export const useCartStore = create<CartStore>()(
  persist(
    (set, get) => ({ ... }),
    {
      name: CART.storageKey,
      partialize: (state) => ({ items: state.items }), // solo persiste items
    }
  )
);
```

**Acciones del store:**

| Acción | Descripción |
|---|---|
| `addItem` | Agrega producto (respeta `maxQuantity`) |
| `removeItem` | Elimina item por ID |
| `updateQuantity` | Actualiza cantidad o elimina si ≤ 0 |
| `clearCart` | Vacía el carrito |
| `openDrawer / closeDrawer` | Controla visibilidad del CartDrawer |
| `getSubtotal / getShipping / getTotal` | Computed values |

---

## 📝 Formularios y Validación – React Hook Form + Zod

```ts
// Flujo de validación dual (client + server)
// Client: RHF + zodResolver en CheckoutForm.tsx
// Server: zod.safeParse en app/api/orders/route.ts

const customerInfoSchema = z.object({ ... });
const result = customerInfoSchema.safeParse(body.customer);
```

**Ventajas del stack:**

- **Zero re-renders** en escritura gracias a RHF con `register` uncontrolled
- **Mismo schema Zod** reutilizado en frontend y API Route
- **Mensajes de error tipados** con `zodResolver`

---

## 🔌 API – Route Handlers (Next.js)

```
POST /api/orders
  ├─ Valida datos del cliente (Zod)
  ├─ Lee productos desde /public/data/products.json (fs/promises)
  ├─ Recalcula precios server-side (previene manipulación)
  ├─ Valida stock y cantidad (1–20 por producto)
  ├─ Calcula shipping (gratis sobre umbral)
  └─ Retorna Order con UUID generado por crypto.randomUUID()
```

**No hay base de datos** — productos almacenados en JSON estático, órdenes retornadas al cliente para flujo WhatsApp.

---

## 🔍 SEO Técnico

### Metadata API (Next.js)

```ts
// app/layout.tsx — metadata completa
export const metadata: Metadata = {
  title: { default: "...", template: "%s | ALENOR" },
  openGraph: { type: "website", locale: "es_PE", ... },
  twitter: { card: "summary_large_image", ... },
  robots: { index: true, follow: true, googleBot: { ... } },
  alternates: { canonical: SITE_URL, languages: { "es-PE": SITE_URL } },
};
```

### JSON-LD Structured Data

Tres schemas implementados en `<script type="application/ld+json">`:

| Schema | Propósito |
|---|---|
| `Organization` | Identidad de marca |
| `WebSite` | Habilita Google Sitelinks Search Box |
| `LocalBusiness + Store` | Rich results para búsquedas locales |

### Archivos SEO generados dinámicamente

| Archivo | Ruta | Propósito |
|---|---|---|
| `sitemap.ts` | `/sitemap.xml` | Indexación automática |
| `robots.ts` | `/robots.txt` | Directivas de crawlers |
| `opengraph-image.tsx` | `/opengraph-image` | OG image dinámica |

---

## 📊 Observabilidad – Vercel

| Servicio | Paquete | Qué mide |
|---|---|---|
| Analytics | `@vercel/analytics` | Pageviews, unique visitors, referrers |
| Speed Insights | `@vercel/speed-insights` | Core Web Vitals (LCP, FID, CLS) en producción |

```tsx
// app/layout.tsx — montados en el RootLayout
<Analytics />
<SpeedInsights />
```

---

## 🏗️ Arquitectura de archivos

```
alenor/
├── app/
│   ├── api/orders/route.ts       # API Route – procesamiento de órdenes
│   ├── components/
│   │   ├── packaging-3d/         # Sistema 3D completo (Three.js + GSAP)
│   │   ├── cart/                 # CartDrawer, CartIcon, CartItem, CartSummary
│   │   ├── checkout/             # CheckoutForm, CheckoutPage, OrderSummary
│   │   ├── catalogo/             # CatalogPage, CatalogProductCard, FilterSidebar
│   │   ├── sections/             # Hero, Products, Benefits, FAQ, Aromas, etc.
│   │   ├── layout/               # Header/Navbar, Footer
│   │   ├── ui/                   # Badge, Button, Card, FilterChip, etc.
│   │   └── widgets/              # ContactWidget, WhatsAppCTA
│   ├── lib/
│   │   ├── cart-store.ts         # Zustand store (persistido)
│   │   ├── animations.ts         # Variantes Framer Motion reutilizables
│   │   ├── constants.ts          # SHIPPING, CART, etc.
│   │   ├── validators.ts         # Schemas Zod compartidos
│   │   ├── format.ts             # Formateo de precios/fechas
│   │   └── products.ts           # Helpers de productos
│   ├── types/
│   │   ├── products.ts           # Product, CartItem, ProductsData
│   │   └── orders.ts             # Order, OrderItem, CustomerInfo
│   ├── catalogo/page.tsx         # /catalogo — catálogo completo
│   ├── checkout/page.tsx         # /checkout — formulario de compra
│   ├── pedido/[orderId]/page.tsx # /pedido/:id — confirmación
│   ├── layout.tsx                # RootLayout + metadata + JSON-LD
│   ├── page.tsx                  # / — landing page
│   ├── sitemap.ts                # /sitemap.xml dinámico
│   ├── robots.ts                 # /robots.txt
│   └── opengraph-image.tsx       # OG image generada por Next.js
├── public/data/products.json     # Catálogo de productos (fuente de verdad)
├── next.config.ts                # Config Next.js (imágenes, compresión, etags)
├── tsconfig.json                 # TypeScript strict + alias @/*
├── postcss.config.mjs            # PostCSS con @tailwindcss/postcss
└── eslint.config.mjs             # ESLint flat config
```

---

## 🔧 TypeScript – Configuración

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2017",
    "strict": true,               // modo estricto completo
    "moduleResolution": "bundler", // resolución moderna
    "incremental": true,           // builds incrementales
    "paths": { "@/*": ["./*"] }    // alias para imports limpios
  }
}
```

---

## 🚀 Deploy – Vercel

| Aspecto | Configuración |
|---|---|
| **Platform** | Vercel (integración nativa Next.js) |
| **Compresión** | `compress: true` en next.config.ts |
| **Headers** | `poweredByHeader: false` (no expone X-Powered-By) |
| **ETags** | `generateEtags: true` (caché HTTP eficiente) |
| **Dominio** | `alenor.store` (canonical, sin www) |
| **Imágenes** | Optimización automática Vercel Image Optimization |

---

## 📌 Decisiones de arquitectura destacadas

1. **Sin base de datos** — Productos en JSON estático (`public/data/products.json`), órdenes procesadas en memory y enviadas por WhatsApp. Reduce costos y complejidad operacional.

2. **Precios recalculados server-side** — El API Route lee precios desde el JSON, nunca confía en los precios enviados por el cliente, previniendo manipulación.

3. **Three.js solo client-side** — `dynamic(() => import(...), { ssr: false })` garantiza que WebGL nunca se intente en servidor.

4. **Schema Zod compartido** — El mismo `customerInfoSchema` valida en el formulario (RHF) y en la API Route, eliminando duplicación de reglas.

5. **Tailwind v4 sin config file** — Tokens definidos en CSS con `@theme`, más cercano al estándar web y sin overhead de configuración JS.

6. **Zustand con `partialize`** — Solo `items` se persiste en localStorage, el estado de UI (drawer abierto) se resetea en cada sesión.

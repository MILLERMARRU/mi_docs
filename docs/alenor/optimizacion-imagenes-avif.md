# 🖼️ Optimización de Imágenes AVIF – ALENOR

> **Documento:** PRD – Pipeline de Conversión de Imágenes  
> **Proyecto:** ALENOR – alenor.store  
> **Fecha:** Marzo 2026

---

## 📋 Resumen ejecutivo

ALENOR sirve imágenes en **formato AVIF** (con fallback a WebP) de forma automática gracias a **Next.js Image Optimization**. Las imágenes fuente son `.jpg` y `.jpeg` almacenadas en `/public`, y se convierten al vuelo en el servidor de Vercel — sin ningún paso de build manual ni herramienta externa.

---

## 🤔 ¿Por qué AVIF?

| Formato | Compresión vs JPEG | Soporte browsers | Transparencia |
|---|---|---|---|
| **JPEG** | baseline (0%) | Universal | ❌ |
| **WebP** | ~30% menor | 96%+ | ✅ |
| **AVIF** | ~50% menor | 93%+ | ✅ |
| **PNG** | mayor tamaño | Universal | ✅ |

> AVIF ofrece la mejor relación **calidad / peso** disponible hoy. En imágenes de productos (jabones con texturas naturales), el ahorro típico es del **40–60%** respecto a JPEG al mismo nivel de calidad percibido.

---

## ⚙️ Configuración del pipeline

### `next.config.ts` — fuente de verdad

```ts
// next.config.ts
const nextConfig: NextConfig = {
  images: {
    unoptimized: false,                          // ✅ Optimización activa
    formats: ['image/avif', 'image/webp'],       // Prioridad: AVIF → WebP → original
    qualities: [60, 70, 75, 80, 85, 90],         // Variantes de calidad precacheadas
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840], // Breakpoints responsive
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],            // Tamaños para fill/fixed
    minimumCacheTTL: 60 * 60 * 24 * 365,         // Cache CDN: 1 año
    dangerouslyAllowSVG: true,                   // SVG permitidos (logo)
    contentDispositionType: 'attachment',
    contentSecurityPolicy: "default-src 'self'; script-src 'none'; sandbox;",
    remotePatterns: [
      { protocol: 'https', hostname: 'i.ytimg.com' }, // YouTube thumbnails
    ],
  },
  compress: true,
  generateEtags: true,
};
```

---

## 🔄 Flujo de conversión paso a paso

```
Browser solicita imagen
        │
        ▼
┌─────────────────────────────┐
│  Next.js /_next/image        │
│  endpoint (Image API)        │
└────────────┬────────────────┘
             │
             ▼
    ¿Soporta AVIF? (Accept header)
    ├── SÍ  → Convierte a AVIF + quality param
    └── NO  → ¿Soporta WebP?
              ├── SÍ  → Convierte a WebP + quality param
              └── NO  → Sirve original (JPEG/PNG)
             │
             ▼
    ¿Existe en caché CDN? (1 año TTL)
    ├── SÍ  → Sirve desde edge cache (0ms processing)
    └── NO  → Convierte, guarda en caché, sirve
             │
             ▼
    Responde con imagen optimizada
    + headers: Cache-Control, Content-Type, ETag
```

---

## 📐 URL generada por Next.js Image

Cada `<Image>` genera una URL como esta en el HTML final:

```
/_next/image?url=%2Fjabones%2Flavanda.jpg&w=828&q=85
              │                            │    │
              └─ path del original         │    └─ quality (85)
                                           └─ width solicitado (breakpoint)
```

El servidor de Next.js/Vercel intercepta esta URL, aplica la conversión y responde con:

```
Content-Type: image/avif          ← formato negociado
Cache-Control: public, max-age=31536000, immutable
ETag: "abc123"                    ← para revalidación condicional
Vary: Accept                      ← esencial para no mezclar AVIF con JPEG en CDN
```

---

## 🧩 Uso de `<Image>` en los componentes

### ProductCard — Tarjetas de productos

```tsx
// app/components/sections/Products/ProductCard.tsx
<Image
  src={product.image}           // "/jabones/lavanda.jpg"
  alt={product.name}
  fill                          // position: absolute, cubre el contenedor
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  priority={product.featured || aboveFold}   // LCP: carga eager si es above-fold
  loading={product.featured || aboveFold ? 'eager' : 'lazy'}
  quality={85}                  // Q85 → AVIF ~40–50KB para 400px
/>
```

### CatalogProductCard — Catálogo

```tsx
// app/components/catalogo/CatalogProductCard.tsx
<Image
  src={product.image}
  alt={product.name}
  fill
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  priority={priority}           // prop controlado por el padre
  quality={85}
/>
```

### IngredientsShowcase — Sección ingredientes

```tsx
// app/components/sections/Ingredients/IngredientsShowcase.tsx
<Image
  src={ingredient.image}        // "/ingredientes/avena.jpg"
  alt={ingredient.name}
  fill
  sizes="(max-width: 768px) 50vw, (max-width: 1200px) 33vw, 25vw"
  quality={85}
  // Sin priority → lazy load (están below-the-fold)
/>
```

---

## 📊 Tabla de imágenes del proyecto

### `/public/jabones/` — Productos principales

| Archivo fuente | Uso | Quality | Sizes hint |
|---|---|---|---|
| `lavanda.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `cafe.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `coco.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `avena.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `limon.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `miel.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `jazmin.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `eucalipto.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `rosas.jpeg` | ProductCard + CatalogCard | 85 | 33–100vw |
| `arroz_vainilla.jpg` | ProductCard + CatalogCard | 85 | 33–100vw |

### `/public/ingredientes/` — Sección ingredientes

| Archivo fuente | Ingrediente |
|---|---|
| `avena.jpg` | Avena |
| `cacao.jpeg` | Cacao |
| `cafe.jpg` | Café |
| `coco.jpg` | Coco |
| `almedran.jpg` | Almendra |
| `arroz.jpg` | Arroz |
| `manzanilla.jpg` | Manzanilla |

### `/public/esencias/` — Sección aromas

| Archivo fuente | Aroma |
|---|---|
| `lavanda.jpg` | Lavanda |
| `cafe.jpg` | Café |
| `coco.jpg` | Coco |
| `jazmin.jpg` | Jazmín |
| `limon.jpg` | Limón |
| `miel.jpg` | Miel |
| `rosas.jpg` | Rosas |
| `vainilla.jpg` | Vainilla |
| `eucalipto.jpg` | Eucalipto |

### `/public/merchandising/` — Rewards section

Bolsos (6), polos (5), tazas (6) — todos `.jpg`

---

## 🎯 Estrategia de `sizes` y breakpoints

El atributo `sizes` es clave para que el browser descargue el breakpoint correcto:

```
Pantalla < 768px  →  100vw  →  Next.js elige w=750 o w=828
Pantalla < 1200px →  50vw   →  Next.js elige w=640 o w=750
Pantalla ≥ 1200px →  33vw   →  Next.js elige w=384 o w=640
```

Los `deviceSizes` configurados (`[640, 750, 828, 1080, 1200, 1920, 2048, 3840]`) actúan como la malla de resoluciones disponibles — Next.js siempre sube al tamaño más cercano por encima del solicitado.

---

## 📈 Variantes de calidad precacheadas

```ts
qualities: [60, 70, 75, 80, 85, 90]
```

Estas son las calidades que Vercel pre-cachea en el CDN. Los componentes de ALENOR usan **`quality={85}`** de forma consistente, lo que garantiza:

- Calidad visual alta (jabones con texturas visibles)
- No se generan variantes innecesarias (ej: q=73 crearía una nueva entrada de caché)

---

## 🚀 Caché y rendimiento

### Headers HTTP de una imagen optimizada

```http
HTTP/2 200
content-type: image/avif
cache-control: public, max-age=31536000, immutable
etag: "d3b2a1f9e8c7"
vary: Accept
x-vercel-cache: HIT
```

### TTL configurado: 1 año

```ts
minimumCacheTTL: 60 * 60 * 24 * 365  // = 31,536,000 segundos
```

Esto significa que una vez generada la variante AVIF de una imagen, **Vercel la sirve desde el edge** sin volver a procesar durante 365 días. La invalidación ocurre automáticamente si la URL cambia (por ejemplo, si se cambia el archivo fuente en `/public`).

---

## ⚡ Optimización LCP (Largest Contentful Paint)

Los productos `featured` y las cards `aboveFold` cargan con `priority={true}`:

```tsx
// ProductCard.tsx
priority={product.featured || aboveFold}
loading={product.featured || aboveFold ? 'eager' : 'lazy'}
```

Esto instruye a Next.js a:
1. **Inyectar `<link rel="preload" as="image">`** en el `<head>` para esa imagen
2. **Omitir el lazy loading** (sin `loading="lazy"` en el `<img>` generado)
3. Garantizar que el LCP de la landing page no sea bloqueado por carga tardía

---

## 🔗 Imágenes remotas (YouTube)

```ts
// next.config.ts
remotePatterns: [
  { protocol: 'https', hostname: 'i.ytimg.com' }
]
```

Los thumbnails de YouTube usados en `YouTubeEmbed.tsx` también pasan por el mismo pipeline de optimización AVIF — Next.js los descarga, convierte y cachea igual que los locales.

---

## 📋 Imagen Open Graph — Caso especial

```ts
// app/opengraph-image.tsx
export const runtime = 'edge';
export const contentType = 'image/png';   // ← PNG, no AVIF
export const size = { width: 1200, height: 630 };
```

La OG image es generada dinámicamente por `next/og` (`ImageResponse`) con **runtime Edge** y sirve como **PNG** — los crawlers de redes sociales (Facebook, Twitter/X, WhatsApp) no soportan AVIF, por lo que este caso queda fuera del pipeline de conversión intencional.

---

## ✅ Checklist de buenas prácticas aplicadas

- [x] `formats: ['image/avif', 'image/webp']` — negociación por `Accept` header
- [x] `unoptimized: false` — optimización activa en todos los entornos
- [x] `quality={85}` consistente en todos los `<Image>` del proyecto
- [x] `sizes` definido en cada `<Image>` (evita descargar imágenes sobre-dimensionadas)
- [x] `priority={true}` en imágenes above-the-fold (LCP)
- [x] `loading="lazy"` por defecto en imágenes below-the-fold
- [x] `minimumCacheTTL` de 1 año (máximo aprovechamiento de CDN)
- [x] `fill` + contenedor `aspect-square` (previene layout shift / CLS = 0)
- [x] `alt` descriptivo en todos los `<Image>` (accesibilidad + SEO)
- [x] `Vary: Accept` en respuestas (CDN no mezcla AVIF con WebP)

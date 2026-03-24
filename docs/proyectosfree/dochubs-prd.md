# PRD – DocHubs

> **Versión:** 1.0  
> **Fecha:** 2026-03-24  
> **Autores:** Miller Zamora · Sam Vasquez  
> **Estado:** Activo

---

## 1. Resumen ejecutivo

DocHubs es una plataforma de documentación colaborativa diseñada para equipos de desarrollo. Permite escribir, publicar y compartir documentación técnica en Markdown de forma instantánea, sin necesidad de una base de datos propia. Todo el contenido persiste como archivos `.md` en un repositorio de GitHub externo, lo que garantiza versionado automático, acceso universal y cero vendor lock-in.

---

## 2. Problema

Los equipos de desarrollo necesitan mantener documentación actualizada, accesible y bien organizada. Las soluciones existentes presentan alguno de estos problemas:

- **Demasiado pesadas** (Confluence, Notion): caro, lento, difícil de integrar con el flujo de trabajo de dev.
- **Demasiado simples** (wikis de GitHub): sin búsqueda potente, sin layouts ricos, sin control de acceso granular.
- **Sin versionado real**: los cambios no quedan trazados con commits.

DocHubs resuelve esto con una solución ligera, dev-first, que usa GitHub como fuente de verdad.

---

## 3. Objetivos del producto

| # | Objetivo | Métrica de éxito |
|---|---|---|
| 1 | Publicar un doc en < 30 segundos | Tiempo medido desde clic hasta visible en `/docs` |
| 2 | Búsqueda instantánea client-side | Resultados en < 100ms |
| 3 | Zero downtime en actualizaciones | ISR cada 120s sin rebuilds manuales |
| 4 | Soporte completo de imágenes en docs | Subida directa desde el editor al repo GitHub |
| 5 | Acceso multi-admin con aislamiento por usuario | Cada admin solo edita/elimina sus propios docs |

---

## 4. Usuarios objetivo

### Administradores (writers)
- Developers del equipo con acceso al panel `/admin`.
- Crean, editan y eliminan documentación.
- Actualmente: `miller` y `sam`.

### Lectores (readers)
- Cualquier persona con acceso a la URL pública.
- Navegan por secciones, buscan docs, leen Markdown renderizado.
- No requieren cuenta.

---

## 5. Funcionalidades principales

### 5.1 Editor de documentación (`/admin`)
- Editor Markdown WYSIWYG con preview en tiempo real (split view).
- Auto-generación de slug desde el título (lowercase, sin tildes, guiones).
- Selección de sección existente o creación de nueva sección.
- Subida de imágenes directamente al repo GitHub (`docs/images/`).
- Carga de archivos `.md` locales desde el filesystem.

### 5.2 Visualización de docs (`/docs`)
- Layout 3 columnas: **Sidebar** | **Contenido** | **TOC (tabla de contenidos)**.
- Syntax highlighting con `shiki` para bloques de código.
- Navegación prev/next entre documentos.
- Responsive: sidebar y TOC en drawers en mobile.

### 5.3 Búsqueda (`⌘K`)
- Búsqueda client-side sobre el índice completo.
- Filtra por título, descripción y tags.
- Modal activado con `Cmd+K` / `Ctrl+K`.

### 5.4 Autenticación
- Login con usuario y contraseña (bcrypt hash + JWT HS256).
- Cookie `docs_session` httpOnly, válida 8 horas.
- Middleware de protección en rutas `/admin` y APIs de escritura.

### 5.5 Control de propiedad
- Cada doc almacena `createdBy` (username del autor).
- Los admins solo pueden editar/eliminar sus propios documentos.
- El panel muestra únicamente los docs del usuario logueado.

### 5.6 Temas
- Dark / Light mode con `next-themes`.
- Toggle en la Navbar.

---

## 6. Arquitectura técnica

```
┌─────────────────────────────────────────────┐
│                  Vercel (CDN)                │
│   Next.js 16 App Router · ISR 120s          │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │  /docs   │  │  /admin  │  │  /api    │  │
│  │ (Server) │  │ (Server) │  │ (Routes) │  │
│  └──────────┘  └──────────┘  └──────────┘  │
└────────────────────────┬────────────────────┘
                         │ Octokit REST
                         ▼
             ┌───────────────────────┐
             │   GitHub Repository   │
             │  ├── index.json       │
             │  └── docs/            │
             │      ├── section/     │
             │      │   └── slug.md  │
             │      └── images/      │
             └───────────────────────┘
```

### Stack

| Capa | Tecnología |
|---|---|
| Framework | Next.js 16.1.6 (App Router) |
| UI | React 19 + Tailwind CSS 4 |
| Componentes | shadcn/ui (Radix UI) |
| Auth | jose (JWT HS256) + bcryptjs |
| Storage | GitHub API via @octokit/rest |
| Markdown | react-markdown + rehype-pretty-code + shiki |
| Búsqueda | cmdk |
| Deploy | Vercel |

---

## 7. Modelo de datos

### `index.json` (fuente de verdad del índice)

```json
{
  "sections": [
    {
      "id": "devops",
      "title": "DevOps",
      "order": 1,
      "items": [
        {
          "id": "docker-build",
          "title": "Docker Build",
          "slug": "devops/docker-build",
          "path": "docs/devops/docker-build.md",
          "description": "Guía de construcción de imágenes Docker.",
          "tags": ["docker", "ci"],
          "order": 1,
          "createdBy": "miller"
        }
      ]
    }
  ]
}
```

### Archivo `.md` del documento

- Ruta en repo: `docs/{sección}/{slug}.md`
- Formato: Markdown estándar + GFM (tablas, checkboxes, etc.)

---

## 8. Variables de entorno

| Variable | Descripción | Requerida |
|---|---|---|
| `GITHUB_TOKEN` | PAT con permisos `repo` | Sí |
| `GITHUB_OWNER` | Owner del repo de docs | Sí |
| `GITHUB_REPO` | Nombre del repo de docs | Sí |
| `GITHUB_BRANCH` | Rama (default: `main`) | No |
| `AUTH_SECRET` | Secret para firmar JWT | Sí |

---

## 9. Rutas de la aplicación

| Ruta | Acceso | Descripción |
|---|---|---|
| `/` | Público | Landing page |
| `/docs` | Público | Índice de documentación |
| `/docs/[...slug]` | Público | Documento específico |
| `/admin` | Admin | Panel de edición |
| `/admin/login` | Público | Formulario de login |
| `/team` | Público | Página del equipo |
| `POST /api/auth/login` | Público | Autenticación |
| `POST /api/auth/logout` | Auth | Cerrar sesión |
| `GET /api/docs/nav` | Público | Índice de navegación |
| `POST /api/docs/upsert` | Auth | Crear/actualizar documento |
| `POST /api/docs/upload-image` | Auth | Subir imagen al repo |

---

## 10. Restricciones y limitaciones conocidas

- **Sin DB propia:** toda la persistencia depende de la GitHub API. Rate limit: 5000 req/h con token.
- **ISR 120s:** los cambios en docs públicos tardan hasta 2 minutos en reflejarse sin revalidación manual.
- **Max payload:** 2 MB por request a la API de upsert.
- **Imágenes:** máx. 5 MB por archivo. Formatos: jpg, jpeg, png, gif, webp, avif, svg.
- **Slugs:** solo caracteres `[a-z0-9-/]` — sin mayúsculas, tildes ni espacios.
- **Admins:** actualmente limitado a 2 usuarios (`miller` y `sam`) definidos en `data/users.json`.

---

## 11. Roadmap futuro

| Prioridad | Feature |
|---|---|
| Alta | OAuth con GitHub (eliminar contraseñas locales) |
| Alta | Webhooks para auto-revalidación (cero delay en publicación) |
| Media | Full-text search con Algolia |
| Media | Versionado y diff de documentos |
| Baja | Comentarios en docs |
| Baja | Roles granulares (viewer / editor / admin) |

---

## 12. Criterios de aceptación (MVP actual)

- [x] Un admin puede crear un doc desde el panel y verlo publicado en `/docs`.
- [x] Un admin solo puede editar/eliminar sus propios documentos.
- [x] La búsqueda encuentra docs por título, descripción y tags.
- [x] El layout es responsive en mobile.
- [x] Las imágenes se suben al repo y se renderizan en el doc.
- [x] El sistema de auth rechaza accesos no autorizados a rutas protegidas.
- [x] El deploy en Vercel funciona sin configuración adicional.

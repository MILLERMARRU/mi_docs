# DocHubs

Plataforma de documentación colaborativa para developers. Escribe en Markdown, publica al instante, comparte con tu equipo. Sin base de datos — todo vive en GitHub.

**Autores:** Miller Zamora · Sam Vasquez  
**Stack:** Next.js 16 · React 19 · TypeScript · Tailwind CSS 4  
**Deploy:** Vercel

---

## Requisitos previos

- Node.js 18+
- npm 9+
- Cuenta de GitHub con un repositorio para almacenar los docs
- Personal Access Token de GitHub con permisos `repo`

---

## Instalación

```bash
# 1. Clona el repositorio
git clone https://github.com/MILLERMARRU/team_doc.git
cd team_doc

# 2. Instala dependencias
npm install

# 3. Crea el archivo de variables de entorno
cp .env.example .env.local
```

---

## Configuración

Edita `.env.local` con tus valores:

```env
# Repositorio de GitHub donde se guardarán los docs
GITHUB_TOKEN=ghp_tu_token_aqui
GITHUB_OWNER=tu_usuario_github
GITHUB_REPO=nombre_del_repo_de_docs
GITHUB_BRANCH=main

# Secret para firmar los JWT de sesión (mínimo 32 caracteres)
AUTH_SECRET=un_string_largo_y_aleatorio_seguro
```

### Generar el token de GitHub

1. Ve a **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**
2. Genera un nuevo token con scope `repo` (acceso completo a repositorios privados) o `public_repo` si el repo es público
3. Copia el token y pégalo en `GITHUB_TOKEN`

---

## Usuarios admin

Los usuarios se definen en `data/users.json`. Para agregar o cambiar contraseñas:

```bash
# Genera un hash bcrypt de tu contraseña
npm run hash-password
```

Pega el hash generado en `data/users.json`:

```json
[
  {
    "username": "miller",
    "passwordHash": "$2b$12$..."
  },
  {
    "username": "sam",
    "passwordHash": "$2b$12$..."
  }
]
```

---

## Comandos

```bash
# Desarrollo local
npm run dev

# Build de producción
npm run build

# Servidor de producción
npm run start

# Lint
npm run lint

# Generar hash bcrypt para contraseñas
npm run hash-password
```

---

## Estructura del proyecto

```
app/
  ├── page.tsx                  → Landing pública
  ├── admin/
  │   ├── page.tsx              → Panel de edición (protegido)
  │   ├── login/page.tsx        → Login
  │   └── _components/
  │       ├── AdminEditor.tsx   → Editor Markdown + preview
  │       └── LoginForm.tsx
  ├── api/
  │   ├── auth/                 → login · logout
  │   └── docs/                 → nav · upsert · upload-image
  └── docs/
      └── [[...slug]]/          → Visualización de docs
components/
  ├── Navbar.tsx
  └── docs/                     → Sidebar · Markdown · SearchCmdk · Toc
lib/
  ├── auth.ts                   → JWT + bcrypt
  ├── github.ts                 → Octokit (leer/escribir en repo)
  └── docs.ts                   → Índice y contenido de docs
data/
  └── users.json                → Usuarios admin
```

---

## Cómo funciona

```
Admin escribe doc
      ↓
POST /api/docs/upsert
      ↓
Octokit sube .md al repo de GitHub
      ↓
index.json actualizado con metadata
      ↓
ISR revalida en ≤ 120 segundos
      ↓
Doc visible en /docs/sección/slug
```

Los documentos se almacenan como archivos `.md` en el repo de GitHub configurado:

```
tu-repo-de-docs/
  ├── index.json           → Índice de secciones e items
  └── docs/
      ├── devops/
      │   └── docker.md
      └── images/
          └── diagrama-1234567890.png
```

---

## Deploy en Vercel

1. Conecta el repo en [vercel.com](https://vercel.com)
2. Agrega las variables de entorno en **Project Settings → Environment Variables**
3. Deploy automático en cada push a `main`

> No se requiere ninguna configuración adicional. `next.config.ts` ya tiene `poweredByHeader: false` y las rutas optimizadas.

---

## Rutas principales

| Ruta | Descripción |
|---|---|
| `/` | Landing page |
| `/docs` | Índice de documentación |
| `/docs/[sección]/[slug]` | Documento específico |
| `/admin` | Panel de edición (requiere login) |
| `/admin/login` | Formulario de acceso |
| `/team` | Página del equipo |

---

## Características

- **Editor WYSIWYG** con preview en tiempo real
- **Syntax highlighting** con Shiki para +100 lenguajes
- **Búsqueda** instantánea con `Cmd+K` / `Ctrl+K`
- **Dark / Light mode** con next-themes
- **Subida de imágenes** directo al repo desde el editor
- **Control de propiedad** — cada admin gestiona sus propios docs
- **Responsive** — sidebar y TOC en drawers en mobile
- **Zero DB** — todo persiste en GitHub con historial de commits

---

## Licencia

MIT © 2025 Miller Zamora · Sam Vasquez

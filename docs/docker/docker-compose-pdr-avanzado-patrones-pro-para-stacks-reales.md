# 🚀 Docker Compose (PDR Avanzado): patrones pro para stacks reales

> ✅ **Objetivo:** Tener un PDR avanzado (sin repetir lo básico) con técnicas “pro” para que tu stack sea **más seguro, mantenible, configurable y listo para equipos**.

---

## 🧭 Índice
1. 🧩 Múltiples archivos (`-f`) y entornos (dev/stage/prod)
2. 🧱 Reutilización con **anchors** y `x-extends` (DRY)
3. 🔐 Secrets & Configs (sin exponer credenciales)
4. 🧰 Overrides por servicio: `command`, `entrypoint`, `profiles`
5. 🧠 `depends_on` real: salud, *wait-for*, y orden correcto
6. 🧯 Reinicios, límites, recursos, y políticas de estabilidad
7. 📦 Build avanzado: multi-stage, cache, `build.args`, `target`
8. 📡 Observabilidad: logging, health, métricas, eventos
9. 🛡️ Hardening: `read_only`, `user`, capabilities, `security_opt`
10. 🗃️ Volúmenes avanzados: bind vs named, `tmpfs`, permisos
11. 🧪 `docker compose` comandos avanzados (debug pro)
12. ✅ Checklist final de producción (con Compose)

---

## 1) 🧩 Múltiples archivos Compose (dev / prod)

**Idea:** un `compose.yml` base + un `compose.dev.yml` (override) + `compose.prod.yml`.

📁 Ejemplo:
```
compose.yml
compose.dev.yml
compose.prod.yml
.env
.env.prod
```

### ▶️ Ejecutar en dev
```bash
docker compose -f compose.yml -f compose.dev.yml up -d --build
```

### ▶️ Ejecutar en prod (en tu VPS)
```bash
docker compose -f compose.yml -f compose.prod.yml --env-file .env.prod up -d
```

✅ Ventaja: no ensucias el base con detalles de dev (hot reload, puertos extra, tools, etc.).

---

## 2) 🧱 DRY con YAML Anchors y extensiones (`x-`)

Cuando tienes muchos servicios, repetir `networks`, `restart`, `logging` es un dolor.  
Solución: **anchors** + `x-commons`.

```yml
x-common: &common
  restart: unless-stopped
  networks:
    - app_net
  logging:
    driver: "json-file"
    options:
      max-size: "10m"
      max-file: "3"

services:
  api:
    <<: *common
    image: mi-api:latest

  worker:
    <<: *common
    image: mi-worker:latest

networks:
  app_net:
```

---

## 3) 🔐 Secrets & Configs (sin meter claves en `.env`)

### ✅ Opción A: Secrets por archivo (recomendado)
📌 `secrets:` monta el valor como archivo dentro del contenedor.

```yml
services:
  api:
    image: mi-api:latest
    secrets:
      - db_password
    environment:
      DB_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

Dentro de tu app, lees el archivo `DB_PASSWORD_FILE`.

### ✅ Opción B: `.env` (solo para dev)
- Bien para local
- No ideal para prod si hay credenciales sensibles

---

## 4) 🧰 `command`, `entrypoint`, `profiles` (control total)

### Cambiar comando sin rebuild
```yml
services:
  api:
    image: mi-api:latest
    command: ["sh", "-c", "python app.py --port 8080"]
```

### `entrypoint` para wrappers / init
```yml
entrypoint: ["sh", "-c", "./migrate.sh && exec "$@"", "--"]
command: ["node", "server.js"]
```

### `profiles` para herramientas de dev
```yml
services:
  adminer:
    image: adminer
    profiles: ["dev"]
    ports: ["8088:8080"]
```

```bash
docker compose --profile dev up -d
```

---

## 5) 🧠 Arranque correcto: health + “wait-for” (realista)

`depends_on` **no garantiza** que la DB esté lista para aceptar conexiones (solo controla arranque).

✅ Solución:
- `healthcheck` en DB
- y en la API usar un “wait-for” (script) o retry interno.

### Healthcheck en Postgres
```yml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
  interval: 5s
  timeout: 3s
  retries: 20
```

### Wait-for (bash) dentro de API (ejemplo)
```bash
#!/usr/bin/env sh
set -e
until nc -z db 5432; do
  echo "⏳ esperando db..."
  sleep 1
done
echo "✅ db lista"
exec "$@"
```

---

## 6) 🧯 Estabilidad: `restart`, límites y recursos

### Políticas de reinicio
| Política | Uso recomendado |
|---|---|
| `no` | pruebas |
| `on-failure` | jobs |
| `always` | servicios core (cuidado) |
| `unless-stopped` ✅ | la más práctica |

```yml
restart: unless-stopped
```

### Límites (ojo: depende del runtime/entorno)
```yml
services:
  api:
    mem_limit: 512m
    cpus: "0.50"
```

---

## 7) 📦 Build avanzado (multi-stage + args + target)

### Multi-stage (Dockerfile)
```dockerfile
# stage build
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# stage runtime
FROM node:22-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
CMD ["node", "dist/index.js"]
```

### Compose `build.args` + `target`
```yml
services:
  api:
    build:
      context: ./apps/api
      dockerfile: Dockerfile
      target: runtime
      args:
        NODE_ENV: production
```

✅ Beneficio: imágenes más chicas, builds más rápidos, menos superficie de ataque.

---

## 8) 📡 Observabilidad: logs, eventos y health

### Logging con rotación
```yml
logging:
  driver: "json-file"
  options:
    max-size: "10m"
    max-file: "5"
```

### Eventos en vivo (debug pro)
```bash
docker compose events
```

### Ver health del contenedor
```bash
docker inspect --format='{{json .State.Health}}' mi_db
```

---

## 9) 🛡️ Hardening (seguridad) para contenedores

### Ejecutar como usuario no-root
```yml
user: "1000:1000"
```

### FS de solo lectura
```yml
read_only: true
tmpfs:
  - /tmp
```

### Quitar privilegios (cuando aplique)
```yml
security_opt:
  - no-new-privileges:true
cap_drop:
  - ALL
```

✅ Esto reduce riesgos si un contenedor es comprometido.

---

## 10) 🗃️ Volúmenes avanzados: bind vs named + permisos

### Bind mount (código en dev)
```yml
volumes:
  - ./apps/api:/app
```

### Named volume (datos persistentes)
```yml
volumes:
  - db_data:/var/lib/postgresql/data
```

### `tmpfs` (rápido y efímero)
```yml
tmpfs:
  - /run
  - /tmp
```

---

## 11) 🧪 Comandos avanzados de `docker compose`

### Validar y ver el Compose final (merge de overrides)
```bash
docker compose -f compose.yml -f compose.dev.yml config
```

### Convertir a salida “lista” para depurar
```bash
docker compose convert
```

### Ejecutar un comando en servicio (sin entrar)
```bash
docker compose exec api env
```

### Correr un contenedor “temporal”
```bash
docker compose run --rm api sh
```

### Escalar (útil para stateless)
```bash
docker compose up -d --scale worker=3
```

---

## 12) ✅ Checklist pro (antes de subir a VPS)

✅ Secrets fuera del repo (`./secrets/*` en `.gitignore`)  
✅ Overrides separados (dev/prod)  
✅ Healthchecks en DB y servicios críticos  
✅ Logs con rotación configurada  
✅ Volúmenes correctos (data persistente)  
✅ `restart: unless-stopped` en servicios core  
✅ Usuario no-root + `read_only` cuando se pueda  
✅ `docker compose config` sin warnings  
✅ Puertos expuestos mínimos (solo lo necesario)

---

## 🧩 Figura: estrategia dev vs prod (simple)

```
           DEV 🧪                          PROD 🏭
compose.yml + compose.dev.yml        compose.yml + compose.prod.yml
    |                                      |
hot reload + tools                   mínimos puertos + secrets + hardening
```

---

## ✅ Cierre

Con este PDR avanzado puedes montar stacks “de verdad”:
- **configuración por entorno**
- **seguridad**
- **reutilización DRY**
- **observabilidad y estabilidad**
- **builds profesionales**

Si me dices tu stack exacto (por ejemplo: **Spring Boot + Postgres + Redis + Nginx + pgAdmin**) te lo dejo totalmente adaptado con nombres reales, puertos y buenas prácticas específicas.

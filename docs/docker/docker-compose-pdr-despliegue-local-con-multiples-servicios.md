# 🐳 Docker Compose (PDR): Levantar un stack multi-servicio fácil y ordenado

> ✅ **Objetivo:** Definir un **PDR** (Procedimiento / Documento de Referencia) para **crear, ejecutar y mantener** un entorno con múltiples servicios usando **Docker Compose** (API moderna `docker compose`).

---

## 📌 ¿Qué resuelve Docker Compose?

Docker Compose te permite declarar una app compuesta por varios servicios (por ejemplo: **API + DB + Redis**) en un solo archivo `compose.yml` y levantar todo con un comando.

---

## 🧩 Conceptos clave (en simple)

| Concepto | ¿Qué es? | Ejemplo |
|---|---|---|
| **service** | Un contenedor (o grupo) con configuración | `api`, `db`, `redis` |
| **image** | Imagen que se descarga/usa | `postgres:16-alpine` |
| **build** | Construye imagen desde Dockerfile | `./apps/api/Dockerfile` |
| **ports** | Mapea puertos host ↔ contenedor | `8080:8080` |
| **env_file / environment** | Variables de entorno | `.env` |
| **volumes** | Persistencia de datos | `db_data:/var/lib/postgresql/data` |
| **networks** | Red interna entre servicios | `app_net` |
| **depends_on** | Orden de arranque (básico) | `api` depende de `db` |

---

## 🗂️ Estructura recomendada del proyecto

📁 Proyecto
```
.
├─ compose.yml
├─ .env
├─ apps/
│  └─ api/
│     ├─ Dockerfile
│     └─ ...
└─ docs/
   └─ docker-compose-pdr.md
```

✅ Mantén `compose.yml` en la raíz.  
✅ El `.env` también en la raíz para que Compose lo cargue automáticamente.

---

## 🧱 Figura: Arquitectura típica (API + DB + Redis)

```
        🌐 Host (tu PC)
           |
     8080 ->|  (puerto expuesto)
           v
     ┌───────────┐       ┌──────────┐
     │   🧩 API   │ <-->  │  ⚡ Redis │
     └─────┬─────┘       └────┬─────┘
           |                  |
           v                  v
        ┌───────────┐
        │  🗄️ Postgres│
        └───────────┘
      (datos persistentes en volumen)
```

---

## ✅ Compose base (listo para usar)

📌 **Archivo:** `compose.yml`

```yml
name: mi-stack

services:
  db:
    image: postgres:16-alpine
    container_name: mi_db
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - app_net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 5s
      timeout: 3s
      retries: 20

  redis:
    image: redis:7-alpine
    container_name: mi_redis
    ports:
      - "6379:6379"
    networks:
      - app_net

  api:
    build:
      context: ./apps/api
      dockerfile: Dockerfile
    container_name: mi_api
    ports:
      - "8080:8080"
    env_file:
      - .env
    environment:
      DATABASE_URL: ${DATABASE_URL}
      REDIS_URL: ${REDIS_URL}
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - app_net

networks:
  app_net:

volumes:
  db_data:
```

---

## 🔐 Ejemplo de `.env` (raí­z del proyecto)

📌 **Archivo:** `.env`

```bash
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=supersecret

DATABASE_URL=postgresql://app_user:supersecret@db:5432/app_db
REDIS_URL=redis://redis:6379
```

✅ Nota: Dentro de Compose, el host de DB/Redis es el **nombre del servicio** (`db`, `redis`), no `localhost`.

---

## ▶️ Comandos esenciales (con ejemplos bash)

### 🚀 Levantar todo
```bash
docker compose up -d
```

### 📜 Ver logs (todo)
```bash
docker compose logs -f
```

### 📜 Ver logs (solo un servicio)
```bash
docker compose logs -f api
```

### 🧯 Detener
```bash
docker compose stop
```

### ♻️ Reiniciar
```bash
docker compose restart api
```

### 🧹 Bajar y borrar contenedores (sin borrar volúmenes)
```bash
docker compose down
```

### 🧨 Bajar y borrar TODO (incluye volúmenes)
⚠️ Esto elimina data persistida (DB).
```bash
docker compose down -v
```

### 🧱 Reconstruir imagen (cuando cambias código/Dockerfile)
```bash
docker compose build --no-cache api
docker compose up -d
```

---

## 🧪 Verificación rápida (checklist)

| Check | Comando | Qué esperas |
|---|---|---|
| Contenedores arriba | `docker compose ps` | `Up` en servicios |
| DB sana | `docker compose logs -f db` | “ready to accept connections” |
| API responde | `curl -i http://localhost:8080` | status 200/401/404 según tu app |
| Red interna ok | `docker exec -it mi_api ping db` | responde ping |

---

## 🧰 Ejecutar comandos dentro de un contenedor

### Entrar al contenedor API
```bash
docker exec -it mi_api sh
```

### Ejecutar comando directo
```bash
docker exec -it mi_api env
```

---

## 🧊 Volúmenes: persistencia real (DB no se pierde)

📌 En el ejemplo:
- `db_data` guarda los datos de Postgres
- aunque reinicies contenedores, la DB se mantiene

### Ver volúmenes
```bash
docker volume ls
```

### Inspeccionar volumen
```bash
docker volume inspect db_data
```

---

## 🧠 Buenas prácticas (lo que evita dolores de cabeza)

✅ **1) Usa `compose.yml` (o `docker-compose.yml`) pero sé consistente**  
✅ **2) Variables sensibles en `.env`** (no las hardcodees)  
✅ **3) Healthchecks en DB** para que la API no arranque “a ciegas”  
✅ **4) No expongas puertos si no es necesario** (solo API, DB si quieres debug)  
✅ **5) Nombres claros** para `container_name` (opcional)  
✅ **6) Usa redes** para comunicación interna segura  
✅ **7) Documenta comandos** (este PDR 👌)

---

## 🧪 Perfiles (dev vs prod) — opcional pero pro

Te permite levantar servicios extra solo en dev (ej: pgAdmin).

```yml
services:
  pgadmin:
    image: dpage/pgadmin4
    profiles: ["dev"]
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@local.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"
    networks:
      - app_net
```

Levantar con perfil:
```bash
docker compose --profile dev up -d
```

---

## 🧯 Troubleshooting rápido (errores comunes)

### ❌ “port is already allocated”
✅ Libera el puerto o cambia el mapeo:
```yml
ports:
  - "8081:8080"
```

### ❌ API no conecta a DB (usa localhost)
✅ Dentro de Compose, usa `db` como host:
```bash
DATABASE_URL=postgresql://user:pass@db:5432/app_db
```

### ❌ Cambié el Dockerfile y no aplica
✅ Reconstruye:
```bash
docker compose build --no-cache api
docker compose up -d
```

---

## 📦 Plantilla mínima ultra-rápida (solo para probar)

```yml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

```bash
docker compose up -d
```

---

## ✅ Cierre

📌 Con este PDR puedes:
- levantar entornos consistentes,
- reducir errores por configuración,
- y documentar tu stack para tu equipo o clientes.

Si quieres, puedo generarte una versión **adaptada a tu stack real** (Spring Boot + Postgres + Redis + Adminer/pgAdmin + Nginx) manteniendo el mismo estilo.
(aja)
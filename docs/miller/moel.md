# 🧭 Instrucciones de Ejecución

## 1. Inicializar la base de datos (solo la primera vez)

```bash
docker compose up -d        # Levanta el contenedor de MySQL
npm run seed:admin          # Crea las tablas y el usuario administrador por defecto
```

> ⚠️ Este paso solo es necesario la primera vez o cuando quieras reinicializar la base de datos.

---

## 2. Iniciar el servidor

```bash
npm run dev
```

El servidor se iniciará en modo desarrollo.

---

## 3. Documentación de la API

Con el servidor en ejecución, puedes acceder a la documentación interactiva (Swagger) desde:

👉 http://localhost:3000/api-docs

---

# 📝 Resumen de Cambios

### 🔁 Relación Usuario – Rol
- Se simplificó la relación de **muchos-a-muchos** (`usuarios_roles`) a **uno-a-muchos**:
  - Ahora: `usuarios.rol_id → roles.id`
  - Cada usuario tiene **un único rol**.

### 👤 Modelo Usuario
- Se agregó el campo `rol_id` como **foreign key** directa hacia la tabla `roles`.

### ⚙️ Servicios
- La creación de usuarios asigna el `rol_id` directamente.
- El login obtiene el rol mediante `include` en la relación `Usuario → Rol`.
- La eliminación de roles valida previamente si existen usuarios asignados a ese rol.

### 🛡️ Middleware `verificarRol`
- Ahora consulta el rol directamente desde la relación:
  - `Usuario → Rol`
- Se eliminó la dependencia de la tabla intermedia `usuarios_roles`.

### ✅ Validaciones (Zod)
- Se añadió el campo opcional `rol_id` en:
  - `UpdateUsuarioSchema`
  - `PatchUsuarioSchema`

### 🗑️ Archivos Eliminados
Se eliminaron los siguientes componentes relacionados al modelo `UsuarioRol`:
- Modelo  
- Servicio  
- Controller  
- Rutas  
- Schema  
**(5 archivos en total)**

### 📚 Swagger
- Documentación actualizada para reflejar la nueva relación:
  - Uso de `include=rol`
  - Eliminación de endpoints `/v1/usuarios-roles`

### 🌱 Seed Script
- El script de seed ahora importa **todos los modelos del sistema**, garantizando que:
  - `sync()` cree correctamente **todas las tablas necesarias**.

---

# ✅ Estado del Refactor

Este refactor simplifica el modelo de autorización, reduce complejidad innecesaria en base de datos y mejora la mantenibilidad del backend al eliminar relaciones intermedias que no aportaban valor en el flujo actual del sistema.

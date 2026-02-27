# Documentación: Cómo Iniciar un Proyecto con Filament Admin en Laravel

## 📋 **Índice**
1. [Requisitos Previos](#requisitos-previos)
2. [Creación de un Proyecto Laravel](#1-creación-de-un-proyecto-laravel)
3. [Instalación de Filament Admin](#2-instalación-de-filament-admin)
4. [Configuración Inicial](#3-configuración-inicial)
5. [Creación de un Usuario Administrador](#4-creación-de-un-usuario-administrador)
6. [Acceso al Panel de Administración](#5-acceso-al-panel-de-administración)
7. [Creación de Recursos CRUD](#6-creación-de-recursos-crud)
8. [Personalización del Panel](#7-personalización-del-panel)
9. [Instalación de Plugins Opcionales](#8-instalación-de-plugins-opcionales)

---

## ✅ **Requisitos Previos**
Antes de iniciar, asegúrate de tener lo siguiente instalado:

- **PHP 8.1 o superior**
- **Composer**
- **Node.js** y **npm** (para compilar assets)
- **Servidor web** (Apache, Nginx, etc.)

---

## 🛠 **1. Creación de un Proyecto Laravel**
Si aún no tienes un proyecto Laravel, crea uno nuevo:

```bash
composer create-project laravel/laravel nombre-del-proyecto
```

📂 **Cambia al directorio del proyecto:**

```bash
cd nombre-del-proyecto
```

---

## 🛠 **2. Instalación de Filament Admin**
Ejecuta el siguiente comando para instalar **Filament Admin**:

```bash
composer require filament/filament
```

---

## 🛠 **3. Configuración Inicial**
Publica los archivos de configuración y las vistas necesarias con el siguiente comando:

```bash
php artisan filament:install
```

Este comando realiza lo siguiente:
- Publica el archivo de configuración en `config/filament.php`.
- Crea una página de bienvenida en el panel de administración.

---

## 🛠 **4. Creación de un Usuario Administrador**
Para acceder al panel de administración, necesitas un usuario. Usa el siguiente comando:

```bash
php artisan make:filament-user
```

Te pedirá que ingreses:
- **Nombre:** (Ejemplo: Admin)
- **Email:** (Ejemplo: admin@tu-dominio.com)
- **Contraseña:** (Ingresa una contraseña segura)

---

## 🛠 **5. Acceso al Panel de Administración**
Inicia el servidor local:

```bash
php artisan serve
now
composer run dev
```

Luego, visita la siguiente URL en tu navegador:

```
http://localhost:8000/admin
```

🔐 **Inicia sesión con el email y la contraseña creados.**

---

## 🛠 **6. Creación de Recursos CRUD**
Para crear un CRUD completo usando Filament, ejecuta:

```bash
php artisan make:filament-resource NombreDelModelo --generate
```

Ejemplo para el modelo `Post`:

```bash
php artisan make:filament-resource Post --generate
```

Esto genera automáticamente las páginas para:
- **Listar Posts**
- **Crear Post**
- **Editar Post**
- **Eliminar Post**

### 📂 **Estructura generada:**
```bash
app
└── Filament
    └── Resources
        └── PostResource.php
        └── PostResource
            ├── Pages
            │   ├── ListPosts.php
            │   ├── CreatePost.php
            │   └── EditPost.php
            ├── Forms
            │   └── PostForm.php
            └── Tables
                └── PostTable.php
```

---

## 🛠 **7. Personalización del Panel**
Puedes personalizar los íconos y el diseño del panel de administración en el archivo `config/filament.php`.

### 🔧 Cambiar el ícono del menú de navegación:

```php
protected static ?string $navigationIcon = 'heroicon-o-home';
```

Para usar un ícono SVG personalizado:

```php
protected static ?string $navigationIcon = <<<'SVG'
<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h11m-6 4h6m4-5V9m-4 1V6a2 2 0 112 0v3a2 2 0 012 0v3a2 2 0 012 0V9"/>
</svg>
SVG;
```

---

## 🛠 **8. Instalación de Plugins Opcionales**
Filament tiene varios **plugins** que puedes instalar para agregar funcionalidades adicionales.

### 📦 **Ejemplo: Instalar Notifications Plugin**

```bash
composer require filament/notifications
```

---

## 📌 **Comandos Clave Resumidos**
| Comando                              | Descripción                                  |
|--------------------------------------|----------------------------------------------|
| `composer require filament/filament` | Instala Filament Admin.                      |
| `php artisan filament:install`       | Publica los archivos de configuración.       |
| `php artisan make:filament-user`     | Crea un usuario admin para acceder al panel. |
| `php artisan make:filament-resource` | Genera un CRUD completo para un modelo.      |
| `php artisan serve`                  | Inicia el servidor local.                    |

---

### 🚀 **¡Listo!**
Ahora tienes un panel de administración completamente funcional con **Filament Admin**. Puedes personalizarlo según las necesidades de tu proyecto.

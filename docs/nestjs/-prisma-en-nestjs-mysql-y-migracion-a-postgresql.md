
Nest.js es un framework progresivo para Node.js basado en TypeScript que facilita la construcción de aplicaciones escalables y mantenibles.

---

## 🔥 1. Instalación de Nest.js y Creación del Proyecto

### 📌 Requisitos Previos

- 🖥️ Tener instalado **Node.js** (versión 16 o superior).
    
- 📦 Tener **npm** o **yarn** para la gestión de paquetes.
    

### 📌 Instalar CLI de Nest.js

```bash
npm i -g @nestjs/cli
```

📌 **Este comando instala la CLI (Command Line Interface) de Nest.js de forma global en tu sistema.**

### 📌 Crear un Nuevo Proyecto

```js
nest new my-project
```

📌 **Esto generará una estructura de carpetas estándar de Nest.js.**

### 📌 Entrar al Proyecto

```bash
cd my-project
```

---

## 🏗️ 2. Estructura del Proyecto (Buenas Prácticas)

Por defecto, Nest.js genera la siguiente estructura:

```
📂 my-project
 ┣ 📂 src
 ┃ ┣ 📂 modules
 ┃ ┃ ┣ 📂 users
 ┃ ┃ ┃ ┣ 📄 users.controller.ts
 ┃ ┃ ┃ ┣ 📄 users.service.ts
 ┃ ┃ ┃ ┣ 📄 users.module.ts
 ┃ ┣ 📄 app.module.ts
 ┃ ┣ 📄 main.ts
 ┣ 📄 package.json
 ┣ 📄 tsconfig.json
 ┣ 📄 nest-cli.json
 ┣ 📄 .gitignore
 ┗ 📄 README.md
```

### 🎯 Buenas Prácticas

- 📂 **Módulos organizados por dominio en la carpeta** `**modules/**`**.**
    
- 🔗 **Separación clara de controladores, servicios y módulos.**
    
- ✅ **Uso de** `**DTOs**` **(Data Transfer Objects) para validar datos de entrada.**
    
- 🛠️ **Configuración centralizada en** `**config/**` **(variables de entorno).**
    
- 🏗️ **Uso de inyección de dependencias.**
    

---

## ⚙️ 3. Comandos de Nest.js Explicados Paso a Paso

Aquí tienes todos los comandos de Nest.js bien explicados y documentados.

### 📌 3.1 Generar un Recurso Completo

Para crear un recurso llamado `users`, ejecuta:

```js
nest g resource users
```

📌 **Esto generará automáticamente la siguiente estructura en** `**src/users/**`**:**

```
📂 src
 ┣ 📂 users
 ┃ ┣ 📂 dto
 ┃ ┃ ┣ 📄 create-user.dto.ts
 ┃ ┃ ┗ 📄 update-user.dto.ts
 ┃ ┣ 📄 users.controller.ts
 ┃ ┣ 📄 users.service.ts
 ┃ ┣ 📄 users.module.ts
 ┗ 📄 users.controller.spec.ts
```

### 📌 3.2 Comandos de Generación

```js
nest g module users
nest g controller users
nest g service users
nest g guard auth
nest g interceptor logging
nest g filter http-exception
nest g middleware logger
nest g class dto/create-user.dto
nest g class entities/user
```

📌 **Estos comandos generan los módulos, controladores, servicios, guards, interceptores, filtros, middlewares y DTOs.**

### 📌 3.3 Comandos de Ejecución

```js
npm run start:dev
npm run start:prod
npm run start --debug
```

📌 **Ejecutan el servidor en modo desarrollo, producción o depuración.**

### 📌 3.4 Comandos de Configuración

```bash
touch .env
npm i @nestjs/config
```

📌 **Crea un archivo** `**.env**` **y añade soporte para variables de entorno.**

### 📌 3.5 Comandos de Testing

```bash
npm run test
npm run test:e2e
npm run test:cov
```

📌 **Ejecutan pruebas unitarias, pruebas E2E y generan un reporte de cobertura.**

### 📌 3.6 Comandos para Base de Datos (TypeORM)

```bash
npm i @nestjs/typeorm typeorm pg
npm run typeorm migration:generate -- -n CreateUsersTable
npm run typeorm migration:run
```

📌 **Instalan TypeORM y crean y ejecutan migraciones.**

---

## 🚀 Configuración de Prisma con Nest.js (Sin Entities)

Si vas a usar **Prisma** en lugar de **TypeORM**, sigue estos pasos.

### 📌 1. Instalación de Prisma y PostgreSQL

```bash
npm install @prisma/client
npm install --save-dev prisma
```

📌 **Instala el cliente de Prisma y el CLI para manejar la base de datos.**

### 📌 2. Inicializar Prisma

```js
npx prisma init
```

📌 **Esto generará la carpeta** `**prisma/**` **con un archivo de configuración** `**.env**`**.**

### 📌 3. Configurar `.env`

Edita el archivo `.env` y define tu conexión a PostgreSQL:

```js
DATABASE_URL="postgresql://user:password@localhost:5432/mydatabase"
```

📌 **Este archivo almacena las variables de entorno necesarias para la conexión a la base de datos.**

### 📌 4. Configurar `schema.prisma`

Modifica `prisma/schema.prisma` para definir tu modelo:

```js
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id       String  @id @default(uuid())
  name     String
  email    String  @unique
  password String
  createdAt DateTime @default(now())
}
```

### 📌 5. Ejecutar Migraciones

```js
npx prisma migrate dev --name init
```

📌 **Genera la migración para aplicar los cambios en la base de datos.**

### 📌 6. Crear PrismaService

Ejecuta:

```js
nest g module prisma
nest g service prisma
```

📌 **Esto creará** `**prisma.module.ts**` **y** `**prisma.service.ts**` **en la carpeta** `**src/prisma/**`**.**

Edita `src/prisma/prisma.service.ts` con el siguiente contenido:

```js
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    await this.$connect();
  }

  async onModuleDestroy() {
    await this.$disconnect();
  }
}
```

📌 **Este servicio maneja la conexión con la base de datos mediante Prisma.**

### 📌 7. Usar Prisma en el Servicio de Usuarios

Modifica `src/users/users.service.ts` para integrar Prisma:

```js
import { Injectable } from '@nestjs/common';
import { PrismaService } from '../prisma/prisma.service';
import { CreateUserDto } from './dto/create-user.dto';
import { UpdateUserDto } from './dto/update-user.dto';

@Injectable()
export class UsersService {
  constructor(private readonly prisma: PrismaService) {}

  async create(createUserDto: CreateUserDto) {
    return this.prisma.user.create({
      data: createUserDto,
    });
  }

  async findAll() {
    return this.prisma.user.findMany();
  }

  async findOne(id: string) {
    return this.prisma.user.findUnique({ where: { id } });
  }

  async update(id: string, updateUserDto: UpdateUserDto) {
    return this.prisma.user.update({
      where: { id },
      data: updateUserDto,
    });
  }

  async remove(id: string) {
    return this.prisma.user.delete({ where: { id } });
  }
}
```

📌 **Ahora el servicio** `**UsersService**` **utiliza Prisma para interactuar con la base de datos.**

---

## 🎯 Resumen

✅ **Instalación y configuración de Prisma en Nest.js.** ✅ **Definición del modelo en** `**schema.prisma**`**.** ✅ **Creación y ejecución de migraciones.** ✅ **Implementación del** `**PrismaService**`**.** ✅ **Uso de Prisma en** `**UsersService**`**.**

🚀 **Siguientes Pasos:** ¿Quieres que documente cómo integrar **autenticación con JWT, GraphQL o subida de archivos** en Nest.js? 📩
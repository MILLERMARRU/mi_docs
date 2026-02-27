## ❓ ¿Qué es Node.js y para qué se usa?

Node.js es un entorno de ejecución de JavaScript construido sobre el motor V8 de Google Chrome. Permite ejecutar JavaScript en el servidor, fuera del navegador.

### 🌟 Características principales:

- **⚡ Asíncrono y basado en eventos:** Maneja operaciones I/O de manera eficiente.
    
- **💻 Multiplataforma:** Compatible con Windows, macOS y Linux.
    
- **📈 Escalable:** Diseñado para manejar un gran número de conexiones simultáneas.
    

**📌 Usos de Node.js:**

- Desarrollo de servidores web.
    
- Aplicaciones en tiempo real (chats, juegos).
    
- Automatización de tareas (scripts).
    
- API RESTful.
    

---

## 🛠 Instalación de Node.js y configuración del entorno

1. 📥 Descargar Node.js desde [https://nodejs.org](https://nodejs.org)
    
2. ⚙️ Instalar según el sistema operativo.
    
3. ✅ Verificar la instalación:
    

```node
node -v  # Verificar versión de Node.js
npm -v   # Verificar versión de npm
```

4. 📂 Crear un proyecto:
    

```node
mkdir mi-proyecto
cd mi-proyecto
npm init -y
```

---

## 💻 Uso de la línea de comandos (node y npm)

- 🚀 Ejecutar Node.js interactivo:
    
    ```node
    node
    > console.log('Hola Mundo');
    ```
    
- ▶️ Ejecutar un archivo:
    
    ```node
    node app.js
    ```
    
- 📦 Uso de `npm` (Node Package Manager):
    
    ```node
    npm init -y
    npm install express
    npm list
    ```
    

---

## 📝 Primer programa en Node.js: "Hola Mundo"

```node
console.log('Hola Mundo desde Node.js');
```

💾 Guarda el archivo como `app.js` y ejecútalo con:

```node
node app.js
```

---

## ⚖️ Diferencia entre Node.js y otros entornos backend

|Característica|Node.js|PHP|Python|
|---|---|---|---|
|Lenguaje|JavaScript|PHP|Python|
|Modelo de ejecución|Asíncrono|Sincrónico|Sincrónico/Asíncrono|
|Escalabilidad|Alta|Media|Alta|
|Rendimiento|Alto|Medio|Alto|

---

# 📚 Módulos en Node.js

## 🔧 Uso de módulos internos (built-in)

1. **📂 fs (sistema de archivos):**
    
    ```node
    const fs = require('fs');
    fs.writeFileSync('archivo.txt', 'Hola desde Node.js');
    ```
    
2. **🌐 http (servidor web):**
    
    ```node
    const http = require('http');
    const server = http.createServer((req, res) => {
        res.writeHead(200, {'Content-Type': 'text/plain'});
        res.end('Hola Mundo');
    });
    server.listen(3000);
    ```
    
3. **📍 path (manejo de rutas):**
    
    ```node
    const path = require('path');
    console.log(path.basename('/ruta/archivo.txt'));
    ```
    
4. **💻 os (información del sistema):**
    
    ```node
    const os = require('os');
    console.log(os.platform());
    ```
    

---

## 🏗 Creación de módulos personalizados

1. 📝 Crear un archivo `mimodulo.js`:
    
    ```node
    module.exports = function() {
        return 'Hola desde mi módulo';
    };
    ```
    
2. 📥 Importarlo en otro archivo:
    
    ```node
    const saludo = require('./mimodulo');
    console.log(saludo());
    ```
    

---

# 📦 Gestión de paquetes con NPM

## ❓ ¿Qué es NPM?

NPM es el gestor de paquetes de Node.js que permite instalar, compartir y gestionar dependencias.

## ⚙️ Instalación de paquetes

```node
npm install express
```

## 🔄 Dependencias locales vs globales

- **Locales:** Instaladas dentro del proyecto.
    
- **Globales:** Disponibles en todo el sistema (`-g`).
    

## 🏷 Scripts en package.json

```json
{
  "scripts": {
    "start": "node app.js"
  }
}
```

▶️ Ejecutar:

```node
npm start
```

## 📊 Gestión de versiones

```node
npm list
npm outdated
npm update
```

## 🔄 Alternativa con Yarn

```node
npm install --global yarn
yarn add express
```

---

# 🔄 Programación asíncrona en Node.js

## 🔄 Callbacks y el problema del "callback hell"

```node
fs.readFile('archivo.txt', (err, data) => {
    if (err) throw err;
    console.log(data.toString());
});
```

Callback hell:

```node
fs.readFile('file1.txt', (err, data) => {
    fs.readFile('file2.txt', (err, data) => {
        fs.readFile('file3.txt', (err, data) => {
            console.log('Callback hell');
        });
    });
});
```

---

# 🏁 Conclusión

Node.js es una tecnología potente y versátil que permite a los desarrolladores crear aplicaciones del lado del servidor con JavaScript. Su enfoque asincrónico y basado en eventos lo hace ideal para aplicaciones en tiempo real y escalables. Con el uso de módulos internos y externos como NPM, es posible ampliar sus funcionalidades y optimizar el desarrollo de aplicaciones modernas. Aprender Node.js te permitirá dominar el backend con un solo lenguaje de programación, simplificando el stack tecnológico.
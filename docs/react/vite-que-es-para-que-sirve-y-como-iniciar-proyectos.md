## ✨ ¿Qué es Vite?

Vite es una herramienta de desarrollo **rápida** y **ligera**, creada por Evan You (el creador de Vue.js), que permite construir aplicaciones frontend modernas con tiempos de carga casi instantáneos.

## 🔧 ¿Para qué sirve?

Vite está diseñado para:

- Proporcionar un **servidor de desarrollo rápido** mediante **ESM (Módulos de ECMAScript)**.
    
- Ofrecer **recarga en caliente (HMR)** ultrarrápida.
    
- Generar builds optimizadas para producción utilizando **Rollup**.
    

---

## 💪 Importancia de Vite

- Velocidad: Arranca proyectos de forma instantánea.
    
- Simplicidad: Configuración intuitiva y lista para usar.
    
- Compatibilidad: Soporta frameworks modernos como **React, Vue, Svelte y Vanilla JS.**
    
- Modularidad: Permite cargar solo los módulos necesarios sin compilar todo el proyecto.
    

---

## 🚴 Cómo iniciar un proyecto con Vite

Puedes crear un nuevo proyecto en diferentes frameworks con los siguientes comandos:

### 1. React

```bash
npm create vite@latest my-project -- --template react
cd my-project
npm install
npm run dev
```

### 2. Vue

```bash
npm create vite@latest my-project -- --template vue
cd my-project
npm install
npm run dev
```

### 3. Svelte

```bash
npm create vite@latest my-project -- --template svelte
cd my-project
npm install
npm run dev
```

### 4. Vanilla JS (sin framework)

```bash
npm create vite@latest my-project -- --template vanilla
cd my-project
npm install
npm run dev
```

---

## 🌟 Estructura de un proyecto Vite con React

```python
my-project/
├── node_modules/         # Dependencias instaladas
├── public/               # Archivos estáticos
├── src/                  # Código fuente
│   ├── App.jsx           # Componente principal
│   ├── main.jsx          # Punto de entrada
│   └── styles.css        # Estilos
├── index.html            # HTML principal
├── package.json          # Dependencias del proyecto
├── vite.config.js         # Configuración de Vite
└── README.md             # Documentación
```

---

## 🎮 Comandos importantes

- Iniciar el servidor de desarrollo:
    
    ```bash
    npm run dev
    ```
    
- Construir para producción:
    
    ```bash
    npm run build
    ```
    
- Previsualizar la versión de producción:
    
    ```bash
    npm run preview
    ```
    

---

## 📝 Configuración básica de `**vite.config.js**`

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
  },
});
```

---

## 📊 Comparación con Webpack

|Característica|Vite|Webpack|
|---|---|---|
|Velocidad de inicio|Rápida (ESM)|Lenta (bundling completo)|
|HMR|Muy rápido|Más lento|
|Configuración|Simple|Compleja|
|Producción|Optimizada con Rollup|Empaquetado robusto|
|Ecosistema|Moderno|Amplio soporte|

---

## 🛠️ Recursos adicionales

- [Documentación oficial de Vite](https://vitejs.dev/)
    
- [Repositorio de Vite en GitHub](https://github.com/vitejs/vite)
    
- [Ejemplos de proyectos con Vite](https://github.com/vitejs/awesome-vite)
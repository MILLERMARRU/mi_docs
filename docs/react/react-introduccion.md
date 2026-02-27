
## 💡 ¿Qué es React?
React es una biblioteca de JavaScript desarrollada por Facebook para construir interfaces de usuario. Es una de las herramientas más populares en el desarrollo web moderno debido a su enfoque en componentes reutilizables y su eficiente manejo del DOM virtual.

## 🎯 ¿Para qué sirve React?
- Crear interfaces de usuario interactivas y dinámicas.
- Construir aplicaciones web de una sola página (Single Page Applications, SPAs).
- Facilitar la creación de componentes reutilizables.
- Mejorar el rendimiento de las aplicaciones mediante el DOM virtual.

---

## 🛠️ Iniciación con Vite y React

### 1️⃣ Instalación de Node.js
Asegúrate de tener instalado Node.js en tu sistema. Puedes verificarlo con:
```bash
node -v
npm -v
```
Si no lo tienes, descárgalo desde [nodejs.org](https://nodejs.org/).

### 2️⃣ Crear un proyecto con Vite
Vite es una herramienta de construcción rápida y moderna para proyectos React.

#### Pasos:
```bash
> Crear un nuevo proyecto con Vite
npm create vite@latest my-react-app -- --template react

> Accede al directorio del proyecto
cd my-react-app

> Instalar las dependencias
npm install

> Iniciar el servidor de desarrollo
npm run dev
```

### 3️⃣ Estructura Básica del Proyecto
Al crear un proyecto con Vite, obtendrás una estructura como esta:
```
my-react-app/
├── public/          # Archivos estáticos
├── src/             # Código fuente
│   ├── components/  # Componentes reutilizables
│   ├── hooks/       # Hooks personalizados
│   ├── pages/       # Páginas principales
│   ├── services/    # Lógica para consumir APIs
│   ├── styles/      # Archivos CSS o módulos de estilo
│   ├── App.jsx      # Componente principal
│   ├── main.jsx     # Punto de entrada
│   └── assets/      # Recursos (imágenes, estilos, etc.)
├── index.html       # HTML principal
└── package.json     # Dependencias y scripts
```
### Buenas Prácticas para Organizar un Proyecto Grande

1. Dividir en Módulos: Organiza tu proyecto por funcionalidades o características. Por ejemplo:
```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── AuthPage.jsx
│   ├── dashboard/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── DashboardPage.jsx
```
2. Centraliza la Configuración: Guarda configuraciones comunes como rutas, temas o constantes globales en una carpeta `config/`.

3. Reutiliza Componentes y Hooks: Coloca componentes reutilizables en `src/components` y hooks personalizados en `src/hooks`.

4. Manejo de Estado: Si usas Context API o herramientas como Redux, organiza el estado global en una carpeta `src/store`.

---

## 🔌 Hooks más usados en React
Los hooks son funciones especiales en React que permiten "enganchar" características como estado y ciclo de vida en componentes funcionales.

### 🧰 Lista de Hooks Comunes

### 1. `useState`
Permite manejar el estado local en un componente.
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### 2. `useEffect`
Permite manejar efectos secundarios como llamadas a APIs o suscripciones.
```jsx
import { useEffect, useState } from 'react';

function FetchData() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => setData(data));
  }, []); // El array vacío asegura que el efecto se ejecute solo una vez.

  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>;
}
```

### 3. `useContext`
Permite acceder a datos globales proporcionados por un contexto.
```jsx
import { useContext, createContext } from 'react';

const ThemeContext = createContext('light');

function ThemedComponent() {
  const theme = useContext(ThemeContext);
  return <div>Current theme: {theme}</div>;
}
```

### 4. `useReducer`
Ofrece un enfoque alternativo para manejar el estado en componentes complejos.
```jsx
import { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}
```

### 5. `useRef`
Proporciona una referencia mutable que persiste entre renderizados.
```jsx
import { useRef } from 'react';

function InputFocus() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

### 6. `useMemo`
Memoriza el resultado de una función costosa para evitar cálculos innecesarios.
```jsx
import { useMemo, useState } from 'react';

function ExpensiveCalculation({ num }) {
  const calculate = (n) => {
    console.log('Calculando...');
    return n * 2;
  };

  const result = useMemo(() => calculate(num), [num]);

  return <p>Resultado: {result}</p>;
}
```

### 7. `useCallback`
Memoriza funciones para evitar su recreación en cada renderizado.
```jsx
import { useCallback, useState } from 'react';

function Button({ handleClick }) {
  return <button onClick={handleClick}>Click me</button>;
}

function App() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <Button handleClick={increment} />
    </div>
  );
}
```

---

Con esta guía básica, ya tienes un buen punto de partida para explorar React y organizar proyectos grandes de manera profesional. 🚀

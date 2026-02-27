# 📌 `useEffect` en React: Explicación Detallada

## 🔹 ¿Qué es `useEffect`?
`useEffect` es un **hook** en React que nos permite manejar **efectos secundarios** en componentes funcionales. Un **efecto secundario** puede ser cualquier operación que interactúe con algo fuera del componente, como:

- Llamadas a APIs.
- Suscripción a eventos o websockets.
- Manipulación directa del DOM.
- Temporizadores, intervalos o actualizaciones en el navegador.

---

## 🔧 Sintaxis básica
```jsx
useEffect(() => {
  // Código que queremos ejecutar como efecto secundario.
  return () => {
    // Código opcional para limpiar el efecto (cleanup).
  };
}, [dependencias]);
```

### 🔑 Partes importantes:
1. **La función principal (`() => {...}`)**: Código que se ejecuta al renderizar o actualizar el componente.
2. **La función de limpieza (`return () => {...}`)**: Código opcional que se ejecuta para limpiar el efecto al desmontar el componente o antes de volver a ejecutarlo.
3. **El array de dependencias (`[dependencias]`)**: Controla cuándo debe ejecutarse el efecto.

---

## 🛠 Ejemplo básico: `useEffect` sin dependencias
```jsx
import { useEffect, useState } from "react";

function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("El componente se ha renderizado.");
  });

  return (
    <div>
      <p>Contador: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
    </div>
  );
}

export default Timer;
```

---

## 🔄 Ejemplo 2: `useEffect` con dependencias
```jsx
import { useEffect, useState } from "react";

function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(`El contador cambió a: ${count}`);
  }, [count]); // Solo se ejecuta cuando 'count' cambia.

  return (
    <div>
      <p>Contador: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
    </div>
  );
}

export default Timer;
```

---

## 🛠 Ejemplo 3: `useEffect` ejecutándose una sola vez
```jsx
import { useEffect } from "react";

function WelcomeMessage() {
  useEffect(() => {
    console.log("Bienvenido, el componente se ha montado.");

    return () => {
      console.log("El componente se desmontó.");
    };
  }, []); // El efecto solo se ejecuta una vez.

  return <h2>¡Hola, usuario!</h2>;
}

export default WelcomeMessage;
```

---

## 🔄 Ejemplo 4: Llamada a una API con `useEffect`
```jsx
import { useState, useEffect } from "react";

function FetchData() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => response.json())
      .then((json) => {
        setData(json);
        setLoading(false);
      });
  }, []);

  if (loading) return <p>Cargando datos...</p>;

  return (
    <div>
      <h2>{data.title}</h2>
      <p>{data.body}</p>
    </div>
  );
}

export default FetchData;
```

---

## 🔧 Ejemplo 5: Limpiar efectos secundarios (`Cleanup`)
```jsx
import { useEffect, useState } from "react";

function TimerWithCleanup() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);

    return () => {
      clearInterval(interval);
      console.log("El intervalo fue limpiado.");
    };
  }, []);

  return <p>Segundos: {seconds}</p>;
}

export default TimerWithCleanup;
```

---

## ❗ Puntos importantes sobre `useEffect`
1. **Dependencias mal configuradas** pueden causar múltiples ejecuciones no deseadas del efecto.
2. **La función de limpieza (`cleanup`)** es crucial para evitar fugas de memoria en suscripciones, intervalos o eventos.
3. Si no pasamos dependencias, el efecto se ejecutará **en cada renderizado**.
4. **El array de dependencias vacío (`[]`)** asegura que el efecto se ejecute solo una vez, al montar.

---

## 🎯 Conclusión
✅ `useEffect` es uno de los hooks más importantes para manejar efectos secundarios en React.  
✅ Nos permite configurar y limpiar operaciones como llamadas a APIs, temporizadores y suscripciones.  
✅ Es altamente flexible gracias a las dependencias, lo que nos permite controlar cuándo ejecutarlo.

---

🚀 **¿Quieres continuar con otro hook como `useContext`?** 😊
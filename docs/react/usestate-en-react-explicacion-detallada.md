# 📌 `useState` en React: Explicación Detallada

## 🔹 ¿Qué es `useState`?
`useState` es un **hook** en React que permite manejar el **estado** dentro de un componente funcional. 

Antes de `useState`, el estado solo podía ser manejado en **componentes de clase** usando `this.state`. Pero con la introducción de los hooks en **React 16.8**, ahora podemos manejar estado en **componentes funcionales**.

---

## 📌 Sintaxis de `useState`
```jsx
const [estado, setEstado] = useState(valorInicial);
```
- `estado` → Es la variable que almacena el valor del estado.
- `setEstado` → Es la función que nos permite actualizar el estado.
- `valorInicial` → Es el valor inicial del estado.

---

## 🛠 Ejemplo Básico: Contador con `useState`
```jsx
import { useState } from "react";

function Counter() {
  // 1️⃣ Definir un estado llamado 'count' con un valor inicial de 0
  const [count, setCount] = useState(0);

  // 2️⃣ Función para incrementar el contador
  const increment = () => {
    setCount(count + 1); // Modifica el estado sumando 1 al valor actual
  };

  return (
    <div>
      <h2>Contador: {count}</h2> {/* 3️⃣ Muestra el valor del estado en pantalla */}
      <button onClick={increment}>Incrementar</button> {/* 4️⃣ Botón para cambiar el estado */}
    </div>
  );
}

export default Counter;
```

---

## 📝 Ejemplo 2: Manejo de cadenas de texto en `useState`
```jsx
import { useState } from "react";

function Greeting() {
  // Estado para almacenar el nombre ingresado
  const [name, setName] = useState("");

  return (
    <div>
      <h2>Hola, {name || "Desconocido"}!</h2> {/* Si name está vacío, muestra "Desconocido" */}
      <input
        type="text"
        placeholder="Escribe tu nombre..."
        value={name} // El valor del input es el estado 'name'
        onChange={(e) => setName(e.target.value)} // Actualiza el estado en cada cambio
      />
    </div>
  );
}

export default Greeting;
```

---

## 🔄 Ejemplo 3: Estado con Objetos
```jsx
import { useState } from "react";

function UserProfile() {
  // Estado inicial con un objeto
  const [user, setUser] = useState({ name: "", age: 0 });

  return (
    <div>
      <h2>Nombre: {user.name}</h2>
      <h3>Edad: {user.age}</h3>

      {/* Input para cambiar el nombre */}
      <input
        type="text"
        placeholder="Nombre"
        value={user.name}
        onChange={(e) => setUser({ ...user, name: e.target.value })} // Usamos spread operator
      />

      {/* Input para cambiar la edad */}
      <input
        type="number"
        placeholder="Edad"
        value={user.age}
        onChange={(e) => setUser({ ...user, age: Number(e.target.value) })} // Usamos spread operator
      />
    </div>
  );
}

export default UserProfile;
```

### 🛠 ¿Qué es el **spread operator (`...`) y cómo funciona?**
El **spread operator (`...`)** permite **copiar y expandir** los valores de un objeto o array sin modificar el original.

📌 **Ejemplo:**
```jsx
const obj1 = { name: "John", age: 30 };
const obj2 = { ...obj1, city: "New York" };
console.log(obj2); // { name: "John", age: 30, city: "New York" }
```

📌 **En React, lo usamos para actualizar estados sin perder datos previos:**
```jsx
setUser({ ...user, name: e.target.value });
```
Esto mantiene todas las propiedades de `user`, pero **solo cambia `name`**.

---

## 🔄 Ejemplo 4: `useState` con Arrays
```jsx
import { useState } from "react";

function TaskList() {
  // Estado con una lista de tareas
  const [tasks, setTasks] = useState([]);

  const addTask = () => {
    const newTask = `Tarea ${tasks.length + 1}`;
    setTasks([...tasks, newTask]); // Agregamos una nueva tarea sin modificar las anteriores
  };

  return (
    <div>
      <h2>Lista de Tareas</h2>
      <button onClick={addTask}>Añadir tarea</button>
      <ul>
        {tasks.map((task, index) => (
          <li key={index}>{task}</li> // Mostramos cada tarea en una lista
        ))}
      </ul>
    </div>
  );
}

export default TaskList;
```

---

## 🎯 Conclusión
✅ `useState` es la base para manejar datos dinámicos en React.  
✅ Puede usarse con **números, strings, objetos y arrays**.  
✅ Es **reactivo**, por lo que cualquier cambio en el estado **vuelve a renderizar** el componente.  
✅ El **spread operator (`...`)** es clave para actualizar objetos y arrays sin perder datos previos.  

---

## 🚀 ¿Qué sigue?
Ahora que entiendes `useState`, el próximo paso es aprender **`useEffect`**, que permite manejar efectos secundarios como **llamadas a APIs** o **suscripciones**. ¿Quieres que sigamos con ese? 😊

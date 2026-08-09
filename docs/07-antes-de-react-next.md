# 07. Lo que debes saber ANTES de React y Next.js

Este es el archivo que te habría ahorrado tiempo: la lista de tipos de TypeScript que necesitas **ya** para trabajar con React/Next.js sin estancarte. Todo lo que aparece aquí se apoya en los archivos anteriores.

## Lista de requisitos previos (chequeo rápido)

| # | Tema | ¿Lo dominas? |
|---|------|--------------|
| 1 | Tipar arrays y objetos con `interface` | [04](04-arrays-y-objetos.md) |
| 2 | Tipar funciones y callbacks | [05](05-funciones.md) |
| 3 | Union types y `interface`/`type` | [06](06-tipos-avanzados.md) |
| 4 | `Promise<T>` y `async/await` | [05](05-funciones.md) |
| 5 | `Omit`, `Pick`, `Partial` | [06](06-tipos-avanzados.md) |

Si los tienes, aquí está la aplicación directa:

## 1. Tipar props de un componente

Un componente de React recibe **props**. En TypeScript se declaran con `interface` o `type`.

```jsx
// JavaScript (React): sin tipos
export default function Tarjeta(props) {
  return <h2>{props.titulo}</h2>;
}
```

```tsx
// TypeScript (React)
interface TarjetaProps {
  titulo: string;
  descripcion?: string; // opcional
}

export default function Tarjeta({ titulo, descripcion }: TarjetaProps) {
  return (
    <div>
      <h2>{titulo}</h2>
      {descripcion && <p>{descripcion}</p>}
    </div>
  );
}
```

> En TypeScript se **destructura el objeto** de props y se tipa con su nombre de tipo. Lo que antes era `props.titulo` ahora es `titulo` directo.

Si alguien usa el componente sin `titulo`, TypeScript avisa:

```tsx
<Tarjeta />               // ❌ Falta la prop 'titulo'
<Tarjeta titulo={123} />  // ❌ number no es assignable a string
```

## 2. Tipar `useState`

El genérico `<T>` que viste en [06](06-tipos-avanzados.md) es exactamente lo que usa `useState`.

```jsx
// JavaScript (React)
const [count, setCount] = useState(0);
const [nombre, setNombre] = useState("");
```

```tsx
// TypeScript (React)
const [count, setCount] = useState<number>(0);      // count: number
const [nombre, setNombre] = useState<string>("");   // nombre: string
const [lista, setLista] = useState<string[]>([]);   // lista: string[]
const [activo, setActivo] = useState<boolean>(false);

// Estado sin datos todavía → unión con null
interface Usuario { id: number; nombre: string }
const [usuario, setUsuario] = useState<Usuario | null>(null);

// Y luego:
setUsuario({ id: 1, nombre: "Ana" }); // ✅
setUsuario("Ana");                    // ❌ Error: string no es Usuario
```

## 3. Tipar eventos de formularios

En React los eventos (`onChange`, `onClick`, `onSubmit`...) tienen tipos. Los tipos van con `React.FormEvent`, `React.MouseEvent`, etc.

```jsx
// JavaScript (React)
function FormularioTodo() {
  const [texto, setTexto] = useState("");

  return (
    <input value={texto} onChange={(e) => setTexto(e.target.value)} />
  );
}
```

```tsx
// TypeScript (React)
import { useState, type FormEvent } from "react";

function FormularioTodo() {
  const [texto, setTexto] = useState<string>("");

  function manejarCambio(e: React.ChangeEvent<HTMLInputElement>) {
    setTexto(e.target.value);
  }

  function enviar(e: FormEvent<HTMLFormElement>) {
    e.preventDefault(); // evita recargar la página
    console.log("texto enviado:", texto);
  }

  return (
    <form onSubmit={enviar}>
      <input value={texto} onChange={manejarCambio} />
    </form>
  );
}
```

> **Forma corta y común:** `onChange={(e: React.ChangeEvent<HTMLInputElement>) => ...}`

## 4. Tipar un evento de props (callback)

Cuando pasas una función como prop, su tipo es el tipo de una función con un parámetro:

```tsx
interface ButtonProps {
  etiqueta: string;
  onClic: () => void; // función sin parámetros que no devuelve nada
  onClicConDato?: (id: number) => void; // opcional, recibe un number
}

function Boton({ etiqueta, onClic }: ButtonProps) {
  return <button onClick={onClic}>{etiqueta}</button>;
}
```

## 5. `fetch` + `async` en Next.js

En Next.js, los componentes de servidor son funciones `async`. El patrón general:

```tsx
// app/pagina.tsx (Next.js App Router — Server Component)
interface Post {
  id: number;
  titulo: string;
}

// en el entry point del servidor
async function obtenerPosts(): Promise<Post[]> {
  const respuesta = await fetch("https://api.ejemplo.com/posts");
  if (!respuesta.ok) throw new Error("No se pudo cargar");
  return (await respuesta.json()) as Post[];
}

export default async function Pagina() {
  const posts = await obtenerPosts();

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.titulo}</li>
      ))}
    </ul>
  );
}
```

Los puntos de tipo:
- `Promise<Post[]>`: la función async devuelve una promesa de array de posts
- `(await respuesta.json()) as Post[]`: el cast `as` le dice a TS la forma de lo que viene de la API
- `posts.map` sigue siendo el mismo `map` de JavaScript, con `post.titulo` tipado

## 6. `useEffect` tipado

```tsx
// JavaScript (React)
useEffect(() => {
  fetchDatos();
}, []);
```

```tsx
// TypeScript (React)
import { useEffect } from "react";

interface Usuario { id: number; nombre: string }

function App() {
  const [users, setUsers] = useState<Usuario[]>([]);

  useEffect(() => {
    fetch("https://api.ejemplo.com/usuarios")
      .then((res) => res.json())
      .then((datos: Usuario[]) => setUsers(datos))
      .catch((error) => console.error(error));
  }, []);

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.nombre}</li>
      ))}
    </ul>
  );
}
```

## 7. Repasar generics: narrowing

Cuando los datos exteriores no tienen tipo seguro (p. ej. una API), TS te pide "restringir" el tipo. Concepto **narrowing**:

```ts
function procesarDatos(valor: string | string[]) {
  if (typeof valor === "string") {
    return valor.toUpperCase(); // aquí TS sabe que es string
  }
  return valor.map((v) => v.toUpperCase()); // aquí sabe que es string[]
}
```

## Resumen: tu checklist para React/Next

1. Tipar props con `interface Props` (y destructuring).
2. Tipar estado: `useState<T>`, `useState<T | null>`.
3. Tipar eventos: `ChangeEvent`, `FormEvent`, `MouseEvent`.
4. Tipar `fetch` con `Promise<T>` y `as Tipo`.
5. En Next.js: entender que los Server Components son funciones `async` y que los archivos se llaman `.tsx`.

---

## Siguiente paso

Ya viste la parte de React/Next. La guía continúa con [08. Clases y OOP](08-clases-y-oop.md) (base para Nest) y termina en [09. Extra para React/Next](09-react-next-extra.md).
# 04. Arrays y objetos

> **Respuesta directa a la pregunta común:** sí, los arrays y los objetos en TypeScript **son iguales que en JavaScript**. Tienen los mismos métodos (`map`, `filter`, `push`, `length`, etc.). La diferencia es que en TypeScript puedes decir **qué tipo de elementos** contiene el array y **qué forma** tiene el objeto.

## Arrays

### En JavaScript

```js
// JavaScript
const numeros = [1, 2, 3];
const nombres = ["Ana", "Luis"];
numeros.push(4);
console.log(numeros.length); // 4
const dobles = numeros.map((n) => n * 2);
```

### En TypeScript (igual, pero tipados)

Hay dos formas de escribir el tipo de un array:

```ts
// Forma 1: tipo[]
const numeros: number[] = [1, 2, 3];
const nombres: string[] = ["Ana", "Luis"];

// Forma 2: Array<tipo> (exactamente lo mismo)
const numeros2: Array<number> = [1, 2, 3];
const nombres2: Array<string> = ["Ana", "Luis"];
```

Usa la que prefieras. En la práctica, con la inferencia casi no hace falta escribirlos:

```ts
const numeros = [1, 2, 3];   // TS deduce: number[]
const nombres = ["Ana"];     // TS deduce: string[]
```

### Un array que no puede tener más que ese tipo

```ts
let numeros: number[] = [1, 2, 3];
numeros.push("cuatro"); // ❌ Error: string no es asignable a number[]
```

```js
// JavaScript: no hay error, es un array "mixto" desde el primer momento
let numeros = [1, 2, 3];
numeros.push("cuatro"); // ✅ sin aviso
```

### Arrays con tipos mezclados: `(tipo | tipo)[]`

```ts
const mezclado: (string | number)[] = ["abc", 123, "def", 456];
```

## Objetos

### Forma tipada de un objeto

```js
// JavaScript
const persona = { nombre: "Ana", edad: 30 };
console.log(persona.nombre); // "Ana"
```

```ts
// TypeScript: se describe la forma
const persona: { nombre: string; edad: number } = { nombre: "Ana", edad: 30 };
console.log(persona.nombre); // "Ana"
persona.edad = "treinta";    // ❌ Error: must be number
```

Cada propiedad lleva `tipo: key;`. Los `;` (o `,`) separan las propiedades.

### `interface` y `type` — darle nombre a la forma del objeto

Para no repetir la forma cada vez, le das un nombre. Esto es **lo que usas a diario en React para tipar props**:

```ts
interface Persona {
  nombre: string;
  edad: number;
  activo?: boolean; // ? significa opcional
}

const ana: Persona = { nombre: "Ana", edad: 30 };
const pedro: Persona = { nombre: "Pedro", edad: 25, activo: true };

function presentar(p: Persona): string {
  return `${p.nombre} tiene ${p.edad} años`;
}
```

`type` puede lo mismo casi siempre:

```ts
type Persona = {
  nombre: string;
  edad: number;
};
```

¿`interface` o `type`? -> La diferencia fina la vemos en [06-tipos-avanzados](06-tipos-avanzados.md). Para objetos con forma, ambos funcionan; en React verás muchísimo `interface`.

### Propiedades opcionales con `?`

```ts
interface Usuario {
  nombre: string;
  telefono?: string; // puede estar o no
}

const u1: Usuario = { nombre: "Ana" };              // ✅ sin telefono
const u2: Usuario = { nombre: "Luis", telefono: "55" }; // ✅ con telefono
```

Es muy usado con datos que vienen de una API (donde algún campo puede faltar).

## Objetos dentro de arrays (¡lo más común!)

En React/Next vas a recibir listas de objetos desde la API o desde el backend. Se tipa así:

```ts
interface Post {
  id: number;
  titulo: string;
}

const posts: Post[] = [
  { id: 1, titulo: "Hola" },
  { id: 2, titulo: "Mundo" },
];

// Los métodos de array siguen siendo los de JavaScript:
const titulos: string[] = posts.map((p) => p.titulo);
const formateados = posts.filter((p) => p.id > 1);
console.log(posts.length); // 2
```

## Tuplas: arrays con posiciones de tipo fijo

Una tupla es un array donde **cada posición tiene un tipo fijo**:

```ts
let coordenada: [number, number] = [10, 20];
coordenada = [15, 25];      // ✅ ok
coordenada = ["x", 20];     // ❌ Error: posición 0 debe ser number

let par: [string, number] = ["edad", 30];
```

Úsala poco; solo cuando la posición importe (por ejemplo, datos de pares clave-valor).

## `length`, `push`, `indexOf`, etc. — todo sigue existiendo

Todos los métodos de array de JavaScript están disponibles. Solo añade el tipo:

```ts
const nombres: string[] = ["Ana", "Luis", "Pepe"];
console.log(nombres.length);           // 3
nombres.push("Rosa");                  // agrega al final
const primero: string = nombres[0];    // "Ana"
const existe: boolean = nombres.includes("Luis"); // true
const ordenados: string[] = nombres.sort();
```

---

## Siguiente paso

[05. Funciones](05-funciones.md) → parámetros, retornos y callbacks.
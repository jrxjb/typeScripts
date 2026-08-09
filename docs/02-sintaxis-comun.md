# 02. Sintaxis común: JavaScript vs TypeScript

> **La idea central de este archivo:** la sintaxis es **la misma**. Condicionales, llaves, `switch`, `map`, `length`... todo lo que existe en JavaScript existe en TypeScript y se escribe igual. La diferencia nunca está en la sintaxis, sino en **el tipado de los datos**.

## Tabla rápida: ¿cambia o no cambia?

| Concepto | ¿Existe en TypeScript? | ¿Cambia la sintaxis? | ¿Qué añade TypeScript? |
|----------|------------------------|----------------------|------------------------|
| `if / else` | ✅ Sí | ❌ No, igual | Tipos en las variables |
| Ternario `? :` | ✅ Sí | ❌ No, igual | Tipos en las variables |
| Llaves `{}` | ✅ Sí | ❌ No, igual | (objetos) forma tipada |
| `switch` | ✅ Sí | ❌ No, igual | Tipos en los casos |
| `for`, `while` | ✅ Sí | ❌ No, igual | Tipos en el contador |
| `map`, `filter`, `reduce` | ✅ Sí | ❌ No, igual | Tipos en el callback |
| `.length` | ✅ Sí | ❌ No, igual | El tipo sabe si puede tener `.length` |
| Objetos `{ }` | ✅ Sí | ❌ No, igual | Forma tipada (interface/type) |
| Funciones `function` / `=>` | ✅ Sí | ❌ No, igual | Parámetros y retorno tipados |
| Destructuring `const { a } = obj` | ✅ Sí | ❌ No, igual | Tipos de lo extraído |

**Conclusión:** si ya sabes JavaScript, ya sabes la sintaxis de TypeScript. Solo falta aprender a escribir los tipos.

---

## Condicionales: `if / else` y ternarios

```js
// JavaScript
let edad = 18;
if (edad >= 18) {
  console.log("Mayor");
} else {
  console.log("Menor");
}
const resultado = edad >= 18 ? "Mayor" : "Menor";
```

```ts
// TypeScript: idéntico, solo que las variables pueden llevar tipo
let edad: number = 18;
if (edad >= 18) {
  console.log("Mayor");
} else {
  console.log("Menor");
}
const resultado: string = edad >= 18 ? "Mayor" : "Menor";
```

La lógica, los paréntesis, las llaves y los operadores (`>=`, `? :`) son **exactamente iguales**.

## Llaves `{}`

Las llaves significan lo mismo en los dos lenguajes:

```js
// JavaScript
if (true) { /* bloque */ }
const persona = { nombre: "Ana" }; // objeto literal
```

```ts
// TypeScript: igual, y el objeto puede declarar su forma
if (true) { /* bloque */ }
const persona: { nombre: string } = { nombre: "Ana" }; // objeto con forma tipada
```

Los bloques de código son idénticos. La diferencia aparece en el objeto: TypeScript describe **la forma del objeto**.

## `switch`

```js
// JavaScript
function dia(n) {
  switch (n) {
    case 1:
      return "Lunes";
    case 2:
      return "Martes";
    default:
      return "Otro día";
  }
}
```

```ts
// TypeScript: mismo switch, misma sintaxis
function dia(n: number): string {
  switch (n) {
    case 1:
      return "Lunes";
    case 2:
      return "Martes";
    default:
      return "Otro día";
  }
}
```

Solo cambia el tipado de la función. El `switch` en sí es idéntico.

## `map`, `filter`, `reduce`, `forEach`

Estos métodos de array **existen y se usan igual**. La diferencia: el callback que les pasas puede llevar tipos.

```js
// JavaScript
const numeros = [1, 2, 3, 4];
const dobles = numeros.map(n => n * 2);
const pares = numeros.filter(n => n % 2 === 0);
```

```ts
// TypeScript: mismo map/filter, con tipos opcionales en el callback
const numeros: number[] = [1, 2, 3, 4];
const dobles: number[] = numeros.map((n: number): number => n * 2);
const pares: number[] = numeros.filter((n: number) => n % 2 === 0);
```

En la práctica, TypeScript **infiere** los tipos del callback solo, así que casi nunca hay que escribirlos a mano:

```ts
const dobles = numeros.map(n => n * 2); // n es number automáticamente
```

## `.length` y otras propiedades

`.length` funciona igual en los dos:

```js
// JavaScript
const nombre = "Maria";
const frutas = ["manzana", "pera"];
console.log(nombre.length);  // 5
console.log(frutas.length);  // 2
```

```ts
// TypeScript
const nombre: string = "Maria";
const frutas: string[] = ["manzana", "pera"];
console.log(nombre.length);  // 5
console.log(frutas.length);  // 2
```

**Beneficio extra de TS:** si intentas hacer `.length` en algo que no lo tiene, te da error:

```ts
const numero = 42;
console.log(numero.length); // ❌ Error: Property 'length' does not exist on type 'number'
```

## Bucles: `for`, `for...of`, `while`

```js
// JavaScript
for (let i = 0; i < 3; i++) console.log(i);
for (const fruta of ["a", "b"]) console.log(fruta);
let x = 0;
while (x < 3) x++;
```

```ts
// TypeScript: idénticos
for (let i = 0; i < 3; i++) console.log(i);            // i se infiere como number
for (const fruta of ["a", "b"]) console.log(fruta);    // fruta se infiere como string
let x: number = 0;
while (x < 3) x++;
```

## Destructuring de objetos y arrays

```js
// JavaScript
const persona = { nombre: "Ana", edad: 30 };
const { nombre, edad } = persona;
const [primero, segundo] = [1, 2];
```

```ts
// TypeScript: mismo destructuring, el tipo de lo extraído se deduce del objeto
interface Persona { nombre: string; edad: number }
const persona: Persona = { nombre: "Ana", edad: 30 };
const { nombre, edad } = persona; // nombre: string, edad: number
const [primero, segundo]: number[] = [1, 2]; // primero: number
```

## Resumen

| Pregunta | Respuesta |
|----------|-----------|
| ¿Existe `switch` en TS? | Sí, igual que en JS |
| ¿Existe `map` / `length` en TS? | Sí, son métodos de JavaScript |
| ¿Cambian las llaves? | No, iguales |
| ¿Cambian los condicionales? | No, iguales |
| ¿Qué cambia realmente? | Solo se **añaden tipos** a los datos |

**Todo lo que viste aquí** ya lo sabías de JavaScript. Ahora toca aprender los tipos: [tipos básicos](03-tipos-basicos.md).

---

## Siguiente paso

[03. Tipos básicos](03-tipos-basicos.md) → primitivos, inferencia y anotación.
# 03. Tipos básicos

## Primitivos

JavaScript tiene estos tipos de datos primitivos, y TypeScript los anota con el mismo nombre (minúscula):

| Tipo | Ejemplo JS | Ejemplo TS |
|------|-----------|------------|
| `string` | `let n = "hola"` | `let n: string = "hola"` |
| `number` | `let n = 42` | `let n: number = 42` |
| `boolean` | `let b = true` | `let b: boolean = true` |
| `null` | `let x = null` | `let x: null = null` |
| `undefined` | `let x` | `let x: undefined` |

```js
// JavaScript
let nombre = "Ana";
let edad = 30;
let activo = true;
```

```ts
// TypeScript
let nombre: string = "Ana";
let edad: number = 30;
let activo: boolean = true;
```

> **Nota importante:** el tipo se escribe en **minúscula** (`string`, `number`, `boolean`), nunca con mayúscula (`String`, `Number`) a menos que quieras el objeto contenedor (caso raro, evítalo).

## Inferencia vs anotación

TypeScript **deduce el tipo automáticamente** cuando no lo escribes. A esto se le llama *inferencia*.

```ts
let mensaje = "hola";     // TS deduce: string
let total = 10 + 5;       // TS deduce: number
const esVerdad = true;    // TS deduce: boolean
```

Puedes dejar que infiera (recomendado para variables simples), o anotarlo explícitamente para ser claro:

```ts
let mensaje: string = "hola"; // anotación explícita
```

Ambas formas son válidas. La inferencia evita escribir de más.

## `const` vs `let` vs `var`

Igual que en JavaScript:

```ts
const PI = 3.14;        // no se puede reasignar
let contador = 0;       // se puede reasignar
var viejo = "evitar";   // existe, pero no lo uses (ámbito de función)
```

**Regla práctica:** usa `const` por defecto y cambia a `let` solo si vas a reasignar.

## ¿Qué pasa si asignas un tipo equivocado?

Aquí está el valor de TypeScript: **el error aparece al compilar**, antes de ejecutar.

```ts
let edad: number = 30;
edad = "treinta"; // ❌ Error: Type 'string' is not assignable to type 'number'
```

```js
// JavaScript: no hay error, simplemente funciona con lo que sea
let edad = 30;
edad = "treinta"; // ✅ sin aviso (y así nacen los bugs)
```

## Union: un valor puede ser de varios tipos

Es el primer tipo "avanzado" pero lo necesitarás desde el día uno (sobre todo para datos de servidor). Se usa el símbolo `|`:

```ts
let id: string | number = "abc";
id = 123;        // ✅ también vale
id = true;       // ❌ Error: boolean no está en string | number
```

En React es muy común: un estado que empieza como `null` y luego tiene datos.

```ts
let usuario: { nombre: string } | null = null;
usuario = { nombre: "Ana" }; // ✅
```

## `any` — el "no tipo"

Si pones `any`, TypeScript deja de comprobar ese valor. Úsalo solo como último recurso (p. ej. migrar código legacy o librerías sin tipos):

```ts
let dato: any = "cualquier cosa";
dato = 123;      // ✅ TS no protesta
dato.foo();      // ✅ tampoco (y por eso es peligroso)
```

> **Regla:** evitar `any` casi siempre. Si no sabes el tipo, usa `unknown` o union. Veremos esto a fondo en [06-tipos-avanzados](06-tipos-avanzados.md).

## Operaciones: número + número, texto + texto

```ts
let a: number = 2;
let b: number = 3;
console.log(a + b);            // 5

let saludo: string = "Hola ";
let nombre: string = "Ana";
console.log(saludo + nombre);  // "Hola Ana"

// ¡Cuidado! Mezclar tipos da error:
let resultado = a + nombre; // ❌ Error: Operator '+' cannot be applied to types 'number' and 'string'
```

En JavaScript esto haría `"23"` (concatenación rara). TypeScript te lo impide.

---

## Siguiente paso

[04. Arrays y objetos](04-arrays-y-objetos.md) → ¿los arrays y objetos son iguales que en JS? Sí, pero con tipos.
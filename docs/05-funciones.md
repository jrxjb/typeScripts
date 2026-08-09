# 05. Funciones

> Las funciones en TypeScript son **iguales que en JavaScript**, con una diferencia: puedes tipar **los parámetros** y **el valor de retorno**.

## Sintaxis básica

### JavaScript

```js
function saludar(nombre) {
  return "Hola " + nombre;
}

const despedir = (nombre) => "Adiós " + nombre;
```

### TypeScript

```ts
function saludar(nombre: string): string {
  return "Hola " + nombre;
}

const despedir = (nombre: string): string => "Adiós " + nombre;
```

Patrón: `nombre: string` tipa el parámetro, y `: string` después de los paréntesis tipa el **retorno**.

## Si no escribes el retorno, TypeScript lo infiere

```ts
function sumar(a: number, b: number) {
  return a + b; // TS deduce que el retorno es number
}
```

Puedes dejar que infiera casi siempre. Anota el retorno cuando quieras ser explícito.

## Funciones sin retorno: `void`

Cuando la función no devuelve nada, el tipo es `void`:

```ts
function log(mensaje: string): void {
  console.log(mensaje);
}

const imprimir = (nombre: string): void => {
  console.log(nombre);
};
```

`void` se lee como "no devuelve valor".

## Parámetros opcionales y valores por defecto

Igual que JavaScript, con extras:

```ts
// Opcional con '?'
function saludar(nombre: string, apellido?: string): string {
  return apellido ? `${nombre} ${apellido}` : nombre;
}
saludar("Ana");           // ✅ apellido no se pasa
saludar("Ana", "López");  // ✅ apellido sí se pasa

// Valor por defecto (el tipo se infiere del default)
function saludar2(nombre: string, apellido: string = "García"): string {
  return `${nombre} ${apellido}`;
}
```

## Error de tipo en el parámetro

```ts
function saludar(nombre: string): string {
  return "Hola " + nombre.toUpperCase();
}

saludar(123); // ❌ Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

```js
// JavaScript: se ejecuta y revienta en tiempo de ejecución
saludar(123); // 💥 TypeError: nombre.toUpperCase is not a function
```

## Callbacks: funciones que se pasan a otras

Los callbacks son la base de `.map`, `.filter`, eventos de React, `setTimeout`, etc.

### Tipo de un callback

```ts
// tipo del callback: (dato) => resultado
function procesar(texto: string, callback: (t: string) => string): string {
  return callback(texto);
}

const mayus = (t: string): string => t.toUpperCase();
console.log(procesar("hola", mayus)); // "HOLA"
```

### Callback en `.map` (muy común en React para listas)

```ts
const nombres: string[] = ["Ana", "Luis"];

// el parámetro (n) se infiere como string
const enMayusculas: string[] = nombres.map((n) => n.toUpperCase());

// con tipos explícitos (opcional)
const conEstilo = nombres.map((n: string): string => `★ ${n}`);
```

## `fetch` + `async/await` (muy usado en Next.js)

Para pedir datos de una API, la función es `async` y devuelve una `Promise`:

```ts
// El retorno es Promise<datos>
async function obtenerUsuarios(): Promise<string[]> {
  const respuesta = await fetch("https://api.ejemplo.com/usuarios");
  const usuarios = await respuesta.json();
  return usuarios;
}
```

> Tip: el tipo de los datos reales (`Usuario[]`, etc.) se define con `interface` (como en [04-arrays-objetos](04-arrays-objetos.md)) y se usa en el `Promise<Tipo>`.

## Funciones como tipos reutilizables

Puedes darle nombre al tipo de una función:

```ts
type Cambiador = (valor: string) => string;

const mayus: Cambiador = (valor) => valor.toUpperCase();
const exclamar: Cambiador = (valor) => valor + "!";
```

Esto se usa mucho para tipar **props que son funciones en React** (eventos, handlers).

---

## Siguiente paso

[06. Tipos avanzados](06-tipos-avanzados.md) → union, `interface` vs `type`, generics y `any`/`unknown`. Ahí está la base para tipar hooks de React.
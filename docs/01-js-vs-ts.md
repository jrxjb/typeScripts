# 01. JavaScript vs TypeScript

## ¿Qué es TypeScript?

TypeScript es **JavaScript con tipos**. Fue creado por Microsoft como una capa encima de JavaScript.

JavaScript es interpretado por el navegador (o Node.js). TypeScript no se ejecuta tal cual: el compilador de TypeScript (`tsc`) lo **convierte (compila) a JavaScript** antes de ejecutarse.

```
TypeScript (.ts)  --compila-->  JavaScript (.js)  --ejecuta-->  Navegador / Node / Next.js
```

## La fórmula simple

> **TypeScript = JavaScript + tipos**

Todo lo que sabes de JavaScript (condicionales, bucles, `map`, `filter`, objetos, `length`, `switch`) **sigue existiendo y funciona igual**. TypeScript solo añade una capa de **tipos** encima para que el código sea más predecible y los errores se detecten antes (en tiempo de compilación, no solo en ejecución).

### Ejemplos iguales en JS y TS

```js
// JavaScript
const saludo = "hola";
console.log(saludo.length);
```

```ts
// TypeScript
const saludo: string = "hola";
console.log(saludo.length);
```

La única diferencia es `: string`. El resto del código es 100 % igual.

## ¿Qué ganas con TypeScript?

Sin tipos, un error como este solo aparece cuando el programa corre:

```js
// JavaScript: no hay error hasta que se ejecuta en el navegador
function saludar(nombre) {
  return "Hola " + nombre.toUpperCase();
}
saludar(123); // 💥 TypeError: nombre.toUpperCase is not a function
```

Con tipos, el editor y el compilador te avisan **antes de ejecutarlo**:

```ts
// TypeScript: error AL COMPILAR
function saludar(nombre: string): string {
  return "Hola " + nombre.toUpperCase();
}
saludar(123); // ❌ Error: Argument of type 'number' is not assignable to type 'string'
```

## ¿Cómo se instala y se usa?

No es obligatorio para leer esta guía, pero para practicar:

```bash
# Instalar TypeScript globalmente
npm install -g typescript

# Compilar un archivo .ts y ver errores
npx tsc miarchivo.ts

# Ver la configuración
npx tsc --init
```

Un `tsconfig.json` es el que dicta cómo compila tu proyecto (qué archivos incluye, hacia dónde emite el JS, etc.). Next.js ya lo crea por ti.

## ¿Y Next.js?

Next.js y React **ya funcionan con TypeScript integrado**. Cuando creas un proyecto Next.js con `--ts`, ya trae `tsconfig.json`, los tipos de React y la compilación configurada.

```bash
npx create-next-app@latest miapp --ts
```

Los archivos de componentes en React/Next que usan JSX se llaman `.tsx`; los que solo tienen lógica, `.ts`.

**En resumen:** no tienes que montar nada; el tipo se *infiere* solo, y tú usas las anotaciones para decirle exactamente qué tipos esperas.

---

## Siguiente paso

La sintaxis común es idéntica entre JS y TS. Lo revisamos tema por tema en el [siguiente archivo](02-sintaxis-comun.md).
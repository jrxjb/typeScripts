# typeScripts

Guía de TypeScript para aprender rápido y aplicarlo en **React** y **Next.js**.

> **Regla de oro:** TypeScript = JavaScript + tipos. La sintaxis (llaves, `if`, `switch`, `map`, `length`, objetos) es la misma. Lo único que cambia es que TypeScript **anota tipos** a los datos.

## Mapa de aprendizaje

Lee en este orden. Cada tema se apoya en el anterior.

| # | Tema | ¿Qué vas a aprender? |
|---|------|----------------------|
| 1 | [01-js-vs-ts](docs/01-js-vs-ts.md) | Qué es TypeScript, cómo se instala y por qué Next.js ya lo trae |
| 2 | [02-sintaxis-comun](docs/02-sintaxis-comun.md) | Comparativa JS vs TS: condicionales, llaves, switch, map, length |
| 3 | [03-tipos-basicos](docs/03-tipos-basicos.md) | Primitivos, inferencia y anotación de tipos |
| 4 | [04-arrays-objetos](docs/04-arrays-y-objetos.md) | Arrays, objetos y tuplas con tipos |
| 5 | [05-funciones](docs/05-funciones.md) | Parámetros, retornos y callbacks tipados |
| 6 | [06-tipos-avanzados](docs/06-tipos-avanzados.md) | Union, interface vs type, generics y `any`/`unknown` |
| 7 | [07-antes-react-next](docs/07-antes-de-react-next.md) | Lo que de TypeScript debes saber ANTES de React/Next (hooks, props, eventos, fetch) |
| 8 | [08-clases-y-oop](docs/08-clases-y-oop.md) | Clases, modificadores, enums y decoradores. La base para Nest.js |
| 9 | [09-react-next-extra](docs/09-react-next-extra.md) | Lo que falta para React/Next: ReactNode, useRef, keyof/typeof/as const, utilidades y tsconfig |

---

## Por qué estudiar TypeScript antes que React

Si ya hiciste ejercicios de React, sabrás que desbloqueaste al ver **cómo se tipan los hooks y las props**. Esta guía cubre exactamente eso: primero las bases de TypeScript (archivos 1 al 6) y luego la aplicación directa en React/Next.js (archivo 7), para que el aprendizaje sea más rápido y ordenado.

## Cómo practicar (recomendado)

- Escribe cada ejemplo de `// JavaScript` en un `.js` y después convierte el bloque de `// TypeScript` en un `.ts` (o `.tsx` para React).
- Ejecuta `npx tsc nombre.ts` para compilar y ver los errores de tipo en acción.
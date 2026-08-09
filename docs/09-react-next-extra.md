# 09. TypeScript extra para React y Next

> **La idea central de este archivo:** tu guía ya cubre el 80 % de lo que vas a usar en React/Next. Esto es el 20 % que falta: tipar `children`, `useRef`, Claves y constantes con `keyof`/`as const`, utilidades (`Record`, `Readonly`, `ReturnType`...), `import type` y el `tsconfig` de verdad.

## 1. Tipar `children`: `React.ReactNode`

Todo componente que envuelve contenido recibe `children`. En TypeScript se tipa con `React.ReactNode` (casi cualquier cosa que React sabe renderizar: texto, JSX, arrays, otro componente).

```tsx
interface CajaProps {
  titulo: string;
  children: React.ReactNode; // el contenido interno
}

export function Caja({ titulo, children }: CajaProps) {
  return (
    <div>
      <h3>{titulo}</h3>
      {children}
    </div>
  );
}
```

Uso:

```tsx
<Caja titulo="Perfil">
  <p>Contenido libre</p>   {/* ✅ children */}
</Caja>

<Caja titulo="Perfil" />   {/* ❌ Falta la prop children */}
```

Si quieres permitir que le pases o no contenido, hazlo opcional: `children?: React.ReactNode`.

> **Diferencia común en entrevistas:** `React.ReactNode` es **lo más amplio** (incluye `null`, `undefined`, arrays, fragmentos). Se tipa `children` con `ReactNode`; se usa `JSX.Element` solo cuando es exactamente **un** elemento JSX.

## 2. Tipar `useRef<T>`

`useRef` crea una "caja mutable" que **no provoca re-renderizados** cuando cambias su valor. Sirve para input focus, timers, valores previos.

```tsx
function MiForm() {
  const inputRef = useRef<HTMLInputElement>(null); // ref a un input

  function enfocar() {
    inputRef.current?.focus(); // '?' porque puede ser null al inicio
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={enfocar}>Enfocar</button>
    </>
  );
}
```

Tips:
- `useRef<HTMLInputElement>(null)` → ref a un elemento del DOM.
- `useRef<number>(0)` → contador que no re-renderiza:
  ```tsx
  const veces = useRef(0);
  veces.current++; // cambia el valor SIN re-renderizar (a diferencia de useState)
  ```

## 3. Tipar Context: `createContext` + `useContext`

Contexto global tipado (tema, sesión, idioma...). La clave es tipar el valor **y el estado inicial**:

```tsx
interface Sesion {
  usuario: string;
  esAdmin: boolean;
}

// createContext<T>: el valor puede ser Sesion o null si no hay proveedor aún
const SesionContext = createContext<Sesion | null>(null);

export function App() {
  return (
    <SesionContext.Provider value={{ usuario: "Ana", esAdmin: true }}>
      <Perfil />
    </SesionContext.Provider>
  );
}
```

Y para consumirlo, un custom hook que obliga a que exista el contexto:

```tsx
function useSesion(): Sesion {
  const contexto = useContext(SesionContext);
  if (!contexto) throw new Error("useSesion debe usarse dentro de un provider");
  return contexto;
}
```

Ese patrón (hook + throw si no existe) es **el estándar** en proyectos Next/React con contexto.

## 4. `keyof`, `typeof` y `as const`: tipar claves y constantes

### `typeof` — extraer el tipo de un valor ya existente

```ts
const usuario = { nombre: "Ana", edad: 30 };
type Usuario = typeof usuario; // { nombre: string; edad: number }
```

### `keyof` — las claves (propiedades) de un tipo

```ts
type Usuario = { nombre: string; edad: number };
type ClavesUsuario = keyof Usuario; // "nombre" | "edad"
```

Útilísimo con funciones que toman "una de las claves":

```ts
function obtenerProp(usuario: Usuario, clave: keyof Usuario) {
  return usuario[clave];
}
obtenerProp(usuario, "nombre"); // ✅
obtenerProp(usuario, "apellido"); // ❌ Error: no está en las claves
```

### `as const` — valores literales fijos (no mutables)

Duerme como `keyof` su pareja ideal. `as const` le dice a TS "este valor no cambia y no se amplía a su tipo general":

```ts
const ROLES = { ADMIN: "admin", USER: "user" } as const;
// Sin as const: el tipo sería { ADMIN: string; USER: string }
// Con as const:   es { readonly ADMIN: "admin"; readonly USER: "user" }

type Rol = typeof ROLES[keyof typeof ROLES]; // "admin" | "user"
```

Combo súper usado para estados/diccionarios tipados sin escribir el union a mano:

```ts
const ESTADOS = { PENDIENTE: "PENDIENTE", PAGADO: "PAGADO" } as const;
type Estado = typeof ESTADOS[keyof typeof ESTADOS]; // "PENDIENTE" | "PAGADO"
```

## 5. Utilidades extra: `Record`, `Readonly`, `Required`, `ReturnType`, `NonNullable`

Ya viste `Omit`, `Pick`, `Partial`. Estas completan el set de las más usadas:

```ts
// Record<Claves, Valor>: objeto donde TODAS las claves tienen el mismo tipo
const permisos: Record<string, boolean> = { admin: true, user: false };

// Readonly<T>: todas las propiedades de solo lectura
type UsuarioCongelado = Readonly<{ nombre: string; edad: number }>;

// Required<T>: el contrario de Partial, todo obligatorio
type Completo = Required<{ nombre?: string; edad?: number }>; // ambos requeridos

// ReturnType<F>: el tipo de retorno de una función
type Resultado = ReturnType<typeof miFuncion>;

// NonNullable<T>: elimina null y undefined
type Datos = NonNullable<{ nombre: string } | null | undefined>; // { nombre: string }
```

`Record` es el que más verás en proyectos React/Next para mapas de configuración y lookup de estados:

```ts
const LEYENDAS_ESTADO: Record<Estado, string> = {
  PENDIENTE: "En espera",
  PAGADO: "Pagado",
};
```

## 6. `import type` / `export type`: tipos separados de valores

Cuando un archivo exporta/importera **solo tipos**, la convención moderna (que Next favorece) es marcarlos con la palabra `type`. Beneficio: el compilador los **borra** al emitir JS (no generan import en producción).

```ts
// tipos.ts
export type UsuarioId = string;
export interface Usuario { id: UsuarioId; nombre: string }

// componente.tsx
import type { Usuario } from "./tipos"; // solo tipos
import { Componente } from "./ui";      // valores (código real)
```

**Regla rápida:** si importas algo que con React/Next compile como código que corre, no uses `type`; si es solo una forma/tipo (`interface`, `type`, props), usa `import type`. Algunos proyectos usan `import type { Props }` y así queda claro.

## 7. `tsconfig` en la práctica

El `tsconfig.json` es el archivo que el archivo [01](01-js-vs-ts.md) mencionó. Tres opciones que de verdad importan:

```jsonc
{
  "compilerOptions": {
    "strict": true,              // TODO el chequeo fuerte activado (recomendado, default en Next)
    "paths": { "@/*": ["./src/*"] },  // alias: import "@/components/Boton" sin rutas largas
    "moduleResolution": "bundler" // cómo entiende los imports (bundler = para Next/Vite)
  }
}
```

- **`strict: true`** → activa todas las normas (incluido "no `any` implícito"). Con `strict` TS es 10 veces más estricto y te ahorra bugs. Next ya lo trae activado.
- **`paths`** → crear aliases. Permite `import { Boton } from "@/ui/Boton"` en vez de `../../../ui/Boton`. Necesita config también en bundler.
- **`moduleResolution`** → la forma en que TypeScript busca qué hay detrás de cada `import`. En Next/Vite el valor moderno es `"bundler"`. No lo cambies salvo que sepas qué estás haciendo.

Ver texto opcional para verificar config desde el editor:

```jsonc
{
  "compilerOptions": { "noUnusedLocals": true, "noUnusedParameters": true }
}
```

## Cierre de la guía

Repaso de lo que ya sabes de TypeScript:

| # | Tema | Lo aplicas en |
|---|------|---------------|
| 1 | [JS vs TS](01-js-vs-ts.md) | cualquier proyecto |
| 2 | [Sintaxis común](02-sintaxis-comun.md) | cualquier proyecto |
| 3 | [Tipos básicos](03-tipos-basicos.md) | cualquier proyecto |
| 4 | [Arrays y objetos](04-arrays-y-objetos.md) | React/Next y Nest |
| 5 | [Funciones](05-funciones.md) | React/Next y Nest |
| 6 | [Tipos avanzados](06-tipos-avanzados.md) | React/Next y Nest |
| 7 | [Antes de React/Next](07-antes-de-react-next.md) | React/Next |
| 8 | [Clases y OOP](08-clases-y-oop.md) | Nest |
| 9 | Extra para React/Next | React/Next |

**Guía completa de TypeScript: de [01](01-js-vs-ts.md) a [09](09-react-next-extra.md).** Practica convirtiendo cada ejemplo JS → TS. ¡Éxito!
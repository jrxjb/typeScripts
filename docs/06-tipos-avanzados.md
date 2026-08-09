# 06. Tipos avanzados

Este archivo junta las herramientas de TypeScript que más vas a usar con React: **union types**, **`interface` vs `type`**, **generics**, y cuándo evitar `any`.

## Union types: un valor puede ser de varios tipos

Se usa `|` y ya lo vimos en [03-tipos-basicos](03-tipos-basicos.md). Lo clave en React es UNION de strings para estados:

```ts
type Status = "cargando" | "exito" | "error";

let estado: Status = "cargando";
estado = "exito";  // ✅
estado = "listo";  // ❌ Error: 'listo' no está en el tipo Status
```

También con objetos:

```ts
type Respuesta = { ok: true; datos: string } | { ok: false; error: string };
```

## `interface` vs `type`

Para tipar **objetos** podemos usar ambos. Diferencias útiles:

```ts
// interface: extensible con 'extends' (herencia)
interface Animal {
  nombre: string;
}
interface Perro extends Animal {
  ladra: boolean;
}
const firulais: Perro = { nombre: "Firulais", ladra: true }; // ✅

// type: puede ser union (no con interface)
type ID = string | number;            // ✅ union con type
interface Rectangulo { ancho: number; alto: number }
interface Circulo { radio: number }
type Forma = Rectangulo | Circulo;    // ✅ union de objetos
```

| | `interface` | `type` |
|---|---|---|
| Para objetos / props de React | ✅ muy común | ✅ también |
| `extends` / herencia | ✅ | ✅ con `&` (intersection) |
| Union `a \| b` | ❌ no | ✅ sí |
| Union de primitivos | ❌ no | ✅ |

**Regla práctica para React:** muchos los usan intercambiables; para **props** verás `interface Props` en bases de código, y con `type Props` en muchos proyectos. Usa `interface` para objetos y `type` cuando necesites union. Lo importante: elige una y sé consistente.

## Generics: la función se usa "con un tipo que se pasa por parámetro"

Un **generic** es una función o tipo que funciona con **cualquier tipo** y le dice exactamente cuál es. Se escribe con `<T>` (la `T` se reemplaza por el tipo real al usarlo).

### Ejemplo clásico: función que devuelve lo mismo que recibe

```ts
function identidad<T>(valor: T): T {
  return valor;
}

const a = identidad<string>("hola");   // a es string
const b = identidad<number>(42);       // b es number
const c = identidad(42);               // TS infiere: c es number
```

Esto es EXACTAMENTE lo mismo que `useState`:

```ts
const [count, setCount] = useState<number>(0); // count: number
const [nombre, setNombre] = useState<string>(""); // nombre: string
```

### Generic con arrays

```ts
function primerElemento<T>(lista: T[]): T | undefined {
  return lista[0];
}
const x = primerElemento(["a", "b"]); // retorna string | undefined
const y = primerElemento([1, 2]);      // retorna number | undefined
```

En React verás `useState<T>` o `Array<T>`, que ya viste.

### Ganando control con `Omit`, `Pick`, `Partial` (muy usados al tipar props)

Son "utilidades" que transforman tipos:

```ts
interface Usuario {
  id: number;
  nombre: string;
  email: string;
  password: string;
}

type UsuarioPublico = Omit<Usuario, "password">; // { id, nombre, email }
type SoloNombre = Pick<Usuario, "nombre">;       // { nombre: string }
type Formulario = Partial<Usuario>; // todas las props opcionales
```

En React es muy útil para componentes: por ejemplo, que el que use tu componente **no pueda pasar** `password`.

## `any` vs `unknown` vs `never`

| Tipo | Qué significa | Uso |
|------|---------------|-----|
| `any` | "no compruebes nada" | evitar; solo para migrar código |
| `unknown` | "no sé qué es, verifica antes de usarlo" | datos externos no validados |
| `null` | ausencia de valor | datos que pueden faltar al inicio |
| `never` | nunca retorna / función que lanza error | casos raros |

Diferencia `any` vs `unknown` en la práctica:

```ts
let cualquier: any = "x";
let desconocido: unknown = "y";

cualquier.loQueSea();     // ✅ sin error (peligroso)
desconocido.toLowerCase(); // ❌ Error: hay que verificarlo antes
if (typeof desconocido === "string") {
  desconocido.toLowerCase(); // ✅ ahora sí
}
```

### Los componentes de React usan `unknown` cuando:

La API te devuelve algo y aún no le pusiste un `interface`. En la práctica tipas el `Promise<T>` con tu `interface` (ver [05-funciones](05-funciones.md)).

## Ejemplo combinado (estilo React)

```ts
interface Props {
  items: string[];
  onSeleccion?: (id: string) => void;
  estado: "cargando" | "listo";
}

type ListaProps = {
  items: string[];
  seleccionar: (id: string) => void;
};
```

Esto es exactamente lo que verás en [07-antes-de-react-next](07-antes-de-react-next.md).

---

## Siguiente paso

[07. Antes de React y Next.js](07-antes-de-react-next.md) → la guía final: qué dominar y cómo se aplica en hooks, eventos, fetch y Server Components.
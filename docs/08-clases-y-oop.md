# 08. Clases y OOP (base para Nest)

> **La idea central de este archivo:** las clases **existen en JavaScript**, pero TypeScript las **potencia** con cosas que JS no tiene: modificadores (`private`, `protected`, `readonly`), atajos de constructor, `implements`, `abstract`, **enums** y **decoradores**. Este es el bloque que necesitas para **Nest.js**, porque Nest es *puro* clases y decoradores.

## ¿Por qué este archivo antes de Nest?

React/Next son sobre todo **funciones y objetos**. Nest, en cambio, gira alrededor de **clases**: controllers, services, módulos, guardas... todo es una clase. Y los **decoradores** (`@Controller`, `@Injectable`) son la forma en que Nest sabe qué hace cada clase.

## `class` en JavaScript vs TypeScript

La base es la misma: una clase tiene un `constructor` y métodos.

```js
// JavaScript
class Usuario {
  constructor(nombre, edad) {
    this.nombre = nombre;
    this.edad = edad;
  }
  presentarse() {
    return `${this.nombre} tiene ${this.edad} años`;
  }
}
```

```ts
// TypeScript: la diferencia es que las propiedades y parámetros llevan tipo
class Usuario {
  nombre: string;
  edad: number;

  constructor(nombre: string, edad: number) {
    this.nombre = nombre;
    this.edad = edad;
  }
  presentarse(): string {
    return `${this.nombre} tiene ${this.edad} años`;
  }
}

const ana = new Usuario("Ana", 30); // ✅
const pepe = new Usuario(); // ❌ Error: faltan nombre y edad
```

Solo cambia el **tipado** (misma historia de siempre). Lo nuevo viene ahora.

## Modificadores de acceso: `public`, `private`, `protected`, `readonly`

Esto **no existe en JavaScript**. Define quién puede leer o modificar una propiedad:

| Modificador | ¿Qué permite? |
|---|---|
| `public` | Accesible desde cualquier lugar (es el default) |
| `private` | Solo dentro de la clase |
| `protected` | Dentro de la clase y sus hijas |
| `readonly` | Solo lectura: no se puede reasignar |

```ts
class Cuenta {
  public titular: string;     // accesible fuera (default)
  private saldo: number;      // solo dentro de la clase
  readonly numero: string;    // no se puede reasignar

  constructor(titular: string, saldo: number, numero: string) {
    this.titular = titular;
    this.saldo = saldo;
    this.numero = numero;
  }

  consultarSaldo(): number {
    return this.saldo; // ✅ sí puede, está dentro de la clase
  }
}

const c = new Cuenta("Ana", 100, "1234");
c.titular;          // ✅ public
c.numero = "9999";  // ❌ Error: readonly no se reasigna
c.saldo;            // ❌ Error: 'saldo' is private
```

> **Regla de Nest:** en los services vas a ver `private` casi siempre: los datos internos (como conexiones o repositorios) no deben exponerse fuera.

## Parameter properties: el atajo del constructor

Escribir `nombre: string` como parámetro y después `this.nombre = nombre` es repetitivo. TypeScript lo compacta en **una línea**. Esto es el estilo que verás en **todas** las clases de Nest:

```ts
// Largo
class A {
  private repo: Repo;
  constructor(repo: Repo) {
    this.repo = repo;
  }
}

// Atajo: se declara la propiedad Y se asigna en los parámetros del constructor
class A {
  constructor(private repo: Repo) {}
}
```

El modificador (`private`, `public`, etc.) delante del parámetro del constructor crea y asigna la propiedad automáticamente.

```ts
class Service {
  constructor(private apiUrl: string, public nombre: string) {}
  // apiUrl es private y nombre es public, sin más líneas
}
```

> Mirá un service típico de Nest: `constructor(private readonly usersService: UsersService) {}`. Ahora ya podés leer esa línea completa.

## `implements`: una clase que cumple un contrato

`interface` describe una forma; `implements` le dice a una clase **"debes cumplir esa forma"**. Si falta un método, TypeScript te avisa:

```ts
interface Repositorio {
  encontrar(id: number): string;
  guardar(dato: string): void;
}

class RepositorioUsuarios implements Repositorio {
  encontrar(id: number): string {
    return `usuario ${id}`;
  }
  guardar(dato: string): void {
    console.log("guardado", dato);
  }
}
// ✅ implementa los dos métodos
```

Si olvidas `guardar`, TypeScript da error al compilar. En Nest se usa para garantizar que todos los services tengan las mismas operaciones.

## `abstract class`: clase base que no se usa sola

Una clase abstracta es una "plantilla": define métodos que sirven también, y métodos que **obligatoriamente** deben implementar las hijas. No se puede instanciar.

```ts
abstract class Formateador {
  abstract plantilla(): string;         // las hijas DEBEN implementarlo

  imprimir(): void {                    // las hijas lo heredan tal cual
    console.log(this.plantilla());
  }
}

class Html extends Formateador {
  plantilla(): string { return "<h1>Hola</h1>"; } // ✅ obligatorio
}

class Txt extends Formateador {
  plantilla(): string { return "Hola"; }
}

// const f = new Formateador(); // ❌ Error: no se puede instanciar una abstract
new Html().imprimir(); // "<h1>Hola</h1>"
```

**¿`interface` o `abstract class`?** `interface` solo declara; `abstract` además puede traer **implementación** compartida (como `imprimir`). Usa `abstract` cuando las hijas compartan lógica real, `interface` cuando solo sea un contrato.

## Enums: algo que JavaScript NO tiene

Un `enum` es un conjunto de **constantes con nombre**. Este es puro TypeScript, no tiene equivalente directo en JS (que usa objetos congelados obligados a hacerlo a mano).

```ts
enum Role {
  ADMIN,
  USER,
  GUEST,
}
// ADMIN = 0, USER = 1, GUEST = 2 (valores numéricos automáticos)

enum Status {
  ACTIVO = "ACTIVO",     // se puede personalizar con strings
  BLOQUEADO = "BLOQUEADO",
}
```

Uso típico en Nest (roles de acceso, estados):

```ts
enum Estado {
  PENDIENTE = "PENDIENTE",
  PAGADO = "PAGADO",
  CANCELADO = "CANCELADO",
}

interface Pedido {
  id: number;
  estado: Estado;
}

const p: Pedido = { id: 1, estado: Estado.PAGADO }; // ✅
const q: Pedido = { id: 2, estado: "pagado" };      // ❌ Error: debe ser Enum Estado
```

Ventaja sobre los strings sueltos: **no puedes equivocarte de ortografía** (guardas `Estado.CANCELADO`, no `"canse lado"`).

## Decoradores: la sintaxis real de Nest

Un **decorador** es una función que **anota** una clase, método o propiedad con metadatos. Se escribe con `@nombre`. En JS todavía es experimental; en TypeScript es **la base de Nest**.

Los nombres de Nest que verás todo el tiempo:

```ts
@Controller("usuarios")     // esta clase es un controller de rutas
export class UsuariosController {
  @Get(":id")               // este método responde a GET /usuarios/:id
  encontrar(@Param("id") id: string) {
    return { id };
  }
}
```

En TypeScript **Necesitas** activar la opción en `tsconfig.json` (o que el framework te la active, como hace Nest):

```jsonc
{
  "experimentalDecorators": true
}
```

> No necesitas saber cómo *crear* un decorador para usar Nest; solo reconocerlo y leerlo: `@Controller` marca las rutas, `@Injectable` marca los services que se pueden inyectar.

## Ejemplo combinado estilo Nest (Controller + Service con DI)

Junto todo lo de este archivo en el patrón real de Nest:

```ts
@Injectable() // puede inyectarse a otros
export class UsuariosService {
  constructor(private readonly api: RepositorioDeUsuarios) {} // DI + readonly + parameter property
  // ...
}

@Controller("usuarios")
export class UsuariosController {
  constructor(private readonly usuariosService: UsuariosService) {} // Nest inyecta solo

  @Get()
  listar(): string[] {
    return this.usuariosService.obtenerTodos();
  }
}
```

Lo que ya puedes leer de ese código:

- `@Controller` / `@Injectable` / `@Get` → **decoradores**
- `constructor(private readonly ...)` → **parameter properties** + `readonly` + inyección de dependencias
- `private` mantiene el service interno fuera del alcance público
- la clase cumple un rol gracias a sus **decoradores**

---

## Siguiente paso

[09. TypeScript extra para React y Next](09-react-next-extra.md) → `ReactNode`, `useRef`, `keyof`, `as const`, utilidades y `tsconfig` en la práctica.
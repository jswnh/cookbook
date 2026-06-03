## 1. Core Generic Type Definitions

Generics allow code reuse across different data types while preserving compile-time type checking, static analysis, and autocompletion in TypeScript.

| Generic Concept        | Definition / Mechanic                                                            | Primary Use Case                                                                | Example                                                              |
| :--------------------- | :------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :------------------------------------------------------------------- |
| **Generic Classes**    | Classes declared with one or more type parameters `<T>` acting as placeholders.  | Implementing custom data structures, wrappers, or universal business managers.  | `class Payload<T> { constructor(public data: T) {} }`                |
| **Generic Functions**  | Functions/Methods declared with type parameters independent of their class.      | Utility functions like parsing, mapping, swapping, or serialization.            | `function logData<T>(message: T): void { console.log(message); }`    |
| **Generic Interfaces** | Interfaces that expose type-parameterized signatures to be implemented by types. | Decoupling architectures via standard contracts (e.g. Repositories, Handlers).  | `interface IRepository<T> { getByIdAsync(id: string): Promise<T>; }` |
| **Generic Type Aliases**| Custom type mappings utilizing type parameters to define shapes or wrappers.     | Value-type layouts featuring automatic immutability or nested field maps.       | `type Frame<T> = { content: T };`                                    |

---

## 2. Generic Constraints (`extends` Clauses)

Constraints instruct the TypeScript compiler about what capabilities a type argument must satisfy. This avoids unsafe runtime type-casting and unlocks access to specific properties/methods on the type parameter.

| Constraint Syntax          | Mechanic                                                               | Primary Use Case                                                                | Example                                                      |
| :------------------------- | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :----------------------------------------------------------- |
| **`T extends object`**     | Type must be a non-primitive type.                                     | Ensuring the type is an object that can hold properties.                        | `class CacheStore<T extends object> { }`                     |
| **`T extends string`**     | Type must be assignable to a string.                                   | Restricting values to basic string inputs or string literals.                   | `function process<T extends string>(val: T) { }`             |
| **`T extends BaseClass`**  | Type must inherit from or exactly match the designated class.           | Standard OOP hierarchies where you limit generic operations to a family.        | `class Fleet<T extends Vehicle> { }`                         |
| **`T extends IInterface`** | Type must implement the specified interface contract/shape.             | Enforcing explicit features like comparability or disposal.                     | `function run<T extends Disposable>(engine: T) { }`          |
| **`K extends keyof T`**    | Enforces that type `K` is a valid property key of type `T`.            | Type-safe property access and lookup functions.                                 | `function getProp<T, K extends keyof T>(obj: T, key: K) { }` |

---

## 3. Generic Variance Operators (`in` and `out`)

TypeScript 4.7+ introduced explicit variance annotations on type parameters. While TypeScript normally infers variance automatically, annotations help speed up type-checking in complex type hierarchies.

| Operator    | Variance Type     | Behavior                                                                                                                       | Primary Use Case                                                     | Example                                                    |
| :---------- | :---------------- | :----------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------- | :--------------------------------------------------------- |
| **`out T`** | **Covariant**     | Permits using a more derived type than originally expected. The type variable can only appear as an **output** (return value). | Read-only streams, lookup interfaces, or factories pushing data out. | `interface IProducer<out T> { output(): T; }`              |
| **`in T`**  | **Contravariant** | Permits using a less derived (more generic) type than expected. The type variable can only appear as an **input** (parameter). | Event sinks, consumers, log targets, or comparers receiving data.    | `interface IReceiver<in T> { input(data: T): void; }`      |

---

## 4. Advanced TypeScript Generic Features

TypeScript supports powerful type manipulation features that have no direct equivalents in C# or Python, enabling dynamic type transformations.

### Conditional Types

Allows declaring types that choose one of two paths based on a relationship test (analogous to ternary operators).

```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false
```

### Mapped Types

Allows creating new types by transforming properties of an existing type.

```typescript
type ReadonlyFields<T> = {
    readonly [P in keyof T]: T[P];
};

interface User {
    id: string;
    name: string;
}

type ReadonlyUser = ReadonlyFields<User>;
// All properties are now readonly: { readonly id: string; readonly name: string; }
```

### Template Literal Types

Generates string types by combining literals.

```typescript
type Direction = "top" | "bottom" | "left" | "right";
type MarginClass = `margin-${Direction}`;

// MarginClass is now: "margin-top" | "margin-bottom" | "margin-left" | "margin-right"
```

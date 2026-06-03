## 1. Primitive Types

TypeScript has primitive types that represent single immutable values. These are stored directly (often on the stack or highly optimized in engine memory).

### Numeric & Basic Primitive Types

| Keyword / Type  | JS / TS Type      | C# Equivalent    | Description                                             | Example                                    |
| :-------------- | :---------------- | :--------------- | :------------------------------------------------------ | :----------------------------------------- |
| **`number`**    | `number`          | `double`         | Double-precision 64-bit IEEE 754 float (all numbers)   | `const pi: number = 3.14159;`              |
| **`bigint`**    | `bigint`          | `BigInteger`     | Arbitrary-precision integer (represented with `n` suffix)| `const largeInt: bigint = 900719925474n;`   |
| **`boolean`**   | `boolean`         | `bool`           | Logical value (`true` or `false`)                       | `const isActive: boolean = true;`          |
| **`string`**    | `string`          | `string`         | Immutable sequence of UTF-16 Unicode characters         | `const name: string = "John";`             |
| **`symbol`**    | `symbol`          | (Guid-like keys) | Unique and immutable primitive value, used as object keys| `const key: symbol = Symbol("id");`        |
| **`undefined`** | `undefined`       | `null` (default) | Represents a declared but unassigned variable           | `let value: undefined = undefined;`        |
| **`null`**      | `null`            | `null`           | Represents the intentional absence of an object value   | `const empty: null = null;`                |

---

## 2. Object and Reference Types

Reference types store the memory address (reference) of the data on the heap.

| Keyword / Type  | C# Analog         | Description                                               | Example                                                  |
| :-------------- | :---------------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`object`**    | `object`          | Represents any non-primitive type                         | `const data: object = { id: 1 };`                         |
| **`interface`** | `interface`       | Declares shape contracts (can be merged or extended)      | `interface User { name: string; }`                       |
| **`type`**      | `struct` / Alias  | Declares aliases for primitives, unions, tuples, or shapes| `type Point = { x: number; y: number; };`                |
| **`class`**     | `class`           | Defines blueprint objects supporting inheritance and state| `class Calculator { add(a: number): number { ... } }`    |
| **Function**    | `delegate`        | A typesafe signature representing callable methods        | `const log: (msg: string) => void = console.log;`        |
| **`enum`**      | `enum`            | User-defined set of named numeric or string constants     | `enum Status { Pending, Approved, Denied }`              |

---

## 3. Special Utility Types (Type System Only)

These types are construct-only features implemented by the TypeScript compiler to guide linter checks and disappear completely after compilation.

| Keyword / Type  | C# Analog         | Description                                               | Example                                                  |
| :-------------- | :---------------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`any`**       | `dynamic`         | Bypasses compile-time type checking completely            | `let rawData: any = JSON.parse(str);`                    |
| **`unknown`**   | `object`          | Type-safe counterpart of `any` (forces narrowing/casting) | `let value: unknown = fetch();\nif (typeof value === "string") { value.toUpperCase(); }` |
| **`void`**      | `void`            | Represents the return value of functions returning nothing| `function log(msg: string): void { console.log(msg); }`  |
| **`never`**     | (N/A)             | Represents values that will never occur (e.g. throws error)| `function fail(): never { throw new Error(); }`          |
| **Union (`\|`)**| (N/A)             | Allows a variable to hold values of multiple listed types | `let result: string \| number = 404;`                    |

## 1. Scalar Types (Value-Like Types)

Scalar types in PHP represent basic values. They are always copied by value (using copy-on-write optimization).

| Type Hint     | C# Equivalent    | Size / Nature    | Description                                             | Example                                    |
| :------------ | :--------------- | :--------------- | :------------------------------------------------------ | :----------------------------------------- |
| **`int`**     | `int` / `long`   | Platform Dep.    | Signed integer (64-bit on 64-bit systems)               | `$population = 1500000;`                    |
| **`float`**   | `double`         | 64-bit           | IEEE 754 double-precision floating-point number         | `$pi = 3.14159;`                           |
| **`bool`**    | `bool`           | 1-byte           | Logical boolean representation (`true` or `false`)      | `$isActive = true;`                        |
| **`string`**  | `string`         | Binary-safe byte | Sequence of bytes (does not enforce specific encoding)  | `$message = "Hello, World!";`              |

---

## 2. Compound and Reference-Like Types

Compound types store objects or collections of values. Objects in PHP are always passed by object handle reference, whereas arrays are passed by value.

| Type Hint     | C# Analog         | Description                                               | Example                                                  |
| :------------ | :---------------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`array`**   | `List` / `Dict`   | Unified ordered map (handles both numeric keys and strings)| `$data = [1, "two" => 2];`                               |
| **`object`**  | `object`          | Instance of a class (user-defined or `stdClass`)          | `$obj = (object) ['id' => 1];`                           |
| **`callable`**| `delegate`        | Represents function references (closures, arrow functions) | `$handler = fn(string $msg) => print($msg);`             |
| **`iterable`**| `IEnumerable`     | Alias accepting either an `array` or `Traversable` object | `function process(iterable $items) { }`                  |

---

## 3. Special Types

| Type Hint     | Description                                               | Example                                                  |
| :------------ | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`null`**    | Represents the absolute absence of a value                | `$value = null;`                                         |
| **`resource`**| A special variable holding a reference to an external resource (e.g. database link, file stream) | `$file = fopen("log.txt", "r");`                         |

---

## 4. Modern Type System Features (PHP 8.0 to 8.4)

PHP features a highly expressive static type-hinting engine resolved at runtime.

| Type Feature       | PHP Version | Description                                               | Example                                                  |
| :----------------- | :---------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **Union Types**    | PHP 8.0+    | Allows a variable to accept multiple distinct types       | `int\|float $number`                                     |
| **`mixed` type**   | PHP 8.0+    | Equivalent to `object\|callable\|array\|string\|int\|...`   | `function handle(mixed $data): void`                     |
| **Intersection**   | PHP 8.1+    | Requires a value to implement multiple interface contracts | `Iterator&Countable $collection`                         |
| **`never` type**   | PHP 8.1+    | Declares that a function must throw or call `exit()`      | `function redirect(): never { exit; }`                   |
| **DNF Types**      | PHP 8.2+    | Disjunctive Normal Form types (combining Union & Intersect)| `(Foo&Bar)\|null $instance`                              |
| **`false` / `true`**| PHP 8.2+    | Using true/false as standalone types                      | `function check(): true\|string`                         |

# 1. Functions / Methods

A function is a named block of code that performs an action. When attached to a class, it is called a method.

```typescript
function greet(): void {
    console.log("Hello, World!");
}

greet();
```

Output:

```text
Hello, World!
```

---

# 2. Method Parameters

Methods can accept typed input values.

```typescript
function greet(name: string): void {
    console.log(`Hello, ${name}!`);
}

greet("John");
```

Output:

```text
Hello, John!
```

---

# 3. Returning Values

```typescript
function add(a: number, b: number): number {
    return a + b;
}

const result = add(10, 20);

console.log(result);
```

Output:

```text
30
```

---

# 4. Arrow Functions (Expression-Bodied Methods)

Useful for short, inline functions.

```typescript
const square = (number: number): number => number * number;

console.log(square(5));
```

Output:

```text
25
```

---

# 5. Optional Parameters

## Using `?` (Undefined fallback)

```typescript
function greet(name: string, greeting?: string): void {
    console.log(`${greeting ?? "Hello"}, ${name}!`);
}

greet("John");
```

---

## Default Parameter Values

```typescript
function greet(name: string, greeting: string = "Hello"): void {
    console.log(`${greeting}, ${name}!`);
}

greet("John");
greet("Jane", "Welcome");
```

---

# 6. Named Arguments (Parameter Destructuring)

TypeScript matches C#'s named arguments by destructuring an object parameter.

```typescript
interface UserArgs {
    name: string;
    age: number;
    email: string;
}

function createUser({ name, age, email }: UserArgs): void {
    console.log(name);
}

createUser({
    name: "John",
    age: 25,
    email: "john@example.com"
});
```

---

# 7. Function Overloading

TypeScript supports overloading by providing multiple signatures followed by a single implementation.

```typescript
// Overload signatures
function add(a: number, b: number): number;
function add(a: string, b: string): string;

// Implementation signature
function add(a: any, b: any): any {
    return a + b;
}

console.log(add(5, 10));      // 15
console.log(add("5", "10"));  // "510"
```

---

# 8. ref Parameters (Object Wrapper)

JavaScript passes primitives by value and objects by reference. To modify a primitive value outside the scope of a function, wrap it in an object.

```typescript
interface Ref<T> {
    value: T;
}

function increment(ref: Ref<number>): void {
    ref.value++;
}

const number: Ref<number> = { value: 10 };

increment(number);

console.log(number.value);
```

Output:

```text
11
```

---

# 9. out Parameters (Tuple Unpacking)

TypeScript returns multiple values using array tuples, which can be destructured upon return.

```typescript
function getUser(): [string, string] {
    const firstName = "John";
    const lastName = "Doe";
    return [firstName, lastName];
}

const [first, last] = getUser();

console.log(`${first} ${last}`);
```

---

# 10. Rest Parameters (`...params`)

Accepts a variable number of arguments (analogous to `params`).

```typescript
function sum(...numbers: number[]): number {
    return numbers.reduce((acc, val) => acc + val, 0);
}

console.log(sum(1, 2, 3, 4, 5));
```

Output:

```text
15
```

---

# 11. Local Functions (Nested Functions)

```typescript
function process(): void {
    function add(a: number, b: number): number {
        return a + b;
    }

    console.log(add(10, 20));
}

process();
```

---

# 12. Instance Methods

Require a class instance.

```typescript
class Calculator {
    public add(a: number, b: number): number {
        return a + b;
    }
}

const calculator = new Calculator();

console.log(calculator.add(10, 20));
```

---

# 13. Static Methods

Belong to the class type instead of an instance.

```typescript
class MathHelper {
    public static double(value: number): number {
        return value * 2;
    }
}

console.log(MathHelper.double(10));
```

---

# 14. Accessors (Properties)

TypeScript supports custom getters and setters using the `get` and `set` keywords.

## Auto-Implemented Field

```typescript
class User {
    public name: string = "";
}
```

---

## Read-Only Field

```typescript
class User {
    public readonly name: string;

    constructor(name: string) {
        self.name = name;
    }
}
```

---

## Custom Getter and Setter

Adds validation or custom logic.

```typescript
class Product {
    private _price: number = 0;

    public get price(): number {
        return this._price;
    }

    public set price(value: number) {
        if (value < 0) {
            throw new Error("Price cannot be negative.");
        }
        this._price = value;
    }
}
```

---

# 15. Constructor Parameter Properties (C# Primary Constructor equivalent)

Declares and initializes fields directly in the constructor signature.

```typescript
class User {
    constructor(public readonly firstName: string, public readonly lastName: string) {}

    public get fullName(): string {
        return `${this.firstName} ${this.lastName}`;
    }
}

const user = new User("John", "Doe");

console.log(user.fullName);
```

Output:

```text
John Doe
```

---

# Summary

## Functions & Methods

- Arrow functions (`=>`)
- Optional and default parameters
- Named arguments using destructuring `{ a, b }`
- Method overloading signatures
- Rest parameters (`...numbers`)
- Local (nested) functions

## Properties / Accessors

- `get` and `set` accessor keywords
- `readonly` modifier
- Constructor parameter properties `constructor(public name: string)`

TypeScript methods and accessors map down to standard JavaScript function closures and Object prototype properties, augmented with compile-time type boundaries.

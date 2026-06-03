# 1. Conditional Statements

## if

Execute code when a condition is true.

```typescript
const age: number = 18;

if (age >= 18) {
    console.log("Adult");
}
```

---

## if-else

```typescript
const age: number = 16;

if (age >= 18) {
    console.log("Adult");
} else {
    console.log("Minor");
}
```

---

## if-else if

```typescript
const score: number = 85;

if (score >= 90) {
    console.log("A");
} else if (score >= 80) {
    console.log("B");
} else if (score >= 70) {
    console.log("C");
} else {
    console.log("Failed");
}
```

---

## Switch Statement

Traditional multi-branch check.

```typescript
const role: string = "Admin";

switch (role) {
    case "Admin":
        console.log("Full Access");
        break;

    case "Manager":
        console.log("Manage Resources");
        break;

    case "User":
        console.log("Limited Access");
        break;

    default:
        console.log("Unknown Role");
        break;
}
```

### Multiple Cases

```typescript
const today = new Date().getDay(); // 0 = Sunday, 6 = Saturday

switch (today) {
    case 0: // Sunday
    case 6: // Saturday
        console.log("Weekend");
        break;

    default:
        console.log("Weekday");
        break;
}
```

---

## Pattern Matching & Discriminated Unions

TypeScript provides type guards and narrowing, enabling pattern matching using `switch` or `if` statements over discriminated unions.

```typescript
type Shape = 
    | { kind: "circle"; radius: number }
    | { kind: "square"; size: number }
    | { kind: "rectangle"; width: number; height: number };

function getArea(shape: Shape): number {
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.size ** 2;
        case "rectangle":
            return shape.width * shape.height;
        default:
            const _exhaustiveCheck: never = shape;
            return _exhaustiveCheck;
    }
}
```

---

# 2. Loops

## for

Best when the iteration count is known.

```typescript
for (let i = 1; i <= 5; i++) {
    console.log(i);
}
```

---

## for-of (foreach equivalent)

Preferred for iterating collections.

```typescript
const languages: string[] = ["C#", "TypeScript", "Go"];

for (const language of languages) {
    console.log(language);
}
```

---

## while

```typescript
let count = 1;

while (count <= 5) {
    console.log(count);
    count++;
}
```

---

## do-while

Runs at least once.

```typescript
let count = 1;

do {
    console.log(count);
    count++;
} while (count <= 5);
```

---

## for await...of

Used with asynchronous streams.

```typescript
for await (const number of getNumbersAsync()) {
    console.log(number);
}
```

---

# 3. Loop Control Statements

## break

Stops execution of a loop.

```typescript
for (let i = 1; i <= 10; i++) {
    if (i === 5) {
        break;
    }
    console.log(i);
}
```

---

## continue

Skips the current iteration.

```typescript
for (let i = 1; i <= 5; i++) {
    if (i === 3) {
        continue;
    }
    console.log(i);
}
```

---

# 4. Arrays

Fixed-size or dynamic-size sequential collections.

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];

for (const number of numbers) {
    console.log(number);
}
```

---

# 5. Map<TKey, TValue>

Key-value storage.

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"],
    [3, "Bob"]
]);

for (const [id, name] of users) {
    console.log(`${id}: ${name}`);
}
```

---

# 6. Set<T>

Stores unique values.

```typescript
const skills = new Set<string>(["TypeScript", "SQL", "TypeScript", "C#"]);

for (const skill of skills) {
    console.log(skill);
}
```

Output:

```text
TypeScript
SQL
C#
```

---

# 7. Queue (FIFO using Array)

First In, First Out processing.

```typescript
const orders: string[] = [];

orders.push("Order A");
orders.push("Order B");
orders.push("Order C");

while (orders.length > 0) {
    console.log(orders.shift()); // shift() removes and returns the first element
}
```

---

# 8. Stack (LIFO using Array)

Last In, First Out tracking.

```typescript
const navigation: string[] = [];

navigation.push("Home");
navigation.push("Products");
navigation.push("Checkout");

while (navigation.length > 0) {
    console.log(navigation.pop()); // pop() removes and returns the last element
}
```

---

# 9. Nested Loops

Useful for matrices and multidimensional data.

```typescript
const matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6]
];

for (let row = 0; row < matrix.length; row++) {
    let rowContent = "";
    for (let col = 0; col < matrix[row].length; col++) {
        rowContent += `${matrix[row][col]} `;
    }
    console.log(rowContent);
}
```

---

# 10. Real-World Example

Combining control flow, loops, interfaces, and collections.

```typescript
interface User {
    name: string;
    age: number;
}

const users: User[] = [
    { name: "John", age: 25 },
    { name: "Jane", age: 17 },
    { name: "Bob", age: 32 }
];

for (const user of users) {
    const category = user.age >= 18 ? "Adult" : "Minor";
    console.log(`${user.name} - ${category}`);
}
```

---

# Summary

## Control Flow

- `if` / `else if` / `else`
- Switch statements with multi-case grouping
- Narrowing discriminated unions with exhaustive checks

## Loops

- `for`
- `for...of` (collections iteration)
- `while`
- `do...while`
- `for await...of` (async generators)

## Loop Controls

- `break`
- `continue`
- `return`

## Common Data Structures

| Data Structure         | TypeScript Equivalent        | Purpose                   |
| ---------------------- | ---------------------------- | ------------------------- |
| Array                  | `Array<T>` or `T[]`          | Dynamic collection        |
| Dictionary / Map       | `Map<K, V>` or `Record<K, V>`| Key-value storage         |
| HashSet                | `Set<T>`                     | Unique values             |
| Queue                  | `T[]` (using `push`/`shift`) | FIFO processing           |
| Stack                  | `T[]` (using `push`/`pop`)   | LIFO processing           |

Modern TypeScript encourages using higher-order functions (`map`, `filter`, `reduce`), discriminated unions, and object mapping lookups instead of heavy switch blocks to write readable and typesafe code.

# Dictionary Representation in TypeScript

In JavaScript and TypeScript, "dictionaries" can be represented in two primary ways:
1.  **Plain JavaScript Objects** using `Record<string, T>` or index signatures `{ [key: string]: T }` (best for string-based keys, JSON serialization, and simple configuration).
2.  **ES6 `Map<K, V>`** (best for arbitrary key types, frequent size queries, insertion order preservation, and dynamic write scenarios).

Both approaches are detailed below.

---

# 1. Creating a Dictionary

### Plain Object

```typescript
const users: Record<number, string> = {
    1: "John",
    2: "Jane",
    3: "Bob"
};
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"],
    [3, "Bob"]
]);
```

---

# 2. Adding Items

### Plain Object

```typescript
const users: Record<number, string> = {};

users[1] = "John";
users[2] = "Jane";
```

### ES6 Map

```typescript
const users = new Map<number, string>();

users.set(1, "John");
users.set(2, "Jane");
```

---

# 3. Accessing Values

### Plain Object

```typescript
const users: Record<number, string> = {
    1: "John",
    2: "Jane"
};

console.log(users[1]);
```

Output:

```text
John
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"]
]);

console.log(users.get(1));
```

Output:

```text
John
```

---

# 4. Updating Values

### Plain Object

```typescript
const users: Record<number, string> = { 1: "John" };
users[1] = "Johnny";

console.log(users[1]);
```

Output:

```text
Johnny
```

### ES6 Map

```typescript
const users = new Map<number, string>([[1, "John"]]);
users.set(1, "Johnny");

console.log(users.get(1));
```

Output:

```text
Johnny
```

---

# 5. Dictionary Count

### Plain Object

```typescript
const users: Record<number, string> = {
    1: "John",
    2: "Jane",
    3: "Bob"
};

console.log(Object.keys(users).length);
```

Output:

```text
3
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"],
    [3, "Bob"]
]);

console.log(users.size);
```

Output:

```text
3
```

---

# 6. Checking for Keys & Values

### Plain Object

```typescript
// Check Key
if (1 in users || Object.prototype.hasOwnProperty.call(users, 1)) {
    console.log("User exists");
}

// Check Value
if (Object.values(users).includes("John")) {
    console.log("Found");
}
```

### ES6 Map

```typescript
// Check Key
if (users.has(1)) {
    console.log("User exists");
}

// Check Value
if (Array.from(users.values()).includes("John")) {
    console.log("Found");
}
```

---

# 7. Safe Retrieval

TypeScript compiler warning: with plain objects, index access may return `undefined` if key is missing.

### Plain Object

```typescript
const nameValue: string | undefined = users[1];

if (nameValue !== undefined) {
    console.log(nameValue);
}
```

### ES6 Map

```typescript
const nameValue: string | undefined = users.get(1);

if (nameValue !== undefined) {
    console.log(nameValue);
}
```

---

# 8. Removing Items

### Plain Object

```typescript
const users: Record<number, string> = {
    1: "John",
    2: "Jane"
};

delete users[1];
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"]
]);

users.delete(1);
```

---

# 9. Clearing a Dictionary

### Plain Object

```typescript
let users: Record<number, string> = { 1: "John" };
users = {}; // Or delete keys individually
```

### ES6 Map

```typescript
const users = new Map<number, string>([[1, "John"]]);
users.clear();
```

---

# 10. Iterating Through a Dictionary

### Plain Object

```typescript
const users: Record<number, string> = {
    1: "John",
    2: "Jane"
};

for (const [id, name] of Object.entries(users)) {
    console.log(`${id}: ${name}`);
}
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"]
]);

for (const [id, name] of users) {
    console.log(`${id}: ${name}`);
}
```

Output:

```text
1: John
2: Jane
```

---

# 11. Iterating Keys

### Plain Object

```typescript
for (const id of Object.keys(users)) {
    console.log(id);
}
```

### ES6 Map

```typescript
for (const id of users.keys()) {
    console.log(id);
}
```

---

# 12. Iterating Values

### Plain Object

```typescript
for (const name of Object.values(users)) {
    console.log(name);
}
```

### ES6 Map

```typescript
for (const name of users.values()) {
    console.log(name);
}
```

---

# 13. Nested Dictionaries

### Plain Object

```typescript
const departments: Record<string, Record<string, string>> = {
    IT: {
        Manager: "John",
        Developer: "Jane"
    },
    HR: {
        Manager: "Bob"
    }
};

console.log(departments["IT"]["Developer"]);
```

Output:

```text
Jane
```

### ES6 Map

```typescript
const departments = new Map<string, Map<string, string>>([
    ["IT", new Map([["Manager", "John"], ["Developer", "Jane"]])],
    ["HR", new Map([["Manager", "Bob"]])]
]);

console.log(departments.get("IT")?.get("Developer"));
```

Output:

```text
Jane
```

---

# 14. Dictionaries with Types/Interfaces

```typescript
export interface User {
    id: number;
    name: string;
    email: string;
}

const users: Record<number, User> = {
    1: { id: 1, name: "John", email: "john@example.com" },
    2: { id: 2, name: "Jane", email: "jane@example.com" }
};

console.log(users[1].name);
```

Output:

```text
John
```

---

# 15. Functional Transformations (Map & Filter)

### Plain Object

```typescript
const users: Record<number, string> = { 1: "John", 2: "Jane", 3: "Bob" };

// Filter
const admins = Object.fromEntries(
    Object.entries(users).filter(([id]) => Number(id) > 1)
);

// Map/Select
const names = Object.values(users);
```

### ES6 Map

```typescript
const users = new Map<number, string>([
    [1, "John"],
    [2, "Jane"],
    [3, "Bob"]
]);

// Filter
const admins = new Map(
    Array.from(users.entries()).filter(([id]) => id > 1)
);

// Map/Select
const names = Array.from(users.values());
```

---

# 16. Converting a List to a Dictionary

```typescript
const usersList: User[] = [
    { id: 1, name: "John", email: "john@example.com" },
    { id: 2, name: "Jane", email: "jane@example.com" }
];

// To Plain Object
const userObject: Record<number, User> = usersList.reduce((acc, user) => {
    acc[user.id] = user;
    return acc;
}, {} as Record<number, User>);

// To ES6 Map
const userMap = new Map<number, User>(
    usersList.map(user => [user.id, user])
);
```

---

# 17. Merging Dictionaries

### Plain Object

```typescript
const first: Record<number, string> = { 1: "John", 2: "Jane" };
const second: Record<number, string> = { 3: "Bob", 4: "Alice" };

const merged = { ...first, ...second };
```

### ES6 Map

```typescript
const first = new Map<number, string>([[1, "John"], [2, "Jane"]]);
const second = new Map<number, string>([[3, "Bob"], [4, "Alice"]]);

const merged = new Map([...first, ...second]);
```

---

# 18. Real-World Example

Caching users by ID.

```typescript
export interface User {
    id: number;
    name: string;
}

const cache = new Map<number, User>();

cache.set(1, { id: 1, name: "John" });
cache.set(2, { id: 2, name: "Jane" });

const user = cache.get(1);
if (user) {
    console.log(user.name);
}
```

Output:

```text
John
```

---

# 19. Object vs Map Performance

| Operation   | Plain Object   | ES6 Map        |
| ----------- | -------------- | -------------- |
| Add / Set   | Fast           | Fast           |
| Lookup      | Fast           | Fast           |
| Remove      | Slow (`delete`)| Fast           |
| Iteration   | Moderate       | Fast           |

*Note: ES6 Maps are optimized for scenarios involving frequent additions and removals of key-value pairs.*

---

# Summary

## Plain Objects (`Record<K, V>`)
- Use for simple mapping configurations, JSON payloads, and compile-time key checking.
- Uses standard JS object brackets `{}` and the `Record<K, V>` type.

## ES6 Map (`Map<K, V>`)
- Use for high-frequency dynamic insertions/deletions, non-string keys, and when preserving insertion order is important.
- Uses `.set()`, `.get()`, `.has()`, `.delete()`, `.clear()`, and `.size`.

# JavaScript Methods & Functions

A comprehensive reference for functions, methods, binding, and modern built-in standard methods in modern JavaScript (ES6 to ES2024+).

---

## 1. Function Declarations, Expressions & Arrow Functions

### Function Declaration
Hoisted to the top of its enclosing scope. Can be called before it is defined.

```javascript
greet("Alice"); // Works due to hoisting

function greet(name) {
  return `Hello, ${name}!`;
}
```

### Function Expression
Not hoisted. The variable is hoisted (if declared with `var`), but remains uninitialized (`let`/`const` temporal dead zone).

```javascript
const add = function (a, b) {
  return a + b;
};

console.log(add(5, 3)); // 8
```

### Arrow Function (ES6)
Concise syntax, implicit return for single expressions, and lexical `this` binding (no own `this`, `arguments`, `super`, or `new.target`).

```javascript
// Concise body with implicit return
const square = (n) => n * n;

// Returning an object literal requires wrapping parentheses
const makeUser = (name, role) => ({ name, role });

console.log(square(6)); // 36
console.log(makeUser("Alex", "Admin")); // { name: 'Alex', role: 'Admin' }
```

---

## 2. Parameters & Arguments

### Default Parameters
Evaluated at call time if the argument is omitted or strictly `undefined`.

```javascript
function connect(host = "localhost", port = 5432, timeout = 3000) {
  return `Connecting to ${host}:${port} (timeout: ${timeout}ms)`;
}

console.log(connect()); // Connecting to localhost:5432 (timeout: 3000ms)
console.log(connect("192.168.1.1", undefined, 5000)); // Connecting to 192.168.1.1:5432 (timeout: 5000ms)
```

### Rest Parameters (`...args`)
Gathers indefinite trailing arguments into a true `Array` instance.

```javascript
function sum(multiplier, ...numbers) {
  return numbers.reduce((acc, curr) => acc + curr * multiplier, 0);
}

console.log(sum(2, 1, 2, 3, 4)); // 20
```

### Parameter Destructuring & Aliasing
Unpacks object or array arguments directly within the parameter list, supporting defaults and renames.

```javascript
function renderUser({ id, name: fullName, role = "Guest", contact: { email } = {} } = {}) {
  console.log(`[${id}] ${fullName} (${role}) - ${email}`);
}

renderUser({
  id: 101,
  name: "Sarah Connor",
  role: "Operator",
  contact: { email: "sarah@skynet-resistance.org" }
});
// [101] Sarah Connor (Operator) - sarah@skynet-resistance.org
```

### `arguments` Object (Legacy) vs Rest Parameters
`arguments` is an array-like object available only in non-arrow functions. Modern code prefers rest parameters (`...args`).

```javascript
function legacySum() {
  // arguments is array-like; must convert to array for array methods
  const args = Array.from(arguments);
  return args.reduce((a, b) => a + b, 0);
}
```

---

## 3. Scope, Closures & IIFE

### Closures
A closure is the combination of a function bundled together with references to its surrounding lexical environment. Closures allow functions to access outer variables even after the outer function has finished executing.

```javascript
function createCounter(initialValue = 0) {
  let count = initialValue; // Private state variable

  return {
    increment() {
      return ++count;
    },
    decrement() {
      return --count;
    },
    getCount() {
      return count;
    }
  };
}

const counter = createCounter(10);
console.log(counter.increment()); // 11
console.log(counter.increment()); // 12
console.log(counter.decrement()); // 11
console.log(counter.getCount());  // 11
```

### Immediately Invoked Function Expression (IIFE)
Executes immediately upon declaration. Used for module-like privacy or top-level scoping.

```javascript
const moduleResult = (() => {
  const secretKey = "super-secret-salt";
  return {
    generateToken(username) {
      return `${username}:${secretKey}`;
    }
  };
})();

console.log(moduleResult.generateToken("admin")); // admin:super-secret-salt
```

---

## 4. The `this` Keyword & Explicit Binding

### Understanding `this`
The value of `this` is determined by how a function is called:

| Call Style | Example | Value of `this` |
| :--- | :--- | :--- |
| **Object Method** | `obj.method()` | The receiver object `obj` |
| **Simple Function Call** | `fn()` | `undefined` in strict mode; global object (`window`/`globalThis`) in sloppy mode |
| **Constructor Call** | `new Fn()` | The newly instantiated object |
| **Arrow Function** | `() => {}` | Lexically inherited from enclosing scope |
| **Explicit Binding** | `fn.call(ctx)` | Provided `ctx` argument |

### Explicit Binding: `call()`, `apply()`, `bind()`

```javascript
function introduce(greeting, punctuation) {
  return `${greeting}, I am ${this.name}${punctuation}`;
}

const person = { name: "Leonardo" };

// 1. call(): invokes immediately with comma-separated arguments
console.log(introduce.call(person, "Hello", "!")); 
// "Hello, I am Leonardo!"

// 2. apply(): invokes immediately with array of arguments
console.log(introduce.apply(person, ["Welcome", "."])); 
// "Welcome, I am Leonardo."

// 3. bind(): returns a new function with this permanently bound
const boundIntro = introduce.bind(person, "Greetings");
console.log(boundIntro("?")); 
// "Greetings, I am Leonardo?"
```

---

## 5. Object Methods & Method Chaining

### Method Definition Shorthand & Computed Method Names

```javascript
const prefix = "calc";

const mathModule = {
  factor: 2,

  // Shorthand method syntax
  double(n) {
    return n * this.factor;
  },

  // Computed method name
  [`${prefix}_square`](n) {
    return n * n;
  },

  // Super call in object literal
  toString() {
    return `[MathModule with factor ${this.factor}]`;
  }
};

console.log(mathModule.double(5));       // 10
console.log(mathModule.calc_square(4));  // 16
```

### Method Chaining (Fluent Interface Pattern)
Return `this` from instance methods to enable chaining.

```javascript
class QueryBuilder {
  #query = {};

  where(field, value) {
    this.#query[field] = value;
    return this; // Enable chaining
  }

  limit(num) {
    this.#query.limit = num;
    return this;
  }

  build() {
    return { ...this.#query };
  }
}

const query = new QueryBuilder()
  .where("status", "active")
  .where("role", "admin")
  .limit(10)
  .build();

console.log(query); // { status: 'active', role: 'admin', limit: 10 }
```

---

## 6. Class Methods (Modern ES6 - ES2024)

Modern JavaScript classes support constructors, public and private instance fields, private methods (`#`), static methods, static initialization blocks, and accessors (`get`/`set`).

```javascript
class BankAccount {
  // Public instance field
  currency = "USD";

  // Private instance field
  #balance = 0;

  // Static public field
  static minimumDeposit = 10;

  // Static private field
  static #totalAccounts = 0;

  // Static initialization block (runs once on class evaluation)
  static {
    console.log("BankAccount class initialized");
  }

  constructor(owner, initialDeposit = 0) {
    if (initialDeposit < BankAccount.minimumDeposit) {
      throw new Error(`Minimum deposit is ${BankAccount.minimumDeposit}`);
    }
    this.owner = owner;
    this.#balance = initialDeposit;
    BankAccount.#totalAccounts++;
  }

  // Getter accessor
  get balance() {
    return `${this.#balance} ${this.currency}`;
  }

  // Setter accessor with validation
  set balance(value) {
    if (value < 0) throw new Error("Negative balance not allowed");
    this.#balance = value;
  }

  // Public instance method
  deposit(amount) {
    this.#validatePositive(amount);
    this.#balance += amount;
    return this;
  }

  // Private instance method (not callable outside the class)
  #validatePositive(amount) {
    if (amount <= 0) {
      throw new Error("Amount must be positive");
    }
  }

  // Static method
  static getTotalAccounts() {
    return BankAccount.#totalAccounts;
  }
}

const acc = new BankAccount("Alice", 100);
acc.deposit(50);
console.log(acc.balance); // "150 USD"
console.log(BankAccount.getTotalAccounts()); // 1
```

---

## 7. Generator Methods & Async Generators

### Generator Functions (`function*`)
Pause and resume execution using the `yield` keyword. Useful for custom iterables, lazy evaluation, and state machines.

```javascript
function* idGenerator(prefix = "id") {
  let count = 1;
  while (true) {
    yield `${prefix}_${count++}`;
  }
}

const gen = idGenerator("user");
console.log(gen.next().value); // "user_1"
console.log(gen.next().value); // "user_2"
console.log(gen.next().value); // "user_3"
```

### Delegating Generators (`yield*`)
Delegates to another generator or iterable.

```javascript
function* frontEnd() {
  yield "HTML";
  yield "CSS";
}

function* fullStack() {
  yield* frontEnd();
  yield "Node.js";
}

console.log([...fullStack()]); // ['HTML', 'CSS', 'Node.js']
```

### Async Generators & `for await...of`
Combines generators with asynchronous promises.

```javascript
async function* fetchPages(totalPages) {
  for (let page = 1; page <= totalPages; page++) {
    // Simulate async network request
    await new Promise((res) => setTimeout(res, 50));
    yield { page, data: [`item_${page}A`, `item_${page}B`] };
  }
}

(async () => {
  for await (const chunk of fetchPages(3)) {
    console.log(`Page ${chunk.page}:`, chunk.data);
  }
})();
```

---

## 8. Higher-Order Functions & Currying

A higher-order function either accepts a function as an argument, returns a function, or both.

### Higher-Order Function Example

```javascript
const withTiming = (fn) => (...args) => {
  const start = performance.now();
  const result = fn(...args);
  const end = performance.now();
  console.log(`${fn.name || "Function"} took ${(end - start).toFixed(2)}ms`);
  return result;
};

const heavyComputation = (limit) => {
  let sum = 0;
  for (let i = 0; i < limit; i++) sum += i;
  return sum;
};

const timedComputation = withTiming(heavyComputation);
timedComputation(1_000_000);
```

### Currying
Transforming a function with multiple arguments into a chain of single-argument functions.

```javascript
const curry = (fn) => {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return (...nextArgs) => curried.apply(this, args.concat(nextArgs));
  };
};

function multiply(a, b, c) {
  return a * b * c;
}

const curriedMultiply = curry(multiply);
console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24
```

---

## 9. Built-in Standard Methods Reference

### Modern Object Methods

| Method | Description | Example |
| :--- | :--- | :--- |
| `Object.keys(obj)` | Returns array of own enumerable string keys | `Object.keys({ a: 1 }) // ['a']` |
| `Object.values(obj)` | Returns array of own enumerable values | `Object.values({ a: 1 }) // [1]` |
| `Object.entries(obj)` | Returns `[key, value]` pairs | `Object.entries({ a: 1 }) // [['a', 1]]` |
| `Object.fromEntries(entries)` | Converts `[key, value]` pairs back to an object | `Object.fromEntries([['a', 1]]) // { a: 1 }` |
| `Object.assign(target, ...sources)` | Shallow copies properties to target | `Object.assign({}, { a: 1 }, { b: 2 })` |
| `Object.freeze(obj)` | Shallowly prevents mutation, addition, deletion | `Object.freeze(obj)` |
| `Object.seal(obj)` | Prevents adding/deleting properties, values can change | `Object.seal(obj)` |
| `Object.hasOwn(obj, prop)` | Modern safe replacement for `hasOwnProperty` (ES2022) | `Object.hasOwn(obj, "prop") // true/false` |
| `Object.groupBy(items, fn)` | Groups iterable items into an object (ES2024) | `Object.groupBy(users, u => u.role)` |

#### Example: `Object.groupBy` & `Object.fromEntries`

```javascript
const inventory = [
  { name: "Asparagus", type: "vegetable", quantity: 5 },
  { name: "Banana", type: "fruit", quantity: 0 },
  { name: "Cherries", type: "fruit", quantity: 12 },
  { name: "Carrot", type: "vegetable", quantity: 20 }
];

// ES2024 Native Object.groupBy()
const grouped = Object.groupBy(inventory, ({ type }) => type);
/*
{
  vegetable: [ { name: "Asparagus", ... }, { name: "Carrot", ... } ],
  fruit: [ { name: "Banana", ... }, { name: "Cherries", ... } ]
}
*/
```

---

### Modern Array Methods (Including ES2023 Non-Mutating Methods)

| Method | Mutates? | Description |
| :--- | :--- | :--- |
| `map(callback)` | No | Transforms every element into a new array |
| `filter(callback)` | No | Retains elements satisfying condition |
| `reduce(callback, init)` | No | Accumulates elements into a single value |
| `find(callback)` / `findIndex` | No | Finds first matching element or index |
| `findLast(callback)` / `findLastIndex` | No | Finds last matching element or index (ES2023) |
| `some(callback)` / `every` | No | Checks if any or all elements pass condition |
| `flat(depth)` / `flatMap(fn)` | No | Flattens nested arrays |
| `toSorted(compareFn)` | **No** | Immutable sort (returns new sorted array, ES2023) |
| `toReversed()` | **No** | Immutable reverse (returns new reversed array, ES2023) |
| `toSpliced(start, deleteCount, ...items)` | **No** | Immutable splice (returns new spliced array, ES2023) |
| `with(index, value)` | **No** | Replaces element at index immutably (ES2023) |

#### Non-Mutating Array Methods in Action

```javascript
const numbers = [3, 1, 4, 1, 5, 9];

// Original array remains unmodified!
const sorted = numbers.toSorted((a, b) => a - b);
console.log(sorted);  // [1, 1, 3, 4, 5, 9]
console.log(numbers); // [3, 1, 4, 1, 5, 9]

// Replace item at index 2 without mutating
const replaced = numbers.with(2, 42);
console.log(replaced); // [3, 1, 42, 1, 5, 9]

// Find last matching element
const lastEven = numbers.findLast((n) => n % 2 === 0);
console.log(lastEven); // 4
```

---

### String Methods Cheatsheet

```javascript
const text = "  JavaScript Web Platform  ";

// Modern String methods
console.log(text.trim());                  // "JavaScript Web Platform"
console.log(text.includes("Web"));         // true
console.log(text.startsWith("  Java"));    // true
console.log(text.replaceAll(" ", "_"));    // "__JavaScript_Web_Platform__"
console.log("5".padStart(3, "0"));         // "005"
console.log("item".padEnd(8, "."));        // "item...."

// Regex matchAll (returns iterator of all match capture groups)
const regex = /#(?<tag>[a-z]+)/g;
const str = "Tags: #javascript #web #code";
for (const match of str.matchAll(regex)) {
  console.log(match.groups.tag); // "javascript", "web", "code"
}
```

---

### Modern Promise Methods (ES2020 - ES2024)

| Method | Resolves When | Rejects When |
| :--- | :--- | :--- |
| `Promise.all(iterable)` | All promises fulfill | Any promise rejects (fails fast) |
| `Promise.allSettled(iterable)` | All promises settle (either fulfilled or rejected) | Never rejects |
| `Promise.race(iterable)` | First promise settles (fulfill or reject) | First promise rejects |
| `Promise.any(iterable)` | First promise fulfills | All promises reject (`AggregateError`) |
| `Promise.withResolvers()` | Returns `{ promise, resolve, reject }` directly (ES2024) | N/A |

#### Example: `Promise.withResolvers()` (ES2024)

```javascript
// Before ES2024:
let resolveFn, rejectFn;
const p = new Promise((res, rej) => {
  resolveFn = res;
  rejectFn = rej;
});

// Modern ES2024:
const { promise, resolve, reject } = Promise.withResolvers();

setTimeout(() => resolve("Data loaded!"), 500);
promise.then(console.log); // "Data loaded!"
```

---

### Deep Cloning: `structuredClone()`

Native global method for deep cloning objects, arrays, Maps, Sets, Dates, ArrayBuffers, and RegExp without `JSON.parse(JSON.stringify())` limitations (handles circular references!).

```javascript
const original = {
  date: new Date(),
  map: new Map([["key", "value"]]),
  nested: { count: 10 }
};

const copy = structuredClone(original);
copy.nested.count = 20;

console.log(original.nested.count); // 10 (completely detached clone)
```

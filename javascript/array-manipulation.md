# JavaScript Array Manipulation

A comprehensive, modern guide to creating, transforming, filtering, sorting, and manipulating arrays in JavaScript (ES6 through ES2024+).

---

## 1. Creating & Initializing Arrays

### Array Literals & Ranges

```javascript
// 1. Array literal
const numbers = [1, 2, 3, 4, 5];

// 2. Generate a numerical range [0, 1, 2, 3, 4]
const range0to4 = Array.from({ length: 5 }, (_, i) => i);

// 3. Generate a numerical range [1, 2, 3, 4, 5]
const range1to5 = Array.from({ length: 5 }, (_, i) => i + 1);

// 4. Pre-filling an array with primitive values
const zeros = new Array(4).fill(0); // [0, 0, 0, 0]

// CAUTION: fill() with objects copies the same reference!
// WRONG: All elements reference the exact same object
const badObjects = new Array(3).fill({}); 

// CORRECT: Generates distinct object instances
const goodObjects = Array.from({ length: 3 }, () => ({}));
```

### Converting Iterables to Arrays

```javascript
// Convert Set to Array (e.g. for deduplication)
const uniqueArray = Array.from(new Set([1, 2, 2, 3])); // [1, 2, 3]

// Convert NodeList / arguments / string using spread:
const chars = [..."hello"]; // ['h', 'e', 'l', 'l', 'o']
```

---

## 2. Accessing & Destructuring Elements

### Modern Indexing with `.at()` (ES2022)
Supports negative indices to count backwards from the end without calculating `array.length - 1`.

```javascript
const colors = ["red", "green", "blue", "yellow"];

console.log(colors.at(0));  // "red"
console.log(colors.at(-1)); // "yellow" (last item)
console.log(colors.at(-2)); // "blue"   (second-to-last item)
```

### Destructuring with Defaults & Rest Pattern

```javascript
const userRecord = ["Alice", 28, "Engineer", "Seattle", "Remote"];

// Destructure first two items, skip the third, capture remainder in rest array
const [name, age, , ...locations] = userRecord;

console.log(name);      // "Alice"
console.log(age);       // 28
console.log(locations); // ['Seattle', 'Remote']

// Swapping variables without temporary variables
let a = 1, b = 2;
[a, b] = [b, a];
console.log(a, b); // 2, 1
```

---

## 3. Mutating vs. Non-Mutating (Immutable) Operations

Modern JavaScript and UI frameworks (React, Vue, etc.) heavily emphasize **immutable updates** to avoid unexpected side-effects.

| Operation | Mutating (Modifies Original) | Non-Mutating (Returns New Array) |
| :--- | :--- | :--- |
| **Add to End** | `arr.push(item)` | `[...arr, item]` or `arr.concat(item)` |
| **Add to Start** | `arr.unshift(item)` | `[item, ...arr]` |
| **Remove from End** | `arr.pop()` | `arr.slice(0, -1)` |
| **Remove from Start** | `arr.shift()` | `arr.slice(1)` |
| **Insert / Replace / Delete** | `arr.splice(start, count, ...items)` | `arr.toSpliced(start, count, ...items)` (ES2023) |
| **Update Item at Index** | `arr[index] = val` | `arr.with(index, val)` (ES2023) |
| **Sort** | `arr.sort(compareFn)` | `arr.toSorted(compareFn)` (ES2023) |
| **Reverse** | `arr.reverse()` | `arr.toReversed()` (ES2023) |

### Modern Immutable Updates (ES2023)

```javascript
const original = ["a", "b", "c", "d"];

// 1. Immutable element replacement with .with()
const updated = original.with(1, "B_MODIFIED");
console.log(updated);  // ['a', 'B_MODIFIED', 'c', 'd']
console.log(original); // ['a', 'b', 'c', 'd'] (unchanged!)

// 2. Immutable splicing with .toSpliced()
// Remove 1 element at index 2 and insert 'NEW'
const spliced = original.toSpliced(2, 1, "NEW");
console.log(spliced);  // ['a', 'b', 'NEW', 'd']
console.log(original); // ['a', 'b', 'c', 'd'] (unchanged!)
```

---

## 4. Transforming & Flattening (`map`, `flat`, `flatMap`)

### `.map()`: Pure Transformation

```javascript
const products = [
  { id: 1, name: "Laptop", price: 1000 },
  { id: 2, name: "Mouse", price: 50 }
];

const pricesWithTax = products.map((item) => ({
  ...item,
  priceWithTax: item.price * 1.15
}));
```

### `.flat(depth)`: Flattening Nested Arrays

```javascript
const nested = [1, [2, [3, [4]]]];

console.log(nested.flat(1));        // [1, 2, [3, [4]]]
console.log(nested.flat(Infinity)); // [1, 2, 3, 4]
```

### `.flatMap()`: Map and Flatten in a Single Pass

```javascript
const sentences = ["Hello world", "JavaScript is awesome"];

// Split all sentences into a flat array of words
const words = sentences.flatMap((s) => s.split(" "));
console.log(words); // ['Hello', 'world', 'JavaScript', 'is', 'awesome']
```

---

## 5. Filtering & Deduplication

### Deduplicating Primitive Values

```javascript
const numbers = [1, 2, 2, 3, 4, 4, 5];

// Modern one-liner using Set
const unique = [...new Set(numbers)];
console.log(unique); // [1, 2, 3, 4, 5]
```

### Deduplicating Objects by Key (e.g. `id`)

```javascript
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 1, name: "Alice (Duplicate)" }
];

// Deduplicate by id keeping the first occurrence
const uniqueUsers = [...new Map(users.map((u) => [u.id, u])).values()];
console.log(uniqueUsers);
// [ { id: 1, name: "Alice (Duplicate)" }, { id: 2, name: "Bob" } ]
```

### Removing Specific Items Immutably

```javascript
const tasks = [
  { id: 101, title: "Deploy" },
  { id: 102, title: "Test" },
  { id: 103, title: "Refactor" }
];

// Remove task with id 102
const filteredTasks = tasks.filter((t) => t.id !== 102);
```

---

## 6. Searching & Finding

```javascript
const team = [
  { name: "Sarah", role: "Dev", score: 85 },
  { name: "John", role: "Design", score: 92 },
  { name: "Alex", role: "Dev", score: 95 },
  { name: "Mark", role: "Dev", score: 78 }
];

// 1. find(): First match
const firstDev = team.find((m) => m.role === "Dev");
console.log(firstDev.name); // "Sarah"

// 2. findLast() (ES2023): Last match without reversing
const lastDev = team.findLast((m) => m.role === "Dev");
console.log(lastDev.name); // "Mark"

// 3. findIndex() and findLastIndex()
const highScorerIndex = team.findLastIndex((m) => m.score > 90);
console.log(highScorerIndex); // 2 (Alex)

// 4. some() and every()
const hasDesigners = team.some((m) => m.role === "Design"); // true
const allPass = team.every((m) => m.score >= 70);           // true
```

---

## 7. Sorting Arrays (ES2023 `toSorted`)

> [!WARNING]
> Default `.sort()` converts items to strings! `[10, 2, 5].sort()` yields `[10, 2, 5]` because `"10"` comes before `"2"`. Always supply a comparator.

```javascript
const items = [
  { name: "Orange", price: 1.5 },
  { name: "Apple", price: 0.9 },
  { name: "Banana", price: 1.2 }
];

// Numerical Sort (Ascending) - Non-mutating
const byPriceAsc = items.toSorted((a, b) => a.price - b.price);

// String Sort with localeCompare (Handles accents and casing correctly)
const byName = items.toSorted((a, b) => a.name.localeCompare(b.name));

// Multi-field Sort: primary by role, secondary by score descending
const employees = [
  { role: "Engineer", score: 80 },
  { role: "Design", score: 90 },
  { role: "Engineer", score: 95 }
];

const multiSorted = employees.toSorted((a, b) => {
  const roleComparison = a.role.localeCompare(b.role);
  if (roleComparison !== 0) return roleComparison;
  return b.score - a.score; // Higher score first
});
```

---

## 8. Aggregating & Grouping

### `.reduce()` Patterns

```javascript
const transactions = [100, -20, 50, -10, 200];

// 1. Total balance
const total = transactions.reduce((acc, curr) => acc + curr, 0); // 320

// 2. Min and Max in a single pass
const { min, max } = transactions.reduce(
  (acc, curr) => ({
    min: Math.min(acc.min, curr),
    max: Math.max(acc.max, curr)
  }),
  { min: Infinity, max: -Infinity }
);

// 3. Frequency count / Tally
const votes = ["yes", "no", "yes", "yes", "abstain"];
const tally = votes.reduce((acc, vote) => {
  acc[vote] = (acc[vote] || 0) + 1;
  return acc;
}, {});
// { yes: 3, no: 1, abstain: 1 }
```

### Native Grouping: `Object.groupBy()` (ES2024)

```javascript
const employeesList = [
  { name: "Carlos", dept: "Engineering" },
  { name: "Dana", dept: "Sales" },
  { name: "Eric", dept: "Engineering" }
];

const groupedByDept = Object.groupBy(employeesList, (emp) => emp.dept);
/*
{
  Engineering: [
    { name: "Carlos", dept: "Engineering" },
    { name: "Eric", dept: "Engineering" }
  ],
  Sales: [
    { name: "Dana", dept: "Sales" }
  ]
}
*/
```

---

## 9. Utility Manipulation Functions

### 1. Chunking an Array (Batching)

```javascript
function chunk(array, size) {
  const chunks = [];
  for (let i = 0; i < array.length; i += size) {
    chunks.push(array.slice(i, i + size));
  }
  return chunks;
}

console.log(chunk([1, 2, 3, 4, 5, 6, 7], 3));
// [[1, 2, 3], [4, 5, 6], [7]]
```

### 2. Array Set Operations (Intersection, Difference, Union)

```javascript
const setA = [1, 2, 3, 4];
const setB = [3, 4, 5, 6];

// Union (all unique items)
const union = [...new Set([...setA, ...setB])]; // [1, 2, 3, 4, 5, 6]

// Intersection (items present in both)
const intersection = setA.filter((item) => setB.includes(item)); // [3, 4]

// Difference (items in A but not in B)
const difference = setA.filter((item) => !setB.includes(item)); // [1, 2]
```

### 3. Shuffling an Array (Fisher-Yates Algorithm)

```javascript
function shuffle(array) {
  const result = [...array]; // Work on a copy
  for (let i = result.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [result[i], result[j]] = [result[j], result[i]];
  }
  return result;
}

console.log(shuffle([1, 2, 3, 4, 5]));
```

---

## 10. Real-World Practical Examples

### Client-Side Pagination

```javascript
function paginate(items, pageNumber = 1, pageSize = 10) {
  const startIndex = (pageNumber - 1) * pageSize;
  const pageItems = items.slice(startIndex, startIndex + pageSize);
  const totalPages = Math.ceil(items.length / pageSize);

  return {
    currentPage: pageNumber,
    totalPages,
    totalItems: items.length,
    hasNextPage: pageNumber < totalPages,
    hasPrevPage: pageNumber > 1,
    data: pageItems
  };
}

const list = Array.from({ length: 25 }, (_, i) => `Item #${i + 1}`);
console.log(paginate(list, 2, 10));
```

### Shopping Cart Total Calculation with Discounts

```javascript
const cart = [
  { sku: "A1", name: "Keyboard", price: 75, quantity: 1, discount: 0.1 },
  { sku: "B2", name: "Cable", price: 15, quantity: 3, discount: 0 },
  { sku: "C3", name: "Monitor", price: 250, quantity: 2, discount: 0.15 }
];

const summary = cart.reduce(
  (acc, item) => {
    const originalSubtotal = item.price * item.quantity;
    const discountAmount = originalSubtotal * item.discount;
    const finalItemTotal = originalSubtotal - discountAmount;

    acc.itemCount += item.quantity;
    acc.grossTotal += originalSubtotal;
    acc.totalDiscount += discountAmount;
    acc.netTotal += finalItemTotal;

    return acc;
  },
  { itemCount: 0, grossTotal: 0, totalDiscount: 0, netTotal: 0 }
);

console.log(summary);
```

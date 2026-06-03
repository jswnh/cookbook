# 1. Creating Arrays

## Traditional Syntax

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];
```

---

## Array Constructor Syntax

```typescript
const numbers: Array<number> = [10, 20, 30, 40, 50];
```

---

## Empty Array

```typescript
const numbers: number[] = [];
```

---

## Array with Fixed Size (Pre-filled)

```typescript
const numbers: number[] = new Array(5).fill(0);
```

Default values:

```text
0
0
0
0
0
```

---

# 2. Accessing Elements

Arrays use zero-based indexing.

```typescript
const fruits: string[] = ["Apple", "Banana", "Orange"];

console.log(fruits[0]);
console.log(fruits[1]);
```

Output:

```text
Apple
Banana
```

---

# 3. Modifying Elements

```typescript
const fruits: string[] = ["Apple", "Banana", "Orange"];

fruits[1] = "Mango";

console.log(fruits[1]);
```

Output:

```text
Mango
```

---

# 4. Array Length

```typescript
const numbers: number[] = [10, 20, 30, 40];

console.log(numbers.length);
```

Output:

```text
4
```

---

# 5. Iterating with for

Best when indexes are needed.

```typescript
const numbers: number[] = [10, 20, 30, 40];

for (let i = 0; i < numbers.length; i++) {
    console.log(`Index: ${i}, Value: ${numbers[i]}`);
}
```

---

# 6. Iterating with for-of / forEach

Preferred when indexes are not needed.

```typescript
const numbers: number[] = [10, 20, 30, 40];

// using for-of
for (const number of numbers) {
    console.log(number);
}

// using forEach
numbers.forEach(number => console.log(number));
```

---

# 7. Multi-Dimensional Arrays

Useful for matrices.

```typescript
const matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6]
];

console.log(matrix[0][0]);
console.log(matrix[1][2]);
```

Output:

```text
1
6
```

---

## Iterating a Multi-Dimensional Array

```typescript
const matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6]
];

for (let row = 0; row < matrix.length; row++) {
    let rowString = "";
    for (let column = 0; column < matrix[row].length; column++) {
        rowString += `${matrix[row][column]} `;
    }
    console.log(rowString);
}
```

---

# 8. Jagged Arrays

An array of arrays of varying sizes.

```typescript
const matrix: number[][] = [
    [1, 2, 3],
    [4, 5],
    [6, 7, 8, 9]
];
```

Accessing values:

```typescript
console.log(matrix[0][1]);
console.log(matrix[2][3]);
```

Output:

```text
2
9
```

---

# 9. Searching Arrays

## Array.prototype.some

```typescript
const numbers: number[] = [10, 20, 30, 40];

const exists: boolean = numbers.some(number => number === 30);

console.log(exists);
```

Output:

```text
true
```

---

## Array.prototype.indexOf / includes

```typescript
const numbers: number[] = [10, 20, 30, 40];

const index: number = numbers.indexOf(30);
const included: boolean = numbers.includes(30);

console.log(index);
console.log(included);
```

Output:

```text
2
true
```

---

# 10. Sorting Arrays

Note: `sort()` in JS/TS sorts elements alphabetically (as strings) by default and mutates the array. Provide a comparator function for numbers. To avoid mutation, use `toSorted()` (ES2023+).

```typescript
const numbers: number[] = [50, 20, 10, 40, 30];

// In-place sorting
numbers.sort((a, b) => a - b);

numbers.forEach(number => console.log(number));
```

Output:

```text
10
20
30
40
50
```

---

# 11. Reversing Arrays

Note: `reverse()` mutates the array. Use `toReversed()` (ES2023+) for a non-mutating version.

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];

numbers.reverse();

numbers.forEach(number => console.log(number));
```

Output:

```text
50
40
30
20
10
```

---

# 12. Copying Arrays

```typescript
const source: number[] = [10, 20, 30];

// Using spread operator
const destination: number[] = [...source];
```

Or using `slice()`:

```typescript
const destination: number[] = source.slice();
```

---

# 13. Array Slicing

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];

const result: number[] = numbers.slice(1, 4);

result.forEach(number => console.log(number));
```

Output:

```text
20
30
40
```

---

# 14. Access from End (Index Operator)

Using `at()` method (ES2022+).

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];

console.log(numbers.at(-1));
console.log(numbers.at(-2));
```

Output:

```text
50
40
```

---

# 15. Array Destructuring

```typescript
const names: string[] = ["John", "Jane", "Bob"];

const [first, second] = names;

console.log(first);
console.log(second);
```

---

# 16. Combining Arrays

Using the spread operator.

```typescript
const first: number[] = [1, 2, 3];
const second: number[] = [4, 5, 6];

const combined: number[] = [...first, ...second];

combined.forEach(number => console.log(number));
```

Output:

```text
1
2
3
4
5
6
```

---

# 17. Functional Array Methods (LINQ Equivalents)

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];

// Filter (Where)
const evenNumbers = numbers.filter(number => number % 2 === 0);

// Map (Select)
const squaredNumbers = numbers.map(number => number * number);

// Reduce (Aggregate)
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
```

---

# 18. Real-World Example

Processing student grades.

```typescript
const grades: number[] = [95, 82, 76, 91, 88];

const highest = Math.max(...grades);
const lowest = Math.min(...grades);
const average = grades.reduce((sum, val) => sum + val, 0) / grades.length;

console.log(`Highest: ${highest}`);
console.log(`Lowest: ${lowest}`);
console.log(`Average: ${average.toFixed(2)}`);
```

Output:

```text
Highest: 95
Lowest: 76
Average: 86.40
```

---

# 19. ReadonlyArray & TypedArray (Advanced)

Exposes read-only or low-allocation binary data representations.

```typescript
// ReadonlyArray
const immutableNumbers: ReadonlyArray<number> = [1, 2, 3];
// immutableNumbers[0] = 99; // Compilation Error

// TypedArray (e.g. Int32Array) for high-performance memory buffers
const buffer = new Int32Array([10, 20, 30]);
buffer[0] = 999;
console.log(buffer[0]);
```

Output:

```text
999
```

---

# Summary

## Creating Arrays

```typescript
const numbers: number[] = [1, 2, 3];
```

## Accessing Elements

```typescript
numbers[0];
numbers.at(-1); // Access from end
```

## Iteration

- for loop
- for-of loop
- `forEach()`

## Array Operations

- `sort()` / `toSorted()`
- `reverse()` / `toReversed()`
- `slice()`
- `some()` / `includes()` / `indexOf()`

## Modern Features

- Spread operator (`...`)
- Array destructuring
- `ReadonlyArray`
- ES2022+ `at()` index operator
- TypedArrays (`Int32Array`, etc.)

Arrays in TypeScript are dynamic, untyped JavaScript arrays underneath, but compile-time type-safety guarantees homogeneity and access validation.

# 1. Creating Arrays

## Traditional Syntax

```php
$numbers = array(10, 20, 30, 40, 50);
```

---

## Short Array Syntax (Modern PHP)

```php
$numbers = [10, 20, 30, 40, 50];
```

---

## Empty Array

```php
$numbers = [];
```

---

## Array with Fixed Size (Pre-filled)

```php
$numbers = array_fill(0, 5, 0);
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

PHP arrays use zero-based indexing by default.

```php
$fruits = ["Apple", "Banana", "Orange"];

echo $fruits[0] . "\n";
echo $fruits[1] . "\n";
```

Output:

```text
Apple
Banana
```

---

# 3. Modifying Elements

```php
$fruits = ["Apple", "Banana", "Orange"];

$fruits[1] = "Mango";

echo $fruits[1] . "\n";
```

Output:

```text
Mango
```

---

# 4. Array Length

```php
$numbers = [10, 20, 30, 40];

echo count($numbers) . "\n";
```

Output:

```text
4
```

---

# 5. Iterating with for

Best when indexes are needed.

```php
$numbers = [10, 20, 30, 40];
$length = count($numbers);

for ($i = 0; $i < $length; $i++) {
    echo "Index: {$i}, Value: {$numbers[$i]}\n";
}
```

---

# 6. Iterating with foreach

Preferred when indexes are not needed.

```php
$numbers = [10, 20, 30, 40];

foreach ($numbers as $number) {
    echo $number . "\n";
}
```

If keys/indexes are also needed:

```php
foreach ($numbers as $index => $number) {
    echo "Index: {$index}, Value: {$number}\n";
}
```

---

# 7. Multi-Dimensional Arrays

Useful for matrices.

```php
$matrix = [
    [1, 2, 3],
    [4, 5, 6]
];

echo $matrix[0][0] . "\n";
echo $matrix[1][2] . "\n";
```

Output:

```text
1
6
```

---

## Iterating a Multi-Dimensional Array

```php
$matrix = [
    [1, 2, 3],
    [4, 5, 6]
];

foreach ($matrix as $row) {
    foreach ($row as $value) {
        echo $value . " ";
    }
    echo "\n";
}
```

---

# 8. Jagged Arrays

An array of arrays of varying sizes.

```php
$matrix = [
    [1, 2, 3],
    [4, 5],
    [6, 7, 8, 9]
];
```

Accessing values:

```php
echo $matrix[0][1] . "\n";
echo $matrix[2][3] . "\n";
```

Output:

```text
2
9
```

---

# 9. Searching Arrays

## `in_array()`

Checks if a value exists in an array.

```php
$numbers = [10, 20, 30, 40];

$exists = in_array(30, $numbers, true); // Strict check

var_dump($exists);
```

Output:

```text
bool(true)
```

---

## `array_search()`

Searches the array for a given value and returns the corresponding key if successful.

```php
$numbers = [10, 20, 30, 40];

$index = array_search(30, $numbers, true);

echo $index . "\n";
```

Output:

```text
2
```

---

# 10. Sorting Arrays

PHP array sort functions sort the array in place (mutating).

```php
$numbers = [50, 20, 10, 40, 30];

sort($numbers); // Sorts ascending

foreach ($numbers as $number) {
    echo $number . "\n";
}
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

`array_reverse()` returns a new reversed array (non-mutating).

```php
$numbers = [10, 20, 30, 40, 50];

$reversed = array_reverse($numbers);

foreach ($reversed as $number) {
    echo $number . "\n";
}
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

In PHP, arrays are copied by value (using copy-on-write optimization). Assigning an array to another variable duplicates it.

```php
$source = [10, 20, 30];

$destination = $source; // Copied by value
$destination[0] = 999;

// $source remains [10, 20, 30]
```

---

# 13. Array Slicing

`array_slice()` extracts a portion of the array.

```php
$numbers = [10, 20, 30, 40, 50];

$result = array_slice($numbers, 1, 3);

foreach ($result as $number) {
    echo $number . "\n";
}
```

Output:

```text
20
30
40
```

---

# 14. Negative Index Operator

PHP 8.1+ supports negative offsets in string indexers, but for numeric arrays, standard negative index access like `$arr[-1]` is **not** supported directly unless keys are explicitly negative (due to array mapping). Instead, use `array_slice()` or offset lookup:

```php
$numbers = [10, 20, 30, 40, 50];

echo $numbers[count($numbers) - 1] . "\n";
echo array_slice($numbers, -1)[0] . "\n";
```

Output:

```text
50
50
```

---

# 15. Array Destructuring

```php
$names = ["John", "Jane", "Bob"];

[$first, $second] = $names;

echo $first . "\n";
echo $second . "\n";
```

---

# 16. Combining Arrays

Using the spread operator (`...`) in PHP 7.4+ (or PHP 8.1+ for string-keyed arrays).

```php
$first = [1, 2, 3];
$second = [4, 5, 6];

$combined = [...$first, ...$second];

foreach ($combined as $number) {
    echo $number . "\n";
}
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

```php
$numbers = [10, 20, 30, 40, 50];

// Filter
$evenNumbers = array_filter($numbers, fn($n) => $n % 2 === 0);

// Map
$squaredNumbers = array_map(fn($n) => $n * $n, $numbers);

// Reduce
$sum = array_reduce($numbers, fn($carry, $item) => $carry + $item, 0);
```

---

# 18. Real-World Example

Processing student grades.

```php
$grades = [95, 82, 76, 91, 88];

$highest = max($grades);
$lowest = min($grades);
$average = array_sum($grades) / count($grades);

echo "Highest: {$highest}\n";
echo "Lowest: {$lowest}\n";
printf("Average: %.2f\n", $average);
```

Output:

```text
Highest: 95
Lowest: 76
Average: 86.40
```

---

# 19. SplFixedArray (Advanced)

For memory efficiency and higher performance when the array size is fixed.

```php
$fixed = new SplFixedArray(5);
$fixed[0] = 10;
$fixed[1] = 20;

echo $fixed->getSize() . "\n"; // 5
```

---

# Summary

## Creating Arrays

```php
$numbers = [1, 2, 3];
```

## Accessing Elements

```php
$numbers[0]
$numbers[count($numbers) - 1]
```

## Iteration

- for
- foreach

## Array Operations

- `sort()`
- `array_reverse()`
- `array_slice()`
- `in_array()`
- `array_search()`

## Modern Features

- Short array syntax (`[]`)
- Spread operator (`...$array`)
- Array destructuring (`[$first, $second]`)
- Arrow functions (`fn($x) => $x * 2`)
- `SplFixedArray` for high performance

In PHP, arrays are unified ordered maps serving as lists, hash tables, stacks, and queues. Standard arrays are dynamic and copied by value.

# 1. Creating Arrays

## Traditional Syntax

```csharp
int[] numbers = { 10, 20, 30, 40, 50 };
```

---

## Collection Expression (Modern C#)

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];
```

---

## Empty Array

```csharp
int[] numbers = [];
```

---

## Array with Fixed Size

```csharp
int[] numbers = new int[5];
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

```csharp
string[] fruits =
[
    "Apple",
    "Banana",
    "Orange"
];

Console.WriteLine(fruits[0]);
Console.WriteLine(fruits[1]);
```

Output:

```text
Apple
Banana
```

---

# 3. Modifying Elements

```csharp
string[] fruits =
[
    "Apple",
    "Banana",
    "Orange"
];

fruits[1] = "Mango";

Console.WriteLine(fruits[1]);
```

Output:

```text
Mango
```

---

# 4. Array Length

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40
];

Console.WriteLine(numbers.Length);
```

Output:

```text
4
```

---

# 5. Iterating with for

Best when indexes are needed.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40
];

for (int i = 0; i < numbers.Length; i++)
{
    Console.WriteLine(
        $"Index: {i}, Value: {numbers[i]}");
}
```

---

# 6. Iterating with foreach

Preferred when indexes are not needed.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40
];

foreach (var number in numbers)
{
    Console.WriteLine(number);
}
```

---

# 7. Multi-Dimensional Arrays

Useful for matrices.

```csharp
int[,] matrix =
{
    { 1, 2, 3 },
    { 4, 5, 6 }
};

Console.WriteLine(matrix[0, 0]);
Console.WriteLine(matrix[1, 2]);
```

Output:

```text
1
6
```

---

## Iterating a Multi-Dimensional Array

```csharp
int[,] matrix =
{
    { 1, 2, 3 },
    { 4, 5, 6 }
};

for (int row = 0; row < matrix.GetLength(0); row++)
{
    for (int column = 0; column < matrix.GetLength(1); column++)
    {
        Console.Write(
            $"{matrix[row, column]} ");
    }

    Console.WriteLine();
}
```

---

# 8. Jagged Arrays

An array of arrays.

```csharp
int[][] matrix =
[
    [1, 2, 3],
    [4, 5],
    [6, 7, 8, 9]
];
```

Accessing values:

```csharp
Console.WriteLine(matrix[0][1]);
Console.WriteLine(matrix[2][3]);
```

Output:

```text
2
9
```

---

# 9. Searching Arrays

## Array.Exists

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40
];

bool exists =
    Array.Exists(numbers,
        number => number == 30);

Console.WriteLine(exists);
```

Output:

```text
True
```

---

## Array.IndexOf

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40
];

int index =
    Array.IndexOf(numbers, 30);

Console.WriteLine(index);
```

Output:

```text
2
```

---

# 10. Sorting Arrays

```csharp
int[] numbers =
[
    50,
    20,
    10,
    40,
    30
];

Array.Sort(numbers);

foreach (var number in numbers)
{
    Console.WriteLine(number);
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

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

Array.Reverse(numbers);

foreach (var number in numbers)
{
    Console.WriteLine(number);
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

```csharp
int[] source =
[
    10,
    20,
    30
];

int[] destination =
    new int[source.Length];

Array.Copy(
    source,
    destination,
    source.Length);
```

---

# 13. Array Slicing

Using ranges.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

int[] result =
    numbers[1..4];

foreach (var number in result)
{
    Console.WriteLine(number);
}
```

Output:

```text
20
30
40
```

---

# 14. Index Operator

Access elements from the end.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

Console.WriteLine(numbers[^1]);
Console.WriteLine(numbers[^2]);
```

Output:

```text
50
40
```

---

# 15. Array Destructuring

```csharp
string[] names =
[
    "John",
    "Jane",
    "Bob"
];

var first = names[0];
var second = names[1];

Console.WriteLine(first);
Console.WriteLine(second);
```

---

# 16. Combining Arrays

Using collection expressions.

```csharp
int[] first =
[
    1,
    2,
    3
];

int[] second =
[
    4,
    5,
    6
];

int[] combined =
[
    ..first,
    ..second
];

foreach (var number in combined)
{
    Console.WriteLine(number);
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

# 17. LINQ with Arrays

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

var evenNumbers =
    numbers.Where(number => number % 2 == 0);

foreach (var number in evenNumbers)
{
    Console.WriteLine(number);
}
```

---

# 18. Real-World Example

Processing student grades.

```csharp
int[] grades =
[
    95,
    82,
    76,
    91,
    88
];

int highest = grades.Max();
int lowest = grades.Min();
double average = grades.Average();

Console.WriteLine($"Highest: {highest}");
Console.WriteLine($"Lowest: {lowest}");
Console.WriteLine($"Average: {average:F2}");
```

Output:

```text
Highest: 95
Lowest: 76
Average: 86.40
```

---

# 19. Span<T> (Advanced)

High-performance view over contiguous memory.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

Span<int> span = numbers;

span[0] = 999;

Console.WriteLine(numbers[0]);
```

Output:

```text
999
```

Useful for performance-critical applications because it avoids allocations.

---

# Summary

## Creating Arrays

```csharp
int[] numbers = [1, 2, 3];
```

## Accessing Elements

```csharp
numbers[0]
numbers[^1]
```

## Iteration

- for
- foreach

## Array Operations

- Sort
- Reverse
- Copy
- Exists
- IndexOf

## Modern Features

- Collection expressions (`[]`)
- Spread operator (`..`)
- Range operator (`..`)
- Index operator (`^`)
- Span<T>

Arrays are fixed-size, memory-efficient collections and serve as the foundation for many higher-level data structures in C#.

# 1. Creating Arrays

Arrays in Java are fixed-size, contiguous blocks of memory allocated on the heap that store elements of the same type.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## Array Initializer (Direct Values)

```java
int[] numbers = { 10, 20, 30, 40, 50 };
```

---

## Explicit Instantiation with Values

```java
String[] languages = new String[]{ "Java", "TypeScript", "Python", "C#" };
```

---

## Fixed-Size Array (Default Initialized)

Allocates space for a specified number of elements. Each element receives the default value for its type.

```java
int[] buffer = new int[5];
```

Default values in memory:

```text
buffer[0] -> 0
buffer[1] -> 0
buffer[2] -> 0
buffer[3] -> 0
buffer[4] -> 0
```

| Type | Default Element Value |
| :--- | :--- |
| `byte`, `short`, `int`, `long` | `0` / `0L` |
| `float`, `double` | `0.0f` / `0.0d` |
| `boolean` | `false` |
| `char` | `'\u0000'` |
| Reference Types (`String`, `Object`) | `null` |

---

# 2. Accessing and Modifying Elements

Java arrays use zero-based indexing.

```java
String[] fruits = { "Apple", "Banana", "Cherry" };

// Accessing elements
String first = fruits[0]; // Apple
String last = fruits[fruits.length - 1]; // Cherry

// Modifying elements
fruits[1] = "Blueberry";

System.out.println(fruits[1]); // Blueberry
```

> [!WARNING]
> Accessing an index `< 0` or `>= array.length` throws an `ArrayIndexOutOfBoundsException`.

---

# 3. Array Length

The length of an array is a constant public field, not a method call.

```java
int[] items = { 100, 200, 300 };

int totalElements = items.length; // 3
```

---

# 4. Iterating Arrays

## Classic for Loop

Ideal when you require the current element index.

```java
int[] numbers = { 10, 20, 30, 40, 50 };

for (int i = 0; i < numbers.length; i++) {
    System.out.printf("Index %d: %d%n", i, numbers[i]);
}
```

---

## Enhanced for-each Loop

The cleanest and most idiomatic way to traverse elements sequentially when indices are not required.

```java
String[] frameworks = { "Spring Boot", "Micronaut", "Quarkus" };

for (String framework : frameworks) {
    System.out.println(framework);
}
```

---

## Java Stream Traversal

```java
import java.util.Arrays;

int[] numbers = { 1, 2, 3, 4, 5 };

Arrays.stream(numbers)
      .forEach(System.out::println);
```

---

# 5. Multidimensional and Jagged Arrays

## Multidimensional Rectangular Arrays

```java
int[][] matrix = {
    { 1, 2, 3 },
    { 4, 5, 6 }
};

System.out.println(matrix[0][1]); // 2 (row 0, col 1)
```

---

## Jagged Arrays (Non-Uniform Rows)

In Java, multidimensional arrays are truly arrays of arrays, meaning sub-arrays can vary in length.

```java
int[][] jagged = new int[3][];
jagged[0] = new int[]{ 1 };
jagged[1] = new int[]{ 2, 3 };
jagged[2] = new int[]{ 4, 5, 6, 7 };

for (int r = 0; r < jagged.length; r++) {
    for (int c = 0; c < jagged[r].length; c++) {
        System.out.print(jagged[r][c] + " ");
    }
    System.out.println();
}
```

---

# 6. Array Utility Methods (`java.util.Arrays`)

The `java.util.Arrays` class provides essential static helper methods for operating on arrays.

## Printing Arrays (`toString` and `deepToString`)

```java
import java.util.Arrays;

int[] numbers = { 5, 2, 8, 1 };
System.out.println(Arrays.toString(numbers)); 
// Output: [5, 2, 8, 1]

int[][] matrix = { { 1, 2 }, { 3, 4 } };
System.out.println(Arrays.deepToString(matrix)); 
// Output: [[1, 2], [3, 4]]
```

---

## Sorting (`sort` and `parallelSort`)

```java
int[] numbers = { 50, 20, 40, 10, 30 };

// Standard Dual-Pivot Quicksort
Arrays.sort(numbers);
System.out.println(Arrays.toString(numbers)); // [10, 20, 30, 40, 50]

// Parallel sorting for large arrays using ForkJoinPool
long[] largeData = new long[1_000_000];
Arrays.parallelSort(largeData);
```

---

## Binary Search

Array must be sorted prior to calling `binarySearch`.

```java
int[] sorted = { 10, 20, 30, 40, 50 };

int index = Arrays.binarySearch(sorted, 30);
System.out.println("Index of 30: " + index); // 2
```

---

## Copying and Slicing (`copyOf` and `copyOfRange`)

```java
int[] original = { 1, 2, 3, 4, 5 };

// Resize / Copy
int[] expanded = Arrays.copyOf(original, 7); // [1, 2, 3, 4, 5, 0, 0]

// Slice from index 1 (inclusive) to 4 (exclusive)
int[] slice = Arrays.copyOfRange(original, 1, 4); // [2, 3, 4]
```

---

## Filling Arrays (`fill`)

```java
int[] flags = new int[5];
Arrays.fill(flags, -1);

System.out.println(Arrays.toString(flags)); // [-1, -1, -1, -1, -1]
```

---

## Equality Comparison (`equals` and `deepEquals`)

```java
int[] a = { 1, 2, 3 };
int[] b = { 1, 2, 3 };

System.out.println(a == b);               // false (different object references)
System.out.println(Arrays.equals(a, b));  // true (element-by-element equality)
```

---

# 7. Converting Arrays to and from Collections

## Array to List

```java
import java.util.Arrays;
import java.util.List;

String[] array = { "Alpha", "Beta", "Gamma" };

// Immutable unmodifiable List
List<String> immutableList = List.of(array);

// Fixed-size backed List
List<String> backedList = Arrays.asList(array);
```

---

## List to Array

Modern Java uses the constructor reference `T[]::new` with `toArray()`.

```java
import java.util.List;

List<String> items = List.of("One", "Two", "Three");

// Modern type-safe conversion
String[] array = items.toArray(String[]::new);
```

---

# 8. Real-World Example

Calculating descriptive statistics on sensor readings.

```java
import java.util.Arrays;

public class SensorMetrics {
    public static void main(String[] args) {
        double[] temperatures = { 21.5, 23.8, 19.4, 25.1, 22.0, 24.3, 18.9 };

        double min = Arrays.stream(temperatures).min().orElse(Double.NaN);
        double max = Arrays.stream(temperatures).max().orElse(Double.NaN);
        double average = Arrays.stream(temperatures).average().orElse(Double.NaN);

        // Calculate sample standard deviation
        double variance = Arrays.stream(temperatures)
                .map(temp -> Math.pow(temp - average, 2))
                .average()
                .orElse(0.0);
        double stdDev = Math.sqrt(variance);

        System.out.printf("Total Readings : %d%n", temperatures.length);
        System.out.printf("Min Temp       : %.2f °C%n", min);
        System.out.printf("Max Temp       : %.2f °C%n", max);
        System.out.printf("Average Temp   : %.2f °C%n", average);
        System.out.printf("Std Deviation  : %.2f%n", stdDev);
    }
}
```

Output:

```text
Total Readings : 7
Min Temp       : 18.90 °C
Max Temp       : 25.10 °C
Average Temp   : 22.14 °C
Std Deviation  : 2.14
```

---

# Summary

| Operation | Syntax / Method |
| :--- | :--- |
| **Creation** | `int[] arr = { 1, 2, 3 };` or `new int[size];` |
| **Element Access** | `arr[index]` |
| **Length** | `arr.length` |
| **Print Elements** | `Arrays.toString(arr)` / `Arrays.deepToString(matrix)` |
| **Sort** | `Arrays.sort(arr)` / `Arrays.parallelSort(arr)` |
| **Binary Search** | `Arrays.binarySearch(sortedArr, target)` |
| **Copy / Subarray**| `Arrays.copyOf(arr, newLength)` / `Arrays.copyOfRange(arr, from, to)` |
| **Fill Elements** | `Arrays.fill(arr, val)` |
| **Stream Processing**| `Arrays.stream(arr)` |
| **To Collection** | `List.of(arr)` or `Arrays.asList(arr)` |
| **From Collection**| `list.toArray(String[]::new)` |

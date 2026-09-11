# 1. Creating Arrays

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

In VB.NET, arrays are zero-indexed reference types backed by `System.Array`.

## Array Literal

```vb
Dim numbers() As Integer = {10, 20, 30, 40, 50}
```

---

## Fixed-Size Array Declaration

> [!IMPORTANT]
> In VB.NET, when declaring an array with a size like `Dim arr(4) As Integer`, the number inside the parentheses represents the **highest index (upper bound)**, NOT the total count. Therefore, `Dim arr(4)` has **5 elements** (indices 0 through 4).

```vb
' Declares an array of 5 elements (indices 0 to 4)
Dim buffer(4) As Integer
```

Default values in memory:

```text
buffer(0) -> 0
buffer(1) -> 0
buffer(2) -> 0
buffer(3) -> 0
buffer(4) -> 0
```

---

## Dynamic Resizing (`ReDim` and `ReDim Preserve`)

- `ReDim`: Reallocates the array; clears existing values to default.
- `ReDim Preserve`: Reallocates while keeping existing elements intact.

```vb
Dim items() As String = {"Alpha", "Beta"}

' Expands array to 4 elements (indices 0 to 3), preserving existing data
ReDim Preserve items(3)
items(2) = "Gamma"
items(3) = "Delta"
```

---

# 2. Accessing and Modifying Elements

VB.NET uses parentheses `()` rather than square brackets `[]` for indexing.

```vb
Dim fruits() As String = {"Apple", "Banana", "Cherry"}

' Accessing
Dim firstFruit As String = fruits(0) ' Apple
Dim lastFruit As String = fruits(fruits.Length - 1) ' Cherry

' Modifying
fruits(1) = "Blueberry"

Console.WriteLine(fruits(1)) ' Blueberry
```

---

# 3. Array Length and Bounds

```vb
Dim data() As Integer = {100, 200, 300, 400}

Dim totalCount As Integer = data.Length ' 4
Dim maxIndex As Integer = data.GetUpperBound(0) ' 3
Dim minIndex As Integer = data.GetLowerBound(0) ' 0
```

---

# 4. Iterating Arrays

## For Each Loop

Preferred when index tracking is not needed.

```vb
Dim frameworks() As String = {".NET", "ASP.NET Core", "Entity Framework"}

For Each item In frameworks
    Console.WriteLine(item)
Next
```

---

## For Loop with Bounds

```vb
Dim scores() As Integer = {95, 88, 72, 91}

For i As Integer = 0 To scores.GetUpperBound(0)
    Console.WriteLine($"Index {i}: {scores(i)}")
Next
```

---

# 5. Multidimensional and Jagged Arrays

## Multidimensional (Rectangular 2D)

All rows have the exact same column count.

```vb
Dim matrix(,) As Integer = {
    {1, 2, 3},
    {4, 5, 6}
}

' Access element at row 0, column 1
Dim val As Integer = matrix(0, 1) ' 2
```

---

## Jagged Arrays (Array of Arrays)

Sub-arrays can have varying lengths.

```vb
Dim jagged(2)() As Integer
jagged(0) = New Integer() {1}
jagged(1) = New Integer() {2, 3}
jagged(2) = New Integer() {4, 5, 6, 7}

For r As Integer = 0 To jagged.GetUpperBound(0)
    For c As Integer = 0 To jagged(r).GetUpperBound(0)
        Console.Write($"{jagged(r)(c)} ")
    Next
    Console.WriteLine()
Next
```

---

# 6. Array Utility Methods (`System.Array`)

```vb
Imports System

Dim numbers() As Integer = {50, 20, 40, 10, 30}

' Sorting
Array.Sort(numbers) ' {10, 20, 30, 40, 50}

' Binary Search (array must be sorted)
Dim foundIdx As Integer = Array.BinarySearch(numbers, 30) ' 2

' Reversing
Array.Reverse(numbers) ' {50, 40, 30, 20, 10}

' Finding Index
Dim idxOf40 As Integer = Array.IndexOf(numbers, 40) ' 1

' Clearing range (sets elements to default)
Array.Clear(numbers, 0, 2) ' Clears 2 elements starting at index 0
```

---

# 7. Converting Arrays to and from Collections

```vb
Imports System.Collections.Generic
Imports System.Linq

Dim languages() As String = {"VB.NET", "C#", "F#"}

' Array to List
Dim langList As List(Of String) = languages.ToList()

' List to Array
Dim backToArray() As String = langList.ToArray()
```

---

# 8. LINQ with Arrays

VB.NET provides rich comprehension syntax alongside standard LINQ method chaining.

```vb
Imports System
Imports System.Linq

Dim scores() As Integer = {75, 92, 84, 60, 95, 88}

' Comprehension syntax
Dim honorRoll = From s In scores
                Where s >= 85
                Order By s Descending
                Select s

' Method syntax
Dim averageScore As Double = scores.Average()
Dim topScore As Integer = scores.Max()

Console.WriteLine($"Average: {averageScore:F2}")
Console.WriteLine($"Top Score: {topScore}")
```

---

# 9. Real-World Example

Calculating descriptive statistics for sensor telemetry data.

```vb
Imports System
Imports System.Linq

Public Module SensorAnalytics
    Public Sub Main()
        Dim readings() As Double = {21.5, 23.8, 19.4, 25.1, 22.0, 24.3, 18.9}

        Dim minTemp As Double = readings.Min()
        Dim maxTemp As Double = readings.Max()
        Dim avgTemp As Double = readings.Average()

        Dim variance As Double = readings.Select(Function(t) Math.Pow(t - avgTemp, 2)).Average()
        Dim stdDev As Double = Math.Sqrt(variance)

        Console.WriteLine($"Total Readings : {readings.Length}")
        Console.WriteLine($"Min Temp       : {minTemp:F2} °C")
        Console.WriteLine($"Max Temp       : {maxTemp:F2} °C")
        Console.WriteLine($"Average Temp   : {avgTemp:F2} °C")
        Console.WriteLine($"Std Deviation  : {stdDev:F2}")
    End Sub
End Module
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

| Operation | VB.NET Syntax |
| :--- | :--- |
| **Declaration (Literal)** | `Dim arr() As Integer = {1, 2, 3}` |
| **Declaration (Sized)** | `Dim arr(n) As Integer` *(Size is n + 1 elements)* |
| **Element Access** | `arr(index)` *(parentheses syntax)* |
| **Length / Bounds** | `arr.Length`, `arr.GetUpperBound(0)` |
| **Resizing** | `ReDim Preserve arr(newUpperBound)` |
| **Sort / Search** | `Array.Sort(arr)`, `Array.BinarySearch(arr, val)` |
| **Multidimensional**| `Dim matrix(rows, cols) As Integer` |
| **To/From List** | `arr.ToList()`, `list.ToArray()` |

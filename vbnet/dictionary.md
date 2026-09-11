# 1. Creating a Dictionary

In VB.NET, associative key-value collections are represented by `System.Collections.Generic.Dictionary(Of TKey, TValue)`.

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## Using Collection Initializer

```vb
Imports System.Collections.Generic

Dim users As New Dictionary(Of Integer, String) From {
    {1, "John"},
    {2, "Jane"},
    {3, "Bob"}
}
```

---

## Empty Instantiation

```vb
Dim capitals As New Dictionary(Of String, String)()
```

---

# 2. Adding Items

```vb
Dim inventory As New Dictionary(Of String, Integer)()

' 1. Using Add() - throws ArgumentException if key already exists
inventory.Add("Apples", 50)
inventory.Add("Oranges", 30)

' 2. Using parentheses indexer - adds if new, overwrites if existing
inventory("Bananas") = 20
inventory("Apples") = 55 ' Overwrites 50 with 55

' 3. Using TryAdd() - returns False without throwing if key already exists
Dim added As Boolean = inventory.TryAdd("Apples", 100) ' False
```

---

# 3. Accessing Values

## Direct Access (Indexer)

```vb
Dim users As New Dictionary(Of Integer, String) From {
    {1, "John"},
    {2, "Jane"}
}

Dim name As String = users(1) ' John
```

> [!WARNING]
> Accessing a key that does not exist via `users(99)` throws a `KeyNotFoundException`.

---

## Safe Access with `TryGetValue`

The recommended and fastest way to retrieve values when the presence of the key is uncertain.

```vb
Dim user As String = Nothing

If users.TryGetValue(1, user) Then
    Console.WriteLine($"Found: {user}")
Else
    Console.WriteLine("User not found")
End If
```

---

# 4. Updating Values

```vb
Dim scores As New Dictionary(Of String, Integer) From {
    {"Alice", 90}
}

' Update value directly
scores("Alice") = 95
```

---

# 5. Removing Items

```vb
Dim statusCodes As New Dictionary(Of Integer, String) From {
    {200, "OK"},
    {404, "Not Found"},
    {500, "Server Error"}
}

' Remove by key - returns True if key was found and removed
Dim removed As Boolean = statusCodes.Remove(500)

' Clear all items
statusCodes.Clear()
```

---

# 6. Checking Keys, Values, and Size

```vb
Dim prices As New Dictionary(Of String, Decimal) From {
    {"Laptop", 1299.99D},
    {"Mouse", 29.99D}
}

Dim hasLaptop As Boolean = prices.ContainsKey("Laptop")   ' True
Dim hasFreeItem As Boolean = prices.ContainsValue(0.0D)   ' False
Dim totalCount As Integer = prices.Count                 ' 2
```

---

# 7. Iterating Over Dictionaries

## Iterating Key-Value Pairs

```vb
Dim scores As New Dictionary(Of String, Integer) From {
    {"Alice", 95},
    {"Bob", 88}
}

For Each kvp In scores
    Console.WriteLine($"{kvp.Key} -> {kvp.Value}")
Next
```

---

## Iterating Keys or Values Only

```vb
' Keys only
For Each student In scores.Keys
    Console.WriteLine($"Student: {student}")
Next

' Values only
For Each score In scores.Values
    Console.WriteLine($"Score: {score}")
Next
```

---

# 8. Thread-Safe Dictionary (`ConcurrentDictionary`)

For multi-threaded environments, use `System.Collections.Concurrent.ConcurrentDictionary(Of TKey, TValue)`.

```vb
Imports System.Collections.Concurrent

Dim cache As New ConcurrentDictionary(Of String, Integer)()

' Atomically add or update
cache.AddOrUpdate("connections", 1, Function(key, oldVal) oldVal + 1)

' Atomically retrieve or add
Dim value As Integer = cache.GetOrAdd("sessions", Function(key) 100)
```

---

# 9. Real-World Example

Frequency analyzer and category classifier using LINQ and Dictionaries.

```vb
Imports System
Imports System.Collections.Generic
Imports System.Linq

Public Module DictionaryAnalytics
    Public Sub Main()
        Dim words() As String = {"apple", "banana", "apple", "cherry", "banana", "apple"}

        Dim frequency As New Dictionary(Of String, Integer)()

        For Each word In words
            If frequency.ContainsKey(word) Then
                frequency(word) += 1
            Else
                frequency(word) = 1
            End If
        Next

        ' LINQ sorting by frequency descending
        Dim sorted = From kvp In frequency
                     Order By kvp.Value Descending
                     Select kvp

        For Each item In sorted
            Console.WriteLine($"{item.Key}: {item.Value} time(s)")
        Next
    End Sub
End Module
```

Output:

```text
apple: 3 time(s)
banana: 2 time(s)
cherry: 1 time(s)
```

---

# Summary

| Method / Operator | Description |
| :--- | :--- |
| **`dict(key)`** | Get or set value for specified key |
| **`Add(key, value)`** | Add key-value pair (throws if duplicate) |
| **`TryAdd(key, value)`** | Attempt to add pair without throwing on duplicate |
| **`TryGetValue(key, outVal)`** | Safely retrieve value without throwing if missing |
| **`ContainsKey(key)`** | Check if key is present |
| **`ContainsValue(val)`**| Check if value is present |
| **`Remove(key)`** | Delete key-value pair |
| **`Count`** | Total number of key-value pairs |

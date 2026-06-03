# 1. Creating a Dictionary

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane",
    [3] = "Bob"
};
```

---

# 2. Adding Items

## Using Add

```csharp
Dictionary<int, string> users = new();

users.Add(1, "John");
users.Add(2, "Jane");
```

---

## Using Indexer

```csharp
Dictionary<int, string> users = new();

users[1] = "John";
users[2] = "Jane";
```

---

# 3. Accessing Values

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane"
};

Console.WriteLine(users[1]);
```

Output:

```text
John
```

---

# 4. Updating Values

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John"
};

users[1] = "Johnny";

Console.WriteLine(users[1]);
```

Output:

```text
Johnny
```

---

# 5. Dictionary Count

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane",
    [3] = "Bob"
};

Console.WriteLine(users.Count);
```

Output:

```text
3
```

---

# 6. Checking for Keys

## ContainsKey

```csharp
if (users.ContainsKey(1))
{
    Console.WriteLine("User exists");
}
```

---

## ContainsValue

```csharp
if (users.ContainsValue("John"))
{
    Console.WriteLine("Found");
}
```

---

# 7. Safe Retrieval with TryGetValue

Preferred over direct indexing when a key may not exist.

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane"
};

if (users.TryGetValue(1, out string? name))
{
    Console.WriteLine(name);
}
```

Output:

```text
John
```

---

# 8. Removing Items

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane"
};

users.Remove(1);
```

---

# 9. Clearing a Dictionary

```csharp
users.Clear();
```

---

# 10. Iterating Through a Dictionary

## KeyValuePair

```csharp
foreach (var user in users)
{
    Console.WriteLine(
        $"{user.Key}: {user.Value}");
}
```

---

## Tuple Deconstruction (Recommended)

```csharp
foreach (var (id, name) in users)
{
    Console.WriteLine($"{id}: {name}");
}
```

Output:

```text
1: John
2: Jane
3: Bob
```

---

# 11. Iterating Keys

```csharp
foreach (var id in users.Keys)
{
    Console.WriteLine(id);
}
```

---

# 12. Iterating Values

```csharp
foreach (var name in users.Values)
{
    Console.WriteLine(name);
}
```

---

# 13. Nested Dictionaries

```csharp
Dictionary<string, Dictionary<string, string>> departments = new()
{
    ["IT"] = new()
    {
        ["Manager"] = "John",
        ["Developer"] = "Jane"
    },

    ["HR"] = new()
    {
        ["Manager"] = "Bob"
    }
};

Console.WriteLine(
    departments["IT"]["Developer"]);
```

Output:

```text
Jane
```

---

# 14. Dictionary with Records

Modern C# commonly stores records as values.

```csharp
public record User(
    int Id,
    string Name,
    string Email
);

Dictionary<int, User> users = new()
{
    [1] = new(
        1,
        "John",
        "john@example.com"
    ),

    [2] = new(
        2,
        "Jane",
        "jane@example.com"
    )
};

Console.WriteLine(users[1].Name);
```

Output:

```text
John
```

---

# 15. Dictionary with Collection Expressions

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane",
    [3] = "Bob"
};
```

This is the modern style preferred in C# 12+ and C# 14.

---

# 16. LINQ with Dictionaries

## Filter

```csharp
var admins =
    users.Where(user => user.Key > 1);

foreach (var (id, name) in admins)
{
    Console.WriteLine($"{id}: {name}");
}
```

---

## Select

```csharp
var names =
    users.Select(user => user.Value);

foreach (var name in names)
{
    Console.WriteLine(name);
}
```

---

# 17. Converting a List to a Dictionary

```csharp
List<User> users =
[
    new(1, "John", "john@example.com"),
    new(2, "Jane", "jane@example.com")
];

Dictionary<int, User> userDictionary =
    users.ToDictionary(
        user => user.Id
    );
```

---

# 18. Merging Dictionaries

Using collection expressions.

```csharp
Dictionary<int, string> first = new()
{
    [1] = "John",
    [2] = "Jane"
};

Dictionary<int, string> second = new()
{
    [3] = "Bob",
    [4] = "Alice"
};

Dictionary<int, string> merged =
[
    ..first,
    ..second
];
```

> Requires modern collection expression support.

---

# 19. Real-World Example

Caching users by ID.

```csharp
public record User(
    int Id,
    string Name
);

Dictionary<int, User> cache = new();

cache[1] = new(
    1,
    "John"
);

cache[2] = new(
    2,
    "Jane"
);

if (cache.TryGetValue(1, out User? user))
{
    Console.WriteLine(user.Name);
}
```

Output:

```text
John
```

---

# 20. Dictionary Performance

| Operation   | Average Complexity |
| ----------- | ------------------ |
| Add         | O(1)               |
| Update      | O(1)               |
| Remove      | O(1)               |
| Lookup      | O(1)               |
| ContainsKey | O(1)               |

Dictionaries are optimized for fast lookups compared to arrays and lists.

---

# Common Use Cases

- User lookup by ID
- Caching
- Configuration settings
- API response mapping
- Grouping data
- Frequency counters
- In-memory indexes

---

# Summary

## Core Operations

```csharp
users.Add(1, "John");

users[1] = "Jane";

users.Remove(1);

users.ContainsKey(1);

users.TryGetValue(1, out var value);
```

## Iteration

```csharp
foreach (var (key, value) in users)
{
    Console.WriteLine($"{key}: {value}");
}
```

## Modern C# Features

- Target-typed `new()`
- Collection expressions
- Tuple deconstruction
- Records
- LINQ integration

`Dictionary<TKey, TValue>` is one of the most important collections in C#, providing fast key-based access and serving as the foundation for many caching, lookup, and mapping scenarios.

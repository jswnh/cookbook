# 1. Conditional Statements

## if

Execute code when a condition is true.

```csharp
int age = 18;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
```

---

## if-else

```csharp
int age = 16;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
else
{
    Console.WriteLine("Minor");
}
```

---

## if-else if

```csharp
int score = 85;

if (score >= 90)
{
    Console.WriteLine("A");
}
else if (score >= 80)
{
    Console.WriteLine("B");
}
else if (score >= 70)
{
    Console.WriteLine("C");
}
else
{
    Console.WriteLine("Failed");
}
```

---

## Switch Statement

Use a switch statement when you need multiple executable statements, loops, or complex logic inside each branch.

```csharp
string role = "Admin";

switch (role)
{
    case "Admin":
        Console.WriteLine("Full Access");
        break;

    case "Manager":
        Console.WriteLine("Manage Resources");
        break;

    case "User":
        Console.WriteLine("Limited Access");
        break;

    default:
        Console.WriteLine("Unknown Role");
        break;
}
```

### Multiple Cases

```csharp
DayOfWeek day = DateTime.Today.DayOfWeek;

switch (day)
{
    case DayOfWeek.Saturday:
    case DayOfWeek.Sunday:
        Console.WriteLine("Weekend");
        break;

    default:
        Console.WriteLine("Weekday");
        break;
}
```

### Pattern Matching with switch

```csharp
object value = 150;

switch (value)
{
    case int number when number > 100:
        Console.WriteLine("Large Number");
        break;

    case int:
        Console.WriteLine("Small Number");
        break;

    case string:
        Console.WriteLine("Text");
        break;

    case null:
        Console.WriteLine("Null");
        break;

    default:
        Console.WriteLine("Unknown");
        break;
}
```

## Switch Expression

Modern C# prefers switch expressions over traditional switch statements.

```csharp
string role = "Admin";

string access = role switch
{
    "Admin" => "Full Access",
    "User" => "Limited Access",
    _ => "Unknown"
};

Console.WriteLine(access);
```

---

## Pattern Matching

```csharp
object value = 100;

string result = value switch
{
    int number when number > 50 => "Large Number",
    int => "Small Number",
    string => "Text",
    null => "Null",
    _ => "Unknown"
};

Console.WriteLine(result);
```

---

# 2. Loops

## for

Best when the iteration count is known.

```csharp
for (int i = 1; i <= 5; i++)
{
    Console.WriteLine(i);
}
```

---

## foreach

Preferred for iterating collections.

```csharp
List<string> languages =
[
    "C#",
    "TypeScript",
    "Go"
];

foreach (var language in languages)
{
    Console.WriteLine(language);
}
```

---

## while

```csharp
int count = 1;

while (count <= 5)
{
    Console.WriteLine(count);
    count++;
}
```

---

## do-while

Runs at least once.

```csharp
int count = 1;

do
{
    Console.WriteLine(count);
    count++;
}
while (count <= 5);
```

---

## await foreach

Used with asynchronous streams.

```csharp
await foreach (var number in GetNumbersAsync())
{
    Console.WriteLine(number);
}
```

---

# 3. Loop Control Statements

## break

Stops execution of a loop.

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 5)
    {
        break;
    }

    Console.WriteLine(i);
}
```

---

## continue

Skips the current iteration.

```csharp
for (int i = 1; i <= 5; i++)
{
    if (i == 3)
    {
        continue;
    }

    Console.WriteLine(i);
}
```

---

# 4. Arrays

Fixed-size collection.

```csharp
int[] numbers =
[
    10,
    20,
    30,
    40,
    50
];

foreach (var number in numbers)
{
    Console.WriteLine(number);
}
```

---

# 5. List<T>

Dynamic collection.

```csharp
List<string> developers =
[
    "John",
    "Jane",
    "Bob"
];

developers.Add("Alice");

foreach (var developer in developers)
{
    Console.WriteLine(developer);
}
```

---

# 6. Dictionary<TKey, TValue>

Key-value storage.

```csharp
Dictionary<int, string> users = new()
{
    [1] = "John",
    [2] = "Jane",
    [3] = "Bob"
};

foreach (var (id, name) in users)
{
    Console.WriteLine($"{id}: {name}");
}
```

---

# 7. HashSet<T>

Stores unique values.

```csharp
HashSet<string> skills =
[
    "C#",
    "SQL",
    "C#",
    "TypeScript"
];

foreach (var skill in skills)
{
    Console.WriteLine(skill);
}
```

Output:

```text
C#
SQL
TypeScript
```

---

# 8. Queue<T>

FIFO (First In, First Out).

```csharp
Queue<string> orders = new();

orders.Enqueue("Order A");
orders.Enqueue("Order B");
orders.Enqueue("Order C");

while (orders.Count > 0)
{
    Console.WriteLine(orders.Dequeue());
}
```

---

# 9. Stack<T>

LIFO (Last In, First Out).

```csharp
Stack<string> navigation = new();

navigation.Push("Home");
navigation.Push("Products");
navigation.Push("Checkout");

while (navigation.Count > 0)
{
    Console.WriteLine(navigation.Pop());
}
```

---

# 10. LinkedList<T>

Efficient insertions and removals.

```csharp
LinkedList<string> tasks = new();

tasks.AddLast("Task 1");
tasks.AddLast("Task 2");
tasks.AddLast("Task 3");

foreach (var task in tasks)
{
    Console.WriteLine(task);
}
```

---

# 11. SortedSet<T>

Automatically maintains sorted order.

```csharp
SortedSet<int> scores =
[
    90,
    50,
    70,
    100,
    60
];

foreach (var score in scores)
{
    Console.WriteLine(score);
}
```

---

# 12. PriorityQueue<TElement, TPriority>

Priority-based processing.

```csharp
PriorityQueue<string, int> tickets = new();

tickets.Enqueue("Low Priority", 3);
tickets.Enqueue("Medium Priority", 2);
tickets.Enqueue("High Priority", 1);

while (tickets.Count > 0)
{
    Console.WriteLine(tickets.Dequeue());
}
```

---

# 13. Nested Loops

Useful for matrices and multidimensional data.

```csharp
int[,] matrix =
{
    { 1, 2, 3 },
    { 4, 5, 6 }
};

for (int row = 0; row < matrix.GetLength(0); row++)
{
    for (int col = 0; col < matrix.GetLength(1); col++)
    {
        Console.Write($"{matrix[row, col]} ");
    }

    Console.WriteLine();
}
```

---

# 14. Real-World Example

Combining control flow, loops, records, and collections.

```csharp
List<User> users =
[
    new("John", 25),
    new("Jane", 17),
    new("Bob", 32)
];

foreach (var user in users)
{
    string category = user.Age switch
    {
        >= 18 => "Adult",
        _ => "Minor"
    };

    Console.WriteLine($"{user.Name} - {category}");
}

public record User(
    string Name,
    int Age
);
```

---

# Summary

## Control Flow

- if
- if-else
- switch expressions
- pattern matching

## Loops

- for
- foreach
- while
- do-while
- await foreach

## Loop Controls

- break
- continue
- return

## Common Data Structures

| Data Structure                     | Purpose                   |
| ---------------------------------- | ------------------------- |
| Array                              | Fixed-size collection     |
| List<T>                            | Dynamic collection        |
| Dictionary<TKey, TValue>           | Key-value storage         |
| HashSet<T>                         | Unique values             |
| Queue<T>                           | FIFO processing           |
| Stack<T>                           | LIFO processing           |
| LinkedList<T>                      | Fast insertion/removal    |
| SortedSet<T>                       | Sorted unique values      |
| PriorityQueue<TElement, TPriority> | Priority-based processing |

Modern C# encourages the use of collection expressions, pattern matching, switch expressions, records, and asynchronous iteration to write cleaner, more expressive, and maintainable code.

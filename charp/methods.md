# 1. Methods

A method is a named block of code that performs an action.

```csharp
public static void Greet()
{
    Console.WriteLine("Hello, World!");
}

Greet();
```

Output:

```text
Hello, World!
```

---

# 2. Method Parameters

Methods can accept input values.

```csharp
public static void Greet(string name)
{
    Console.WriteLine($"Hello, {name}!");
}

Greet("John");
```

Output:

```text
Hello, John!
```

---

# 3. Returning Values

Methods can return data.

```csharp
public static int Add(int a, int b)
{
    return a + b;
}

int result = Add(10, 20);

Console.WriteLine(result);
```

Output:

```text
30
```

---

# 4. Expression-Bodied Methods

Useful for short methods.

```csharp
public static int Square(int number)
    => number * number;

Console.WriteLine(Square(5));
```

Output:

```text
25
```

---

# 5. Optional Parameters

Provide default values.

```csharp
public static void Greet(
    string name,
    string greeting = "Hello")
{
    Console.WriteLine($"{greeting}, {name}!");
}

Greet("John");
Greet("Jane", "Welcome");
```

---

# 6. Named Arguments

Improve readability.

```csharp
CreateUser(
    name: "John",
    age: 25,
    email: "john@example.com");

public static void CreateUser(
    string name,
    int age,
    string email)
{
    Console.WriteLine(name);
}
```

---

# 7. Method Overloading

Multiple methods with the same name but different parameters.

```csharp
public static int Add(int a, int b)
{
    return a + b;
}

public static double Add(double a, double b)
{
    return a + b;
}
```

Usage:

```csharp
Console.WriteLine(Add(5, 10));
Console.WriteLine(Add(5.5, 10.5));
```

---

# 8. ref Parameters

Pass by reference.

```csharp
public static void Increment(ref int value)
{
    value++;
}

int number = 10;

Increment(ref number);

Console.WriteLine(number);
```

Output:

```text
11
```

---

# 9. out Parameters

Return multiple values.

```csharp
public static void GetUser(
    out string firstName,
    out string lastName)
{
    firstName = "John";
    lastName = "Doe";
}

GetUser(out var first, out var last);

Console.WriteLine($"{first} {last}");
```

---

# 10. params

Accept a variable number of arguments.

```csharp
public static int Sum(params int[] numbers)
{
    return numbers.Sum();
}

Console.WriteLine(
    Sum(1, 2, 3, 4, 5));
```

Output:

```text
15
```

---

# 11. Local Functions

Methods inside methods.

```csharp
void Process()
{
    int Add(int a, int b)
    {
        return a + b;
    }

    Console.WriteLine(Add(10, 20));
}

Process();
```

---

# 12. Lambda Expressions

Anonymous functions.

```csharp
Func<int, int> square =
    number => number * number;

Console.WriteLine(square(5));
```

Output:

```text
25
```

---

# 13. Static Methods

Belong to the type rather than an instance.

```csharp
public static class MathHelper
{
    public static int Double(int value)
        => value * 2;
}

Console.WriteLine(
    MathHelper.Double(10));
```

---

# 14. Instance Methods

Require an object instance.

```csharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}

var calculator = new Calculator();

Console.WriteLine(
    calculator.Add(10, 20));
```

---

# 15. Properties

Properties provide controlled access to fields.

---

## Auto-Implemented Property

```csharp
public class User
{
    public string Name { get; set; } = "";
}
```

Usage:

```csharp
var user = new User();

user.Name = "John";

Console.WriteLine(user.Name);
```

---

# 16. Read-Only Property

Only exposes a getter.

```csharp
public class User
{
    public string Name { get; }

    public User(string name)
    {
        Name = name;
    }
}
```

---

# 17. init Property

Can only be assigned during initialization.

```csharp
public class User
{
    public string Name { get; init; } = "";
}
```

Usage:

```csharp
var user = new User
{
    Name = "John"
};

// user.Name = "Jane"; // Compilation Error
```

---

# 18. required Property

Forces initialization.

```csharp
public class User
{
    public required string Name { get; init; }
}
```

Usage:

```csharp
var user = new User
{
    Name = "John"
};
```

---

# 19. Custom Getter and Setter

Adds validation or custom logic.

```csharp
public class Product
{
    private decimal _price;

    public decimal Price
    {
        get
        {
            return _price;
        }
        set
        {
            if (value < 0)
            {
                throw new ArgumentException(
                    "Price cannot be negative.");
            }

            _price = value;
        }
    }
}
```

---

# 20. Expression-Bodied Property

```csharp
public class Circle
{
    public double Radius { get; init; }

    public double Area =>
        Math.PI * Radius * Radius;
}
```

Usage:

```csharp
var circle = new Circle
{
    Radius = 10
};

Console.WriteLine(circle.Area);
```

---

# 21. Primary Constructor (C# 12+)

Modern way to initialize objects.

```csharp
public class User(string firstName, string lastName)
{
    public string FullName =>
        $"{firstName} {lastName}";
}

var user = new User(
    "John",
    "Doe");

Console.WriteLine(user.FullName);
```

---

# 22. Record with Properties

```csharp
public record User
{
    public required string FirstName { get; init; }

    public required string LastName { get; init; }
}
```

Usage:

```csharp
var user = new User
{
    FirstName = "John",
    LastName = "Doe"
};
```

---

# Summary

## Methods

- Standard methods
- Expression-bodied methods
- Overloading
- Optional parameters
- Named arguments
- ref
- out
- params
- Local functions
- Lambdas

## Properties

- get
- set
- init
- required
- Read-only properties
- Custom getters and setters
- Expression-bodied properties

## Modern C# Features

- Primary constructors
- Records
- Collection expressions
- Pattern matching

Methods define behavior, while properties control access to data. Together they form the foundation of encapsulation and object-oriented design in modern C#.

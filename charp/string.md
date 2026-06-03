---

# 1. Creating Strings

## String Literal

```csharp
string firstName = "John";
string lastName = "Doe";
```

---

## Empty String

```csharp
string empty = "";
```

```csharp
string empty = string.Empty;
```

---

## Raw String Literals

Introduced in modern C# for multi-line text.

```csharp
string json = """
{
    "name": "John",
    "age": 25
}
""";

Console.WriteLine(json);
```

---

# 2. String Interpolation

Preferred way to build strings.

```csharp
string name = "John";
int age = 25;

string message =
    $"Name: {name}, Age: {age}";

Console.WriteLine(message);
```

Output:

```text
Name: John, Age: 25
```

---

# 3. Concatenation

## Using +

```csharp
string fullName =
    firstName + " " + lastName;
```

---

## Using string.Concat

```csharp
string fullName =
    string.Concat(firstName, " ", lastName);
```

---

# 4. Length

```csharp
string name = "OpenAI";

Console.WriteLine(name.Length);
```

Output:

```text
6
```

---

# 5. Accessing Characters

```csharp
string name = "OpenAI";

Console.WriteLine(name[0]);
Console.WriteLine(name[1]);
```

Output:

```text
O
p
```

---

# 6. Contains

```csharp
string email = "john@example.com";

bool hasDomain =
    email.Contains("@");

Console.WriteLine(hasDomain);
```

Output:

```text
True
```

---

## Case-Insensitive Contains

```csharp
string text = "Hello World";

bool exists =
    text.Contains(
        "world",
        StringComparison.OrdinalIgnoreCase);
```

---

# 7. StartsWith

```csharp
string url = "https://openai.com";

Console.WriteLine(
    url.StartsWith("https"));
```

---

# 8. EndsWith

```csharp
string file = "report.pdf";

Console.WriteLine(
    file.EndsWith(".pdf"));
```

---

# 9. IndexOf

Finds the position of a substring.

```csharp
string text = "Hello World";

int position =
    text.IndexOf("World");

Console.WriteLine(position);
```

Output:

```text
6
```

---

# 10. Replace

```csharp
string text = "Hello World";

string result =
    text.Replace("World", "C#");

Console.WriteLine(result);
```

Output:

```text
Hello C#
```

---

# 11. ToUpper

```csharp
string text = "hello";

Console.WriteLine(
    text.ToUpper());
```

Output:

```text
HELLO
```

---

# 12. ToLower

```csharp
string text = "HELLO";

Console.WriteLine(
    text.ToLower());
```

Output:

```text
hello
```

---

# 13. Trim

Removes whitespace.

```csharp
string text = "   Hello World   ";

Console.WriteLine(
    text.Trim());
```

Output:

```text
Hello World
```

---

## TrimStart

```csharp
text.TrimStart();
```

---

## TrimEnd

```csharp
text.TrimEnd();
```

---

# 14. Split

Convert a string into multiple parts.

```csharp
string csv =
    "John,Jane,Bob";

string[] names =
    csv.Split(',');

foreach (var name in names)
{
    Console.WriteLine(name);
}
```

---

# 15. Join

Combine multiple strings.

```csharp
string[] names =
[
    "John",
    "Jane",
    "Bob"
];

string result =
    string.Join(", ", names);

Console.WriteLine(result);
```

Output:

```text
John, Jane, Bob
```

---

# 16. Substring

Extract a portion of a string.

```csharp
string text = "Hello World";

string value =
    text.Substring(6);

Console.WriteLine(value);
```

Output:

```text
World
```

---

# 17. Remove

```csharp
string text = "Hello World";

string result =
    text.Remove(5);

Console.WriteLine(result);
```

Output:

```text
Hello
```

---

# 18. Insert

```csharp
string text = "Hello";

string result =
    text.Insert(5, " World");

Console.WriteLine(result);
```

Output:

```text
Hello World
```

---

# 19. String Comparison

## Equality

```csharp
string a = "John";
string b = "John";

Console.WriteLine(a == b);
```

Output:

```text
True
```

---

## Case-Insensitive Comparison

```csharp
bool same =
    string.Equals(
        "john",
        "JOHN",
        StringComparison.OrdinalIgnoreCase);

Console.WriteLine(same);
```

Output:

```text
True
```

---

# 20. StringBuilder

Use for many modifications.

```csharp
using System.Text;

StringBuilder builder = new();

builder.Append("Hello");
builder.Append(" ");
builder.Append("World");

Console.WriteLine(builder.ToString());
```

Output:

```text
Hello World
```

---

# 21. StringBuilder Chaining

```csharp
using System.Text;

StringBuilder builder = new();

builder
    .Append("John")
    .Append(" ")
    .Append("Doe");

Console.WriteLine(builder);
```

---

# 22. Null or Empty Checks

```csharp
string? value = "";

bool result =
    string.IsNullOrEmpty(value);

Console.WriteLine(result);
```

---

# 23. Null, Empty, or Whitespace

```csharp
string? value = " ";

bool result =
    string.IsNullOrWhiteSpace(value);

Console.WriteLine(result);
```

Output:

```text
True
```

---

# 24. String Enumeration

```csharp
string word = "OpenAI";

foreach (char character in word)
{
    Console.WriteLine(character);
}
```

---

# 25. Collection Expressions with Strings

```csharp
List<string> languages =
[
    "C#",
    "TypeScript",
    "Python",
    "Go"
];

foreach (var language in languages)
{
    Console.WriteLine(language);
}
```

---

# 26. Real-World Example

Generating a username.

```csharp
string firstName = "John";
string lastName = "Doe";

string username =
    $"{firstName}.{lastName}"
        .ToLower()
        .Replace(" ", "");

Console.WriteLine(username);
```

Output:

```text
john.doe
```

---

# 27. Modern String Formatting

```csharp
string name = "John";
decimal salary = 50000;

Console.WriteLine(
    $"{name} earns {salary:C}");
```

Example Output:

```text
John earns $50,000.00
```

---

# Summary

## Creation

- String literals
- Raw string literals
- String interpolation

## Searching

- Contains
- StartsWith
- EndsWith
- IndexOf

## Modification

- Replace
- Trim
- Insert
- Remove
- Substring

## Comparison

- ==
- string.Equals
- StringComparison

## Utilities

- Split
- Join
- Length
- StringBuilder
- IsNullOrEmpty
- IsNullOrWhiteSpace

## Modern C# Features

- Raw string literals (`"""`)
- String interpolation (`$""`)
- Collection expressions (`[]`)
- Case-insensitive comparisons using `StringComparison`

Strings are immutable in C#, so every modification creates a new string. For frequent modifications, prefer `StringBuilder` to reduce allocations and improve performance.

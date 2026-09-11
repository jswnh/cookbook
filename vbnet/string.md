# 1. Creating Strings

In VB.NET, strings are instances of `System.String`. They are immutable reference types stored on the managed heap.

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## String Literal

```vb
Dim firstName As String = "John"
Dim lastName As String = "Doe"
```

---

## Empty String & Nothing

In VB.NET, `Nothing` for a string represents a null reference, but many VB string operations treat `Nothing` as equivalent to an empty string.

```vb
Dim emptyStr As String = ""
Dim explicitEmpty As String = String.Empty
Dim nullStr As String = Nothing

Console.WriteLine(String.IsNullOrEmpty(nullStr))   ' True
Console.WriteLine(String.IsNullOrEmpty(emptyStr))  ' True
```

---

# 2. String Interpolation

Prefixed with `$` to embed expressions and format specifiers directly.

```vb
Dim name As String = "Josuan"
Dim points As Integer = 1250
Dim passRate As Double = 0.98675
Dim balance As Decimal = 149.99D

Dim message As String = $"User {name} earned {points:N0} pts (Pass: {passRate:P1}, Balance: {balance:C2})"
Console.WriteLine(message)
```

Output:

```text
User Josuan earned 1,250 pts (Pass: 98.7%, Balance: $149.99)
```

---

# 3. Common String Methods

## Inspection & Emptiness

```vb
Dim text As String = "  VB.NET 17  "

Dim len As Integer = text.Length ' 13

Dim isEmpty As Boolean = String.IsNullOrEmpty(text) ' False
Dim isBlank As Boolean = String.IsNullOrWhiteSpace("   ") ' True
```

---

## Trimming

```vb
Dim raw As String = "  Hello World  "

Console.WriteLine($"'{raw.Trim()}'")      ' 'Hello World'
Console.WriteLine($"'{raw.TrimStart()}'") ' 'Hello World  '
Console.WriteLine($"'{raw.TrimEnd()}'")   ' '  Hello World'
```

---

## Searching and Substrings

```vb
Dim path As String = "src/modules/UserModule.vb"

' Substring(startIndex, length)
Dim filename As String = path.Substring(12, 13) ' UserModule.vb

' Slicing to end
Dim ext As String = path.Substring(path.LastIndexOf("."c)) ' .vb

' Verification
Dim hasModules As Boolean = path.Contains("modules") ' True
Dim isVbFile As Boolean = path.EndsWith(".vb")       ' True
Dim startsWithSrc As Boolean = path.StartsWith("src/") ' True

' Index of substring
Dim idx As Integer = path.IndexOf("User") ' 12
```

---

## Replacing

```vb
Dim message As String = "The color is red. The sky is red."

' Replaces all occurrences
Dim updated As String = message.Replace("red", "blue")
Console.WriteLine(updated)
' Output: The color is blue. The sky is blue.
```

---

## Splitting and Joining

```vb
Dim tags As String = "vb,dotnet,csharp,azure"

' Splitting by char delimiter
Dim tagArray() As String = tags.Split(","c)

' Joining with delimiter
Dim formatted As String = String.Join(" | ", tagArray)
Console.WriteLine(formatted) ' vb | dotnet | csharp | azure
```

---

## Case Conversion

```vb
Dim title As String = "Visual Basic"

Console.WriteLine(title.ToUpper()) ' VISUAL BASIC
Console.WriteLine(title.ToLower()) ' visual basic
```

---

# 4. String Equality & Comparison

For culture-invariant or case-insensitive string comparisons, use `String.Equals` with `StringComparison`:

```vb
Imports System

Dim str1 As String = "VB.NET"
Dim str2 As String = "vb.net"

' Case-sensitive comparison
Dim exactMatch As Boolean = str1.Equals(str2) ' False

' Case-insensitive ordinal comparison
Dim caseInsensitiveMatch As Boolean = str1.Equals(str2, StringComparison.OrdinalIgnoreCase) ' True
```

---

# 5. High-Performance Building (`StringBuilder`)

When concatenating strings inside loops, repeated `&` or `+` operations allocate many intermediate strings. Use `System.Text.StringBuilder`.

```vb
Imports System.Text

Dim builder As New StringBuilder()

For i As Integer = 1 To 5
    builder.Append("Item-").Append(i).Append(" ")
Next

Dim finalString As String = builder.ToString()
Console.WriteLine(finalString) ' Item-1 Item-2 Item-3 Item-4 Item-5
```

---

# 6. Real-World Example

Sanitizing, parsing, and validating user input.

```vb
Imports System

Public Module UserDataSanitizer
    Public Structure UserRecord
        Public Property Username As String
        Public Property Email As String
        Public Property Domain As String
    End Structure

    Public Function ProcessUserInput(rawUser As String, rawEmail As String) As UserRecord
        If String.IsNullOrWhiteSpace(rawUser) Then
            Throw New ArgumentException("Username cannot be empty")
        End If

        If String.IsNullOrWhiteSpace(rawEmail) OrElse Not rawEmail.Contains("@"c) Then
            Throw New ArgumentException("Invalid email address")
        End If

        Dim cleanUser As String = rawUser.Trim().ToLower()
        Dim cleanEmail As String = rawEmail.Trim().ToLower()

        Dim parts() As String = cleanEmail.Split("@"c)
        Dim emailDomain As String = parts(1)

        Return New UserRecord With {
            .Username = cleanUser,
            .Email = cleanEmail,
            .Domain = emailDomain
        }
    End Function

    Public Sub Main()
        Dim user = ProcessUserInput("   JosuanDev   ", "   JOSUAN@EXAMPLE.COM   ")
        Console.WriteLine($"User: {user.Username} | Email: {user.Email} | Domain: {user.Domain}")
    End Sub
End Module
```

Output:

```text
User: josuandev | Email: josuan@example.com | Domain: example.com
```

---

# Summary

| Task | Modern VB.NET Approach |
| :--- | :--- |
| **Interpolation** | `$"Hello {name}, Age {age}"` |
| **Concatenation** | `str1 & str2` |
| **Blankness check**| `String.IsNullOrWhiteSpace(str)` |
| **Trimming** | `str.Trim()`, `str.TrimStart()`, `str.TrimEnd()` |
| **Substring** | `str.Substring(startIndex, length)` |
| **Search** | `str.Contains(text)`, `str.StartsWith(prefix)`, `str.EndsWith(suffix)` |
| **Replace** | `str.Replace(oldVal, newVal)` |
| **Split / Join** | `str.Split(","c)`, `String.Join(", ", arr)` |
| **Large Concatenations**| `System.Text.StringBuilder` |

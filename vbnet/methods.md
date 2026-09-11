# 1. Sub vs Function

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

In VB.NET, procedures that perform actions are divided into:
- **`Sub`**: Does not return a value (equivalent to `void`).
- **`Function`**: Returns a calculated value.

```vb
Public Module Greeter
    Public Sub Greet()
        Console.WriteLine("Hello, World!")
    End Sub

    Public Function Add(a As Integer, b As Integer) As Integer
        Return a + b
    End Function

    Public Sub Main()
        Greet()
        Dim sum As Integer = Add(10, 20)
        Console.WriteLine($"Sum: {sum}")
    End Sub
End Module
```

---

# 2. Parameters: ByVal vs ByRef

- **`ByVal` (Default)**: Passes a copy of the argument. Modifications inside the method do not reassign the caller's variable.
- **`ByRef`**: Passes a reference to the caller's variable. Modifications directly change the caller's original variable.

```vb
Public Module ParameterDemo
    Public Sub IncrementByVal(ByVal x As Integer)
        x += 10
    End Sub

    Public Sub IncrementByRef(ByRef x As Integer)
        x += 10
    End Sub

    Public Sub Main()
        Dim num As Integer = 10

        IncrementByVal(num)
        Console.WriteLine($"After ByVal: {num}") ' 10 (Unchanged)

        IncrementByRef(num)
        Console.WriteLine($"After ByRef: {num}") ' 20 (Modified)
    End Sub
End Module
```

---

# 3. Optional Parameters

Optional parameters must provide a default compile-time constant value and must appear at the end of the parameter list.

```vb
Public Sub DisplayGreeting(name As String, Optional greeting As String = "Hello")
    Console.WriteLine($"{greeting}, {name}!")
End Sub

DisplayGreeting("John")            ' Hello, John!
DisplayGreeting("Jane", "Welcome") ' Welcome, Jane!
```

---

# 4. Named Arguments

Named arguments improve readability and allow parameters to be supplied in any order.

```vb
Public Sub RegisterUser(name As String, age As Integer, email As String)
    Console.WriteLine($"Registered {name}, Age {age}, Contact: {email}")
End Sub

RegisterUser(email:="john@example.com", name:="John", age:=25)
```

---

# 5. Method Overloading

The `Overloads` keyword explicitly declares that a method shares the name of another method within the same scope with different parameter types or counts.

```vb
Public Class Calculator
    Public Overloads Function Multiply(a As Integer, b As Integer) As Integer
        Return a * b
    End Function

    Public Overloads Function Multiply(a As Double, b As Double) As Double
        Return a * b
    End Function

    Public Overloads Function Multiply(a As Integer, b As Integer, c As Integer) As Integer
        Return a * b * c
    End Function
End Class
```

---

# 6. ParamArray (Variable Arguments)

Accepts zero or more arguments of a specified type, passed as an array.

```vb
Public Function SumAll(ParamArray numbers() As Integer) As Integer
    Dim total As Integer = 0
    For Each n In numbers
        total += n
    Next
    Return total
End Function

Dim r1 As Integer = SumAll()              ' 0
Dim r2 As Integer = SumAll(10, 20)        ' 30
Dim r3 As Integer = SumAll(1, 2, 3, 4, 5) ' 15
```

> [!NOTE]
> `ParamArray` can only be applied to the last parameter in the signature and cannot be combined with `Optional` or `ByRef`.

---

# 7. Extension Methods

Extension methods allow you to add new methods to existing types without modifying or inheriting from them. They must be declared in a standard `Module` and decorated with `<Extension()>`.

```vb
Imports System.Runtime.CompilerServices

Public Module StringExtensions
    <Extension()>
    Public Function ToTitleCase(input As String) As String
        If String.IsNullOrWhiteSpace(input) Then Return input
        Return Globalization.CultureInfo.CurrentCulture.TextInfo.ToTitleCase(input)
    End Function
End Module

' Usage:
Dim title As String = "hello modern vb.net".ToTitleCase()
Console.WriteLine(title) ' Hello Modern Vb.Net
```

---

# 8. Lambda Expressions & Delegates

VB.NET supports both single-line and multi-line lambda expressions for both `Sub` and `Function`.

```vb
Imports System

' Expression Function Lambda: (x) -> x * 2
Dim doubleVal = Function(x As Integer) x * 2
Console.WriteLine(doubleVal(5)) ' 10

' Statement Sub Lambda:
Dim logAction = Sub(msg As String)
                    Dim timestamp = DateTime.UtcNow.ToString("s")
                    Console.WriteLine($"[{timestamp}] {msg}")
                End Sub

logAction("Operation completed")

' Using standard Func(Of T, TResult) and Action(Of T)
Dim isEven As Predicate(Of Integer) = Function(n) n Mod 2 = 0
Console.WriteLine(isEven(4)) ' True
```

---

# 9. Real-World Example

Validation and processing pipeline using lambdas and LINQ.

```vb
Imports System
Imports System.Collections.Generic
Imports System.Linq

Public Module PipelineExample
    Public Structure Order
        Public Property Id As String
        Public Property Amount As Decimal
        Public Property IsVerified As Boolean
    End Structure

    Public Sub Main()
        Dim orders As New List(Of Order) From {
            New Order With {.Id = "O1", .Amount = 250.0D, .IsVerified = True},
            New Order With {.Id = "O2", .Amount = -10.0D, .IsVerified = True},
            New Order With {.Id = "O3", .Amount = 120.0D, .IsVerified = False},
            New Order With {.Id = "O4", .Amount = 450.0D, .IsVerified = True}
        }

        Dim isValidOrder = Function(o As Order) o.Amount > 0D AndAlso o.IsVerified

        Dim approvedOrders = orders.Where(isValidOrder).ToList()

        For Each ord In approvedOrders
            Console.WriteLine($"Approved: {ord.Id} (${ord.Amount:F2})")
        Next
    End Sub
End Module
```

Output:

```text
Approved: O1 ($250.00)
Approved: O4 ($450.00)
```

---

# Summary

| Concept | Syntax / Description |
| :--- | :--- |
| **`Sub`** | Procedure that returns no value |
| **`Function`** | Procedure that returns a value (`Return value`) |
| **`ByVal`** | Passes copy of parameter (default) |
| **`ByRef`** | Passes reference allowing modification of caller's variable |
| **`Optional`** | Parameter with default fallback value |
| **`Overloads`** | Multiple signatures for the same method name |
| **`ParamArray`** | Variable number of arguments |
| **`<Extension()>`**| Extension method defined in a Module |
| **Lambda** | `Function(x) x * 2` or `Sub(msg) Console.WriteLine(msg)` |

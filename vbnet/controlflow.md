# 1. Conditional Statements

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## If...Then

Execute code when a condition evaluates to `True`.

```vb
Dim age As Integer = 18

If age >= 18 Then
    Console.WriteLine("Adult")
End If
```

---

## If...Then...Else

```vb
Dim age As Integer = 16

If age >= 18 Then
    Console.WriteLine("Adult")
Else
    Console.WriteLine("Minor")
End If
```

---

## If...Then...ElseIf

Chain multiple conditional branches.

```vb
Dim score As Integer = 85

If score >= 90 Then
    Console.WriteLine("Grade: A")
ElseIf score >= 80 Then
    Console.WriteLine("Grade: B")
ElseIf score >= 70 Then
    Console.WriteLine("Grade: C")
Else
    Console.WriteLine("Grade: F")
End If
```

---

## Inline Ternary `If()` Operator

The ternary `If(condition, truePart, falsePart)` operator short-circuits evaluation.

```vb
Dim age As Integer = 20
Dim status As String = If(age >= 18, "Eligible", "Ineligible")

Console.WriteLine(status)
```

---

# 2. Select Case Statement

`Select Case` handles multi-branch conditions cleanly with support for comma-separated lists, value ranges, and relational `Is` operators.

```vb
Dim role As String = "Admin"

Select Case role
    Case "Admin"
        Console.WriteLine("Full system access granted")

    Case "Manager"
        Console.WriteLine("Resource management access")

    Case "User"
        Console.WriteLine("Standard access")

    Case Else
        Console.WriteLine("Unknown role")
End Select
```

---

## Multiple Values, Ranges, and `Is` Comparisons

```vb
Dim score As Integer = 88

Select Case score
    Case 100
        Console.WriteLine("Perfect score!")

    Case 90 To 99
        Console.WriteLine("Grade: A")

    Case 80 To 89
        Console.WriteLine("Grade: B")

    Case 70 To 79
        Console.WriteLine("Grade: C")

    Case Is < 70
        Console.WriteLine("Failing grade")

    Case Else
        Console.WriteLine("Invalid score")
End Select
```

---

# 3. Loops

## For...Next Loop

Used when the number of iterations is known in advance.

```vb
' Basic 1 to 5
For i As Integer = 1 To 5
    Console.WriteLine($"Iteration: {i}")
Next

' Stepping backwards
For count As Integer = 10 To 0 Step -2
    Console.Write($"{count} ")
Next
' Output: 10 8 6 4 2 0
```

---

## For Each...Next Loop

The standard idiomatic way to traverse arrays and `IEnumerable(Of T)` collections.

```vb
Dim frameworks() As String = {"ASP.NET Core", "WPF", "WinForms", "MAUI"}

For Each framework In frameworks
    Console.WriteLine(framework)
Next
```

---

## Do While...Loop and Do...Loop While

- `Do While...Loop`: checks the condition **before** entering the loop body.
- `Do...Loop While`: executes **at least once** before evaluating the condition.

```vb
Dim counter As Integer = 1

' Pre-tested condition
Do While counter <= 3
    Console.WriteLine($"Count: {counter}")
    counter += 1
Loop

' Post-tested condition
Dim attempts As Integer = 0
Do
    Console.WriteLine($"Attempt: {attempts}")
    attempts += 1
Loop While attempts < 2
```

---

## Do Until...Loop and Do...Loop Until

Executes until the condition evaluates to `True`.

```vb
Dim balance As Integer = 100

Do Until balance <= 0
    balance -= 25
    Console.WriteLine($"Remaining: {balance}")
Loop
```

---

## While...End While Loop

```vb
Dim index As Integer = 0

While index < 3
    Console.WriteLine($"Index: {index}")
    index += 1
End While
```

---

# 4. Loop Control Statements

## Exit Statements (`Exit For`, `Exit While`, `Exit Do`)

Immediately terminates loop execution.

```vb
For i As Integer = 1 To 10
    If i = 5 Then
        Exit For
    End If
    Console.Write($"{i} ")
Next
' Output: 1 2 3 4
```

---

## Continue Statements (`Continue For`, `Continue While`, `Continue Do`)

Skips the remainder of the current iteration and begins the next.

```vb
For i As Integer = 1 To 5
    If i Mod 2 = 0 Then
        Continue For
    End If
    Console.Write($"{i} ")
Next
' Output: 1 3 5
```

---

# 5. Nested Loops

```vb
Dim matrix(,) As Integer = {
    {1, 2, 3},
    {4, 5, 6}
}

For r As Integer = 0 To matrix.GetUpperBound(0)
    For c As Integer = 0 To matrix.GetUpperBound(1)
        Console.Write($"{matrix(r, c)} ")
    Next
    Console.WriteLine()
Next
```

---

# 6. Real-World Example

Order batch status processor combining `For Each`, `Select Case`, and collections.

```vb
Imports System
Imports System.Collections.Generic

Public Module OrderBatchProcessor
    Public Structure Order
        Public Property OrderId As String
        Public Property Amount As Decimal
        Public Property StatusCode As Integer
    End Structure

    Public Sub Main()
        Dim orders As New List(Of Order) From {
            New Order With {.OrderId = "ORD-1", .Amount = 149.99D, .StatusCode = 1},
            New Order With {.OrderId = "ORD-2", .Amount = 45.0D, .StatusCode = 2},
            New Order With {.OrderId = "ORD-3", .Amount = 0.0D, .StatusCode = 9},
            New Order With {.OrderId = "ORD-4", .Amount = 890.5D, .StatusCode = 3}
        }

        For Each ord In orders
            If ord.Amount <= 0 Then
                Console.WriteLine($"[FLAGGED] {ord.OrderId}: Invalid zero or negative amount")
                Continue For
            End If

            Dim statusDescription As String

            Select Case ord.StatusCode
                Case 1
                    statusDescription = "Pending Processing"
                Case 2
                    statusDescription = "Dispatched / In Transit"
                Case 3
                    statusDescription = "Delivered Successfully"
                Case 9
                    statusDescription = "Cancelled by Customer"
                Case Else
                    statusDescription = "Unknown Status"
            End Select

            Console.WriteLine($"Order {ord.OrderId} (${ord.Amount:F2}) -> {statusDescription}")
        Next
    End Sub
End Module
```

Output:

```text
Order ORD-1 ($149.99) -> Pending Processing
Order ORD-2 ($45.00) -> Dispatched / In Transit
[FLAGGED] ORD-3: Invalid zero or negative amount
Order ORD-4 ($890.50) -> Delivered Successfully
```

---

# Summary

| Construct | Syntax | Description |
| :--- | :--- | :--- |
| **If** | `If condition Then ... End If` | Standard conditional block |
| **Inline If** | `If(cond, valIfTrue, valIfFalse)` | Short-circuit ternary expression |
| **Select Case**| `Select Case val ... Case ... End Select` | Multi-way branching with ranges and comparisons |
| **For...Next** | `For i = start To finish [Step n] ... Next` | Indexed loop counter |
| **For Each** | `For Each item In collection ... Next` | Traverses elements in an iterable |
| **Do While** | `Do While cond ... Loop` | Repeats while condition is True |
| **Do Until** | `Do Until cond ... Loop` | Repeats until condition becomes True |
| **Exit** | `Exit For`, `Exit Do`, `Exit While` | Immediately breaks out of the loop |
| **Continue** | `Continue For`, `Continue Do` | Skips to next loop iteration |

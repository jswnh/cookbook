## 1. Value Types

Value types store data directly in memory (typically on the stack).

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

### Integral Types (Whole Numbers)

| Keyword | CLR Type | Size | Range | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`SByte`** | `System.SByte` | 8-bit | -128 to 127 | `Dim level As SByte = -10` |
| **`Byte`** | `System.Byte` | 8-bit | 0 to 255 | `Dim age As Byte = 25` |
| **`Short`** | `System.Int16` | 16-bit | -32,768 to 32,767 | `Dim elevation As Short = -500` |
| **`UShort`** | `System.UInt16` | 16-bit | 0 to 65,535 | `Dim port As UShort = 8080US` |
| **`Integer`** | `System.Int32` | 32-bit | -2,147,483,648 to 2,147,483,647 | `Dim population As Integer = 1_500_000` |
| **`UInteger`**| `System.UInt32` | 32-bit | 0 to 4,294,967,295 | `Dim distance As UInteger = 3000000UI` |
| **`Long`** | `System.Int64` | 64-bit | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | `Dim timestamp As Long = 100000000000L` |
| **`ULong`** | `System.UInt64` | 64-bit | 0 to 18,446,744,073,709,551,615 | `Dim maxBytes As ULong = 18446744073709551615UL` |

> [!NOTE]
> Numeric literals in modern VB.NET support digit separators (`_`) for improved readability, e.g. `1_000_000` and binary literals `&B1010_0101`.

### Floating-Point & Decimal Types

| Keyword | CLR Type | Size | Best For | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`Single`** | `System.Single` | 32-bit | Single-precision floating point (Graphics, 3D math) | `Dim pi As Single = 3.14F` |
| **`Double`** | `System.Double` | 64-bit | Double-precision floating point (Scientific calc) | `Dim latitude As Double = 37.7749295R` |
| **`Decimal`**| `System.Decimal`| 128-bit| Financial and monetary calculations (high precision) | `Dim balance As Decimal = 1999.99D` |

### Other Built-in Value Types

| Keyword | CLR Type | Description | Example |
| :--- | :--- | :--- | :--- |
| **`Char`** | `System.Char` | 16-bit single Unicode character | `Dim grade As Char = "A"c` |
| **`Boolean`** | `System.Boolean` | Logical value (`True` or `False`) | `Dim isActive As Boolean = True` |
| **`Date`** | `System.DateTime` | Date and time representation | `Dim created As Date = #2026-06-15 10:30:00 AM#` |
| **`Structure`**| `System.ValueType` | User-defined lightweight value type | `Public Structure Point : Dim X, Y As Integer : End Structure` |
| **`Enum`** | `System.Enum` | User-defined set of named constants | `Public Enum Status : Pending : Approved : Denied : End Enum` |

---

## 2. Reference Types

Reference types store memory addresses pointing to data allocated on the managed heap.

| Keyword | CLR Type | Description | Example |
| :--- | :--- | :--- | :--- |
| **`String`** | `System.String` | A sequence of Unicode characters | `Dim message As String = "Hello, VB.NET!"` |
| **`Object`** | `System.Object` | The root base class for all .NET types | `Dim data As Object = "Universal reference"` |
| **`Class`** | Custom | User-defined reference type (OOP foundation) | `Public Class User : Public Property Name As String : End Class` |
| **`Interface`**| Custom | Defines contract signatures implemented by types | `Public Interface IRepository : Sub Save() : End Interface` |
| **`Delegate`** | `System.Delegate` | Type-safe reference pointer to a method | `Public Delegate Sub LogHandler(msg As String)` |

---

## 3. Type Conversion & Nullability

### Conversion Operators

```vb
' Explicit conversion functions
Dim countStr As String = "42"
Dim count As Integer = CInt(countStr)
Dim balance As Decimal = CDec("199.95")

' DirectCast: fast, requires explicit inheritance or implementation relationship
Dim obj As Object = "Hello"
Dim text As String = DirectCast(obj, String)

' TryCast: safe reference casting; returns Nothing on failure without throwing an exception
Dim baseObj As Object = 123
Dim result As String = TryCast(baseObj, String) ' result is Nothing
```

### Nullable Value Types

Value types can be made nullable by appending `?` or using `Nullable(Of T)`.

```vb
Dim middleInitial As Char? = Nothing
Dim score As Integer? = 95

If score.HasValue Then
    Console.WriteLine($"Score: {score.Value}")
End If

' Null-coalescing expression: returns score if not Nothing, else 0
Dim finalScore As Integer = If(score, 0)
```

---

## 4. Modern Type Features

### Type Inference (`Dim` with `Option Infer On`)

The compiler automatically infers the variable type based on the initialization value.

```vb
' Inferred as System.String
Dim greeting = "Welcome to modern VB.NET"

' Inferred as System.Collections.Generic.List(Of System.String)
Dim languages = New List(Of String) From {"VB.NET", "C#", "F#"}

' Inferred as System.Collections.Generic.Dictionary(Of String, Integer)
Dim scores = New Dictionary(Of String, Integer) From {{"Alice", 95}}
```

### ValueTuples

Lightweight value-type tuples with named elements for returning multiple values without allocating class objects.

```vb
Dim coordinate As (X As Integer, Y As Integer) = (10, 20)
Console.WriteLine($"X: {coordinate.X}, Y: {coordinate.Y}")
```

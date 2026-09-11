# 1. Core Generic Type Definitions

Generics allow you to write reusable, type-safe classes, structures, interfaces, and methods without incurring the performance costs and casting risks of `System.Object`.

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## Generic Classes

Declared with the `(Of T)` syntax.

```vb
Public Class Box(Of T)
    Private _item As T

    Public Sub SetItem(item As T)
        _item = item
    End Sub

    Public Function GetItem() As T
        Return _item
    End Function
End Class

Dim stringBox As New Box(Of String)()
stringBox.SetItem("Hello Generics")
Dim text As String = stringBox.GetItem()
```

---

## Generic Interfaces

```vb
Public Interface IRepository(Of T, ID)
    Sub Save(entity As T)
    Function FindById(id As ID) As T
End Interface
```

---

## Generic Methods

Methods can declare their own independent type parameters.

```vb
Public Module GenericUtilities
    Public Sub Swap(Of T)(ByRef a As T, ByRef b As T)
        Dim temp As T = a
        a = b
        b = temp
    End Sub
End Module

Dim x As Integer = 10
Dim y As Integer = 20
GenericUtilities.Swap(x, y)
Console.WriteLine($"x={x}, y={y}") ' x=20, y=10
```

---

## Generic Structures

```vb
Public Structure Payload(Of T)
    Public Property Timestamp As Long
    Public Property Data As T
End Structure
```

---

# 2. Generic Constraints

Constraints enforce that a type argument meets specific criteria. When multiple constraints are applied to a single type parameter, enclose them in curly braces `{}`.

| Constraint Syntax | Meaning | Example |
| :--- | :--- | :--- |
| **`As Class`** | Must be a reference type | `(Of T As Class)` |
| **`As Structure`** | Must be a non-nullable value type | `(Of T As Structure)` |
| **`As New`** | Must feature a public parameterless constructor | `(Of T As New)` |
| **`As BaseClass`** | Must inherit from the specified base class | `(Of T As Vehicle)` |
| **`As Interface`** | Must implement the specified interface | `(Of T As IComparable)` |
| **Multiple** | Must satisfy all listed constraints | `(Of T As {Class, IComparable, New})` |

### Constraint Example

```vb
Public Class EntityRepository(Of T As {Class, IComparable(Of T), New})
    Public Function CreateAndCompare(existing As T) As Boolean
        Dim newInstance As New T()
        Return newInstance.CompareTo(existing) = 0
    End Function
End Class
```

---

# 3. Variance Operators (`In` and `Out`)

Variance governs type compatibility when assigning generic interfaces or delegates with related type arguments.

## Covariant (`Out T`)

Permits using a more derived type than originally expected. The type parameter can only appear as an **output** (return type).

```vb
Public Interface IProducer(Of Out T)
    Function Produce() As T
End Interface

' Usage: IProducer(Of String) can be assigned to IProducer(Of Object)
Dim stringProd As IProducer(Of String) = Nothing
Dim objectProd As IProducer(Of Object) = stringProd
```

---

## Contravariant (`In T`)

Permits using a less derived (more generic) type. The type parameter can only appear as an **input** (parameter).

```vb
Public Interface IConsumer(Of In T)
    Sub Consume(item As T)
End Interface

' Usage: IConsumer(Of Object) can be assigned to IConsumer(Of String)
Dim objectConsumer As IConsumer(Of Object) = Nothing
Dim stringConsumer As IConsumer(Of String) = objectConsumer
```

---

# 4. Real-World Example

A type-safe generic in-memory repository cache with constraints.

```vb
Imports System
Imports System.Collections.Generic

Public Interface IEntity(Of TKey)
    ReadOnly Property Id As TKey
End Interface

Public Class CacheRepository(Of TKey, T As {Class, IEntity(Of TKey)})
    Private ReadOnly _store As New Dictionary(Of TKey, T)()

    Public Sub AddOrUpdate(item As T)
        If item Is Nothing Then Throw New ArgumentNullException(NameOf(item))
        _store(item.Id) = item
    End Sub

    Public Function Find(id As TKey) As T
        Dim result As T = Nothing
        _store.TryGetValue(id, result)
        Return result
    End Function

    Public Function GetAll() As IEnumerable(Of T)
        Return _store.Values
    End Function
End Class

' Entity Model
Public Class User
    Implements IEntity(Of String)

    Public ReadOnly Property Id As String Implements IEntity(Of String).Id
    Public Property Name As String

    Public Sub New(id As String, name As String)
        Me.Id = id
        Me.Name = name
    End Sub
End Class

Public Module Program
    Public Sub Main()
        Dim cache As New CacheRepository(Of String, User)()
        cache.AddOrUpdate(New User("USR-1", "Josuan"))
        cache.AddOrUpdate(New User("USR-2", "Sarah"))

        Dim user = cache.Find("USR-1")
        Console.WriteLine($"Found: {user?.Name}")
    End Sub
End Module
```

Output:

```text
Found: Josuan
```

---

# Summary

| Concept | VB.NET Syntax | Purpose |
| :--- | :--- | :--- |
| **Generic Declaration** | `(Of T)` | Defines parameterized placeholder |
| **Reference Constraint**| `(Of T As Class)` | Enforces reference type |
| **Value Constraint** | `(Of T As Structure)` | Enforces value type |
| **Default Constructor** | `(Of T As New)` | Allows `New T()` instantiation |
| **Multiple Constraints**| `(Of T As {Class, IEntity})` | Combines multiple constraint criteria |
| **Covariance** | `(Of Out T)` | Enables safe polymorphic return types |
| **Contravariance** | `(Of In T)` | Enables safe polymorphic argument input |

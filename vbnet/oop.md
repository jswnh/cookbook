# Object-Oriented Programming (OOP) in VB.NET

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## 1. The Four Pillars of OOP

| Pillar | Definition | Purpose |
| :--- | :--- | :--- |
| **Encapsulation** | Bundling state and methods together while restricting direct access to internal fields. | Protects object integrity and prevents unauthorized mutation. |
| **Inheritance** | Creating new classes based on existing ones using `Inherits`. | Enables code reuse and defines an "is-a" taxonomy. |
| **Polymorphism** | Allowing derived classes to override methods (`Overridable` / `Overrides`) accessed via base types. | Provides dynamic runtime dispatch and flexibility. |
| **Abstraction** | Hiding implementation details behind `MustInherit` classes and `Interface` contracts. | Exposes clean, high-level APIs while decoupling callers from internals. |

---

### Comprehensive Example Demonstrating All Four Pillars

```vb
Imports System
Imports System.Collections.Generic

' 1. ABSTRACTION: Interface Contract
Public Interface IPaymentGateway
    Function ProcessPayment(amount As Decimal) As PaymentResult
End Interface

Public Structure PaymentResult
    Public Property Success As Boolean
    Public Property TransactionId As String
    Public Property Message As String
End Structure

' 2. ENCAPSULATION & INHERITANCE: Abstract Base Class
Public MustInherit Class BaseGateway
    Implements IPaymentGateway

    ' Encapsulated private state
    Private _transactionCount As Integer = 0

    Public ReadOnly Property GatewayName As String

    Protected Sub New(name As String)
        If String.IsNullOrWhiteSpace(name) Then
            Throw New ArgumentException("Gateway name cannot be blank")
        End If
        Me.GatewayName = name
    End Sub

    Protected Sub IncrementCount()
        _transactionCount += 1
    End Sub

    Public ReadOnly Property TotalProcessed As Integer
        Get
            Return _transactionCount
        End Get
    End Property

    ' Abstract method must be implemented by derived classes
    Public MustOverride Function ProcessPayment(amount As Decimal) As PaymentResult Implements IPaymentGateway.ProcessPayment
End Class

' 3. INHERITANCE & POLYMORPHISM: Concrete Implementations
Public Class CreditCardGateway
    Inherits BaseGateway

    Public Property MerchantId As String

    Public Sub New(merchantId As String)
        MyBase.New("CreditCard")
        Me.MerchantId = merchantId
    End Sub

    Public Overrides Function ProcessPayment(amount As Decimal) As PaymentResult
        IncrementCount()
        Dim txId As String = $"CC-{DateTime.UtcNow.Ticks}"
        Return New PaymentResult With {
            .Success = True,
            .TransactionId = txId,
            .Message = $"Processed {amount:C2} via merchant {MerchantId}"
        }
    End Function
End Class

Public Class CryptoGateway
    Inherits BaseGateway

    Public Property WalletAddress As String

    Public Sub New(walletAddress As String)
        MyBase.New("Crypto")
        Me.WalletAddress = walletAddress
    End Sub

    Public Overrides Function ProcessPayment(amount As Decimal) As PaymentResult
        IncrementCount()
        Dim txId As String = $"0x{DateTime.UtcNow.Ticks:X}"
        Return New PaymentResult With {
            .Success = True,
            .TransactionId = txId,
            .Message = $"Sent {amount:C2} to wallet {WalletAddress}"
        }
    End Function
End Class

' POLYMORPHIC EXECUTION
Public Module Program
    Public Sub Main()
        Dim gateways As New List(Of IPaymentGateway) From {
            New CreditCardGateway("MERCH-1002"),
            New CryptoGateway("0x9F4B...A1C")
        }

        For Each gw In gateways
            Dim res = gw.ProcessPayment(129.5D)
            Console.WriteLine($"[{If(res.Success, "OK", "FAIL")}] ID: {res.TransactionId} -> {res.Message}")
        Next
    End Sub
End Module
```

---

# 2. Classes, Constructors, and Properties

## Constructors (`Sub New`)

Constructors are declared using `Sub New`. Base constructors are invoked via `MyBase.New()`.

```vb
Public Class Customer
    ' Auto-implemented property
    Public Property Name As String

    ' ReadOnly auto-implemented property
    Public ReadOnly Property CustomerId As String

    Public Sub New(id As String, name As String)
        Me.CustomerId = id
        Me.Name = name
    End Sub
End Class
```

---

## Full Properties with Backing Fields

```vb
Public Class Account
    Private _balance As Decimal

    Public Property Balance As Decimal
        Get
            Return _balance
        End Get
        Set(value As Decimal)
            If value < 0 Then Throw New ArgumentException("Balance cannot be negative")
            _balance = value
        End Set
    End Property
End Class
```

---

# 3. Access Modifiers

| Modifier | Accessibility Scope |
| :--- | :--- |
| **`Public`** | Unrestricted access from anywhere |
| **`Protected`** | Accessible only within the declaring class and derived classes |
| **`Friend`** | Accessible only within the same compilation assembly (internal) |
| **`Protected Friend`** | Accessible within the assembly OR in derived classes outside the assembly |
| **`Private`** | Accessible only within the declaring class |

---

# 4. Abstract Classes vs Interfaces

| Feature | `MustInherit Class` | `Interface` |
| :--- | :--- | :--- |
| **Implementation** | Inherited with `Inherits` (single inheritance) | Implemented with `Implements` (multiple interfaces allowed) |
| **Fields / State** | Can contain instance variables, fields, and constructors | No state or instance fields |
| **Constructors** | Can define constructors | Cannot have constructors |
| **Method Modifiers**| `MustOverride`, `Overridable`, concrete methods | Signatures only |

```vb
Public Interface IShape
    Function CalculateArea() As Double
End Interface

Public Class Circle
    Implements IShape

    Public Property Radius As Double

    Public Sub New(r As Double)
        Me.Radius = r
    End Sub

    Public Function CalculateArea() As Double Implements IShape.CalculateArea
        Return Math.PI * Radius * Radius
    End Function
End Class
```

---

# 5. Polymorphism Keywords

- **`Overridable`**: Declares that a method in a base class can be overridden in derived classes (equivalent to `virtual`).
- **`Overrides`**: Overrides an `Overridable` base method in a derived class.
- **`MustInherit`**: Declares that a class cannot be instantiated directly (equivalent to `abstract`).
- **`MustOverride`**: Declares that a method has no implementation and must be overridden in subclasses.
- **`NotInheritable`**: Prevents other classes from inheriting from this class (equivalent to `sealed`).
- **`MyBase`**: References the immediate base class implementation.

---

# 6. Modules and Shared Members

- **`Shared`**: Belongs to the class type itself rather than an instance (equivalent to `static`).
- **`Module`**: A container for shared procedures and variables. Cannot be instantiated or inherited.

```vb
Public Module MathHelpers
    Public Const PiApproximation As Double = 3.14159

    Public Function Square(x As Double) As Double
        Return x * x
    End Function
End Module
```

---

# Summary

| Keyword | Equivalent (C#/Java) | Purpose |
| :--- | :--- | :--- |
| **`Inherits`** | `: BaseClass` / `extends` | Inherits from a base class |
| **`Implements`** | `: IInterface` / `implements`| Implements an interface contract |
| **`Sub New`** | Constructor | Initializes new class instance |
| **`MyBase`** | `base` / `super` | Accesses base class members |
| **`MustInherit`** | `abstract class` | Cannot be instantiated directly |
| **`MustOverride`**| `abstract method` | Must be implemented by subclass |
| **`Overridable`** | `virtual` | Can be overridden by subclass |
| **`Overrides`** | `override` | Implements polymorphic override |
| **`NotInheritable`**| `sealed` / `final` | Blocks further subclassing |
| **`Shared`** | `static` | Member belongs to type itself |

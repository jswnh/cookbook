# Object-Oriented Programming (OOP) in TypeScript

## 1. The Four Pillars of OOP

Object-Oriented Programming (OOP) uses objects to model real-world entities and behaviors. TypeScript provides a robust, class-based OOP implementation built on top of JavaScript's prototype system.

| Pillar            | Definition                                                                                | Purpose                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Encapsulation** | Bundling data and behavior together while restricting direct access to internal state.    | Protects object integrity and reduces coupling.             |
| **Inheritance**   | Creating new classes that derive from existing classes to reuse and extend behavior.      | Promotes code reuse and establishes an "is-a" relationship. |
| **Polymorphism**  | Allowing different implementations to be accessed through a common interface or base type.| Enables flexible and extensible designs.                    |
| **Abstraction**   | Exposing only essential behavior while hiding implementation details.                     | Reduces complexity and improves maintainability.            |

### Example

```typescript
// ABSTRACTION
export interface NotificationChannel {
    send(message: string): void;
}

// ENCAPSULATION + INHERITANCE
export abstract class NotificationBase implements NotificationChannel {
    public readonly senderAddress: string;
    private _status: string = "Pending";

    constructor(senderAddress: string) {
        this.senderAddress = senderAddress;
    }

    public getStatus(): string {
        return this._status;
    }

    protected updateStatus(newStatus: string): void {
        this._status = newStatus;
    }

    public abstract send(message: string): void;
}

// INHERITANCE + POLYMORPHISM
export class EmailNotification extends NotificationBase {
    public readonly recipientEmail: string;

    constructor(senderAddress: string, recipientEmail: string) {
        super(senderAddress);
        this.recipientEmail = recipientEmail;
    }

    public override send(message: string): void {
        console.log(`[Email] From: ${this.senderAddress} To: ${this.recipientEmail}`);
        console.log(`Content: ${message}`);
        this.updateStatus("Dispatched via SMTP");
    }
}

export class SmsNotification extends NotificationBase {
    public readonly mobileNumber: string;

    constructor(senderAddress: string, mobileNumber: string) {
        super(senderAddress);
        this.mobileNumber = mobileNumber;
    }

    public override send(message: string): void {
        console.log(`[SMS] To: ${this.mobileNumber}`);
        console.log(`Content: ${message}`);
        this.updateStatus("Sent via Telecom Gateway");
    }
}
```

---

## 2. Access Modifiers

TypeScript supports three access modifiers that are validated at compile-time. It also supports ES Private fields (`#`) that enforce privacy at runtime.

| Modifier             | Accessibility                                                             |
| -------------------- | ------------------------------------------------------------------------- |
| `public`             | Accessible from anywhere (default).                                       |
| `private`            | Accessible only within the containing class (compile-time check only).    |
| `#field` (ES Private)| Hard private field. Inaccessible outside the class even at JS runtime.     |
| `protected`          | Accessible within the containing class and derived classes.                |

### Example

```typescript
export class AccountManager {
    public publicIdentifier: string = "PUB-1029";
    private _accountBalance: number = 5500.25;  // Compile-time private
    #secureKey: string = "SECRET_123";          // Runtime private
    protected internalNotes: string = "Review required";
    internal systemCode: string = "SYS_ALPHA_9"; // No internal keyword; use ES modules instead

    public processTransaction(amount: number): void {
        this._accountBalance += amount;
    }
}

export class PremiumAccountManager extends AccountManager {
    public displayNotes(): void {
        console.log(this.internalNotes); // Allowed (protected)

        // Compilation errors:
        // console.log(this._accountBalance); 
        // console.log(this.#secureKey);
    }
}
```

---

## 3. Abstract Classes vs Interfaces

| Feature          | Purpose                                                                   | Can Contain State? |
| ---------------- | ------------------------------------------------------------------------- | ------------------ |
| **`override` / virtual**| All methods in JS/TS are virtual by default. `override` keyword ensures signature matching. | Yes |
| **`abstract class`**| Provides shared state and behavior while forcing certain implementations.| Yes |
| **`interface`**  | Defines a contract that classes/objects must implement (disappears at runtime).| No state           |

### Example

```typescript
export interface Renderable {
    render(): void;
}

export abstract class UIComponent implements Renderable {
    constructor(public width: number, public height: number) {}

    public abstract render(): void;

    public handleInput(): void {
        console.log("Standard UI input processed.");
    }
}

export class Button extends UIComponent {
    public label: string = "Submit";

    public override render(): void {
        console.log(
            `Rendering button [${this.label}] with dimensions ${this.width}x${this.height}`
        );
    }

    public override handleInput(): void {
        super.handleInput();
        console.log("Button click event triggered.");
    }
}
```

---

## 4. Modern TypeScript Features

TypeScript has features that align with C#'s modern object construction and data layout.

| Feature                    | Description                                                       | Use Case                                                      |
| -------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- |
| **Parameter Properties**   | Constructor arguments with visibility modifiers automatically become fields.| Shortening constructor boilerplates.                         |
| **Structural Typing**      | Types are checked based on shape (members) rather than inheritance names.| Duck typing and clean integration with external shapes.       |
| **`readonly`**             | Properties that can only be written to during object construction.| Immutable data models (analogous to `init`).                  |
| **`Readonly<T>`**          | Utility type that converts all properties of `T` to readonly.     | Enforcing immutability on complex interfaces or payloads.     |

### Parameter Properties & Readonly Example

```typescript
export class Order {
    constructor(
        public readonly orderId: string,
        public readonly totalCost: number
    ) {}
}

const order1 = new Order("11111111", 100);
// order1.totalCost = 200; // Compilation Error: totalCost is readonly
```

### Structural Equality (Value-based comparisons require custom logic)

```typescript
const order1 = { orderId: "1111", totalCost: 100 };
const order2 = { orderId: "1111", totalCost: 100 };

console.log(order1 === order2); // false (Reference equality in JS)

// Structural comparison must be manual or using lodash:
const isEqual = JSON.stringify(order1) === JSON.stringify(order2);
console.log(isEqual); // true
```

---

# Summary

OOP in TypeScript bridges static, type-safe structures with dynamic JavaScript execution:

- **Encapsulation** uses `private`, `protected`, and native `#` properties.
- **Inheritance** uses standard class hierarchies (`extends`).
- **Polymorphism** is achieved through abstract class overrides and structural typing.
- **Abstraction** uses structural `interface` contracts and `abstract class` declarations.

TypeScript enhances OOP with constructor parameter properties and type mapping utilities (`Readonly<T>`, etc.) to build clean, testable, and self-documenting architectures.

## 1. PHPDoc-Based Generics (PHPStan & Psalm)

PHP does not natively support runtime generic parameters (like `<T>`). Instead, modern PHP relies on **static analysis tools** (such as **PHPStan** and **Psalm**) to provide compile-time/static type safety and autocomplete using PHPDoc annotations (e.g., `@template T`).

| Generic Concept        | Definition / Annotation                                                            | Primary Use Case                                                                | Example                                                              |
| :--------------------- | :------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :------------------------------------------------------------------- |
| **Generic Classes**    | Classes declared with `@template T` in their header block.                       | Implementing custom data structures, wrappers, or universal business managers.  | [See Generic Class Example](#generic-class)                          |
| **Generic Methods**    | Methods declared with `@template T` independent of their containing class.       | Utility functions like parsing, mapping, or serialization.                      | [See Generic Method Example](#generic-method)                        |
| **Generic Interfaces** | Interfaces that expose type-parameterized signatures for implementation.          | Decoupling architectures via standard contracts (e.g. Repositories).            | [See Generic Interface Example](#generic-interface)                  |

---

## 2. Generic Class Example

```php
/**
 * @template T
 */
class Payload
{
    /**
     * @param T $data
     */
    public function __construct(
        public mixed $data
    ) {}

    /**
     * @return T
     */
    public function getData(): mixed
    {
        return $this->data;
    }
}

// IDEs and PHPStan know that $result is of type User
/** @var Payload<User> $payload */
$payload = new Payload(new User());
$result = $payload->getData();
```

---

## 3. Generic Method Example

```php
class Logger
{
    /**
     * @template T
     * @param T $message
     * @return T
     */
    public function log(mixed $message): mixed
    {
        echo print_r($message, true) . "\n";
        return $message;
    }
}
```

---

## 4. Generic Interface Example

```php
/**
 * @template T
 */
interface Repository
{
    /**
     * @param string $id
     * @return T|null
     */
    public function getById(string $id): mixed;
}

/**
 * @implements Repository<User>
 */
class UserRepository implements Repository
{
    public function getById(string $id): ?User
    {
        return new User();
    }
}
```

---

## 5. Generic Constraints (`of` Clause)

You can restrict the types that can satisfy the placeholder using the `of` keyword.

| Constraint Syntax          | Mechanic                                                               | Primary Use Case                                                                | Example                                                      |
| :------------------------- | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :----------------------------------------------------------- |
| **`@template T of class`** | Type must be an object instance class.                                 | Restricting parameters to object classes.                                       | `* @template T of User`                                      |
| **`@template T of string`**| Type must be a string.                                                 | Enforcing basic string representations.                                         | `* @template T of string`                                    |

### Example

```php
/**
 * @template T of Vehicle
 */
class Fleet
{
    /** @var array<int, T> */
    private array $vehicles = [];

    /**
     * @param T $vehicle
     */
    public function addVehicle(mixed $vehicle): void
    {
        $this->vehicles[] = $vehicle;
    }
}
```

---

## 6. Generic Variance (`@template-covariant`)

Psalm and PHPStan support explicit variance annotations to ensure type safety on assignments.

```php
/**
 * Covariant type parameter (only used as output / return type)
 * @template-covariant T
 */
interface Producer
{
    /**
     * @return T
     */
    public function produce(): mixed;
}
```

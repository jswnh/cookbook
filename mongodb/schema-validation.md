## 1. Creating a Collection with Schema Validation

Schema validation rules are configured during collection creation using the `validator` parameter with the `$jsonSchema` operator.

```javascript
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["username", "email", "age", "status"],
      properties: {
        username: {
          bsonType: "string",
          minLength: 3,
          maxLength: 30,
          description:
            "username must be a string between 3 and 30 characters and is required",
        },
        email: {
          bsonType: "string",
          pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$",
          description:
            "email must match a valid email regex pattern and is required",
        },
        age: {
          bsonType: "int",
          minimum: 18,
          maximum: 120,
          description:
            "age must be an integer between 18 and 120 and is required",
        },
        status: {
          enum: ["active", "pending", "suspended"],
          description:
            "status can only be one of the pre-defined enum values and is required",
        },
        roles: {
          bsonType: "array",
          items: {
            bsonType: "string",
          },
          description: "roles must be an array of strings",
        },
      },
    },
  },
  validationLevel: "strict",
  validationAction: "error",
});
```

---

## 2. Validation Configurations

When creating or modifying validators, you can specify validation behavior using two parameters:

### validationAction

Determines how MongoDB handles documents that violate validation rules:

- **`"error"`**: Default. Rejects the insert/update write completely and throws an validation exception.
- **`"warn"`**: Logs the violation in the MongoDB server logs, but allows the invalid write to complete successfully.

### validationLevel

Determines how strictly rules are applied to pre-existing documents during updates:

- **`"strict"`**: Default. Applies validation rules to all inserts and all updates.
- **`"moderate"`**: Applies validation rules to inserts, and updates of _new_ documents. Updates of pre-existing documents that were already invalid are ignored (unless the update edits validated fields).

---

## 3. Modifying Validation Rules (collMod)

To add, update, or remove validation rules on an existing collection, use the **`collMod`** database command.

```javascript
-- Add a new 'phone' string requirement to the 'users' collection
db.runCommand({
    collMod: "users",
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["username", "email", "age", "status", "phone"],
            properties: {
                username: { bsonType: "string" },
                email: { bsonType: "string" },
                age: { bsonType: "int" },
                status: { enum: ["active", "pending", "suspended"] },
                phone: {
                    bsonType: "string",
                    pattern: "^\\+[1-9]\\d{1,14}$", -- E.164 phone format
                    description: "phone must be a valid E.164 phone number"
                }
            }
        }
    }
});
```

### Removing Validation Rules

```javascript
-- Remove validation completely by passing an empty validator object
db.runCommand({
    collMod: "users",
    validator: {}
});
```

---

## 4. Key JSON Schema Rules

- **`bsonType`**: Matches BSON data types (e.g. `"string"`, `"int"`, `"double"`, `"decimal"`, `"objectId"`, `"array"`, `"object"`).
- **`pattern`**: Applies regular expression validations to strings.
- **`minimum` / `maximum`**: Sets range bounds for numbers.
- **`items`**: Validates the content type or schema of items inside an array.
- **`additionalProperties`**: If set to `false`, MongoDB will reject any document that contains fields not explicitly listed in the `properties` schema definition.

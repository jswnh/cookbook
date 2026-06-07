## 1. Create Operations

```javascript
-- 1. Insert a single document
db.users.insertOne({
    name: "John Doe",
    email: "john@example.com",
    age: NumberInt(28)
});

-- 2. Insert multiple documents
db.users.insertMany([
    { name: "Alice", email: "alice@example.com", age: NumberInt(24) },
    { name: "Bob", email: "bob@example.com", age: NumberInt(32) }
]);
```

---

## 2. Read Operations (find)

```javascript
db.collection.find(filter, projection);
```

### Basic Querying & Projection

```javascript
-- Fetch active users, returning ONLY name and email (exclude _id)
db.users.find(
    { status: "active" },
    { name: 1, email: 1, _id: 0 }
);
```

### Comparison Query Operators

- **`$eq` / `$ne`**: Matches values equal/not equal to a value.
- **`$gt` / `$gte`**: Greater than / greater than or equal to.
- **`$lt` / `$lte`**: Less than / less than or equal to.
- **`$in` / `$nin`**: Matches any/none of the values specified in an array.

```javascript
-- Find users aged between 25 and 35 (inclusive) with specific roles
db.users.find({
    age: { $gte: NumberInt(25), $lte: NumberInt(35) },
    role: { $in: ["admin", "editor"] }
});
```

### Logical Query Operators

- **`$and` / `$or`**: Joins query clauses with logical AND/OR.
- **`$not`**: Inverts the effect of a query expression.

```javascript
db.users.find({
  $or: [{ status: "pending" }, { login_count: { $gt: NumberInt(100) } }],
});
```

### Element and Array Operators

- **`$exists`**: Matches documents that have the specified field.
- **`$all`**: Matches arrays that contain all elements of the query.
- **`$elemMatch`**: Selects documents if at least one array element matches all criteria.

```javascript
-- Find users who have the 'tags' array field containing BOTH 'node' and 'mongodb'
db.users.find({ tags: { $all: ["node", "mongodb"] } });

-- Find users with at least one active billing address in zip '10001'
db.users.find({
    addresses: {
        $elemMatch: { type: "billing", zip: "10001", active: true }
    }
});
```

---

## 3. Update Operations

```javascript
db.collection.updateOne(filter, update, options);
```

### Atomic Update Operators

- **`$set`**: Sets the value of a field.
- **`$unset`**: Deletes the specified field.
- **`$inc`**: Increments the field value by a specified number.
- **`$currentDate`**: Sets the field value to the current date/timestamp.

```javascript
-- Update score, set status to active, and record modification timestamp
db.users.updateOne(
    { email: "john@example.com" },
    {
        $inc: { score: NumberInt(10) },
        $set: { status: "active" },
        $currentDate: { updated_at: true }
    },
    { upsert: true } -- If no user matches, insert this document as new
);
```

### Array Update Operators

- **`$push`**: Appends a value to an array.
- **`$addToSet`**: Adds a value to an array _only_ if the value does not already exist (guarantees uniqueness).
- **`$pull`**: Removes all array elements that match a specified query.

```javascript
-- Add a role uniquely, and append a log message
db.users.updateOne(
    { email: "john@example.com" },
    {
        $addToSet: { roles: "superuser" },
        $push: { activity_logs: "Granted superuser access" }
    }
);
```

### Positional Array Updates

- **`$`**: Acts as a placeholder for the _first_ element that matches the query document filter.
- **`$[<identifier>]`**: Updates _all_ elements in the array that match the `arrayFilters` conditions.

```javascript
-- 1. Update zip code of the matched work address (updates first match)
db.users.updateOne(
    { email: "john@example.com", "addresses.type": "work" },
    { $set: { "addresses.$.zip": "10022" } }
);

-- 2. Update status of ALL addresses inside the array that are inactive
db.users.updateOne(
    { email: "john@example.com" },
    { $set: { "addresses.$[elem].active": true } },
    { arrayFilters: [{ "elem.active": false }] }
);
```

---

## 4. Delete Operations

```javascript
-- 1. Delete a single matching document
db.users.deleteOne({ status: "banned" });

-- 2. Delete all matching documents
db.users.deleteMany({ login_count: NumberInt(0), status: "inactive" });
```

---

## 5. Bulk Write Operations (bulkWrite)

`bulkWrite` executes multiple write operations (inserts, updates, deletes) in a single server round-trip, optimizing throughput.

```javascript
db.users.bulkWrite([
    {
        insertOne: {
            document: { name: "Charlie", email: "charlie@example.com" }
        }
    },
    {
        updateOne: {
            filter: { email: "bob@example.com" },
            update: { $set: { status: "verified" } }
        }
    },
    {
        deleteOne: {
            filter: { status: "expired" }
        }
    }
], { ordered: false }); -- ordered: false allows operations to continue even if one fails
```

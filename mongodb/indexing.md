# MongoDB Indexes & Query Optimization Reference (v7.0 / v8.0)

Indexes store a small portion of the collection's data set in an easy-to-traverse B-Tree structure. Proper indexing prevents expensive Collection Scans (COLLSCAN).

---

## 1. Index Types

### Single Field Index
```javascript
-- Create ascending index on email (1 = Ascending, -1 = Descending)
db.users.createIndex({ email: 1 });
```

### Compound Index (Multi-field)
An index on multiple fields, e.g. `{ status: 1, age: -1 }`.
- **Leftmost Prefix Rule**: A query filtering by `status` or by `status` AND `age` can use this index. A query filtering by `age` *alone* cannot use this index.
- **Sort Ordering**: If an index is `{ status: 1, age: -1 }`, it supports sorting on `{ status: 1, age: -1 }` or `{ status: -1, age: 1 }` (reverse). It does NOT support sorting on `{ status: 1, age: 1 }`.

```javascript
db.users.createIndex({ status: 1, age: -1 });
```

### Multikey Index (Array Fields)
If you index a field containing an array, MongoDB automatically indexes each element in the array.

```javascript
-- Indexes each tag string in the array
db.users.createIndex({ tags: 1 });
```
> [!WARNING]
> You cannot create a compound multikey index where *more than one* of the indexed fields is an array (e.g. `{ tags: 1, roles: 1 }` is invalid if both are arrays, as it creates a combinatorial explosion of index entries).

### TTL (Time-To-Live) Index
Automatically removes documents from a collection after a specified number of seconds or at a specific date. Useful for sessions or transient logs.

```javascript
-- Automatically delete logs 3600 seconds (1 hour) after creation
db.logs.createIndex(
    { created_at: 1 },
    { expireAfterSeconds: 3600 }
);
```

### Partial Indexes
Only indexes documents that meet a specified filter condition. Reduces index storage and write overhead.

```javascript
-- Only index active users' emails
db.users.createIndex(
    { email: 1 },
    { partialFilterExpression: { status: "active" } }
);
```

### Sparse Indexes
Only indexes documents containing the indexed field (skips null/missing values).
```javascript
db.users.createIndex({ twitter_handle: 1 }, { sparse: true });
```
*Note*: Partial indexes are more powerful and generally preferred over sparse indexes.

---

## 2. Index Management Syntax

```javascript
-- 1. List all indexes on a collection
db.users.getIndexes();

-- 2. Drop a specific index (by name or key structure)
db.users.dropIndex("email_1");
db.users.dropIndex({ email: 1 });

-- 3. Drop all non-primary key (_id) indexes on a collection
db.users.dropIndexes();
```

---

## 3. Query Analysis with explain()

Append `.explain("executionStats")` to queries to examine the execution plan chosen by the query planner.

```javascript
db.users.find({ email: "john@example.com" }).explain("executionStats");
```

### Key Stages in explain() Output

- **`COLLSCAN`**: Collection Scan. The engine scanned every document in the database collection. **Indicates a missing index.**
- **`IXSCAN`**: Index Scan. The engine traversed the B-Tree index to locate matches. Good.
- **`FETCH`**: The engine retrieved the full document pages from disk/memory after finding matching keys in the index.
- **`PROJECTION_COVERED`**: Covering Index. If the query projections select *only* fields that are present in the index itself, MongoDB skips the `FETCH` stage entirely, resolving the query solely from the B-Tree.
```javascript
-- Covered query: requires index on { email: 1, name: 1 }
db.users.find({ email: "john@example.com" }, { name: 1, _id: 0 });
```

### Profiling Metrics to Watch
- **`totalKeysExamined`**: Number of index keys traversed.
- **`totalDocsExamined`**: Number of actual documents read from disk/memory.
- **`nReturned`**: Number of documents returned to client.
- **Optimal Ratio**: In an ideal query, `totalKeysExamined` $\approx$ `totalDocsExamined` $\approx$ `nReturned`. If `totalDocsExamined` is high while `nReturned` is 1, index selection is inefficient.

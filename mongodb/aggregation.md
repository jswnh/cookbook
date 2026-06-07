## 1. Core Pipeline Stages

| Stage | Purpose | SQL Analogue |
| :--- | :--- | :--- |
| **`$match`** | Filters documents based on conditions. Put `$match` as early as possible to utilize indexes. | `WHERE` |
| **`$project`** | Reshapes documents, adds computed fields, includes or excludes fields. | `SELECT` (columns & math) |
| **`$group`** | Groups input documents by a specified identifier expression and applies accumulators. | `GROUP BY` & Aggregates |
| **`$sort`** | Sorts documents by keys. | `ORDER BY` |
| **`$limit` / `$skip`**| Restricts document count / skips ahead. | `LIMIT` / `OFFSET` |
| **`$unwind`** | Deconstructs array fields, outputting a separate copy of the document for each element. | (Flattening arrays) |
| **`$lookup`** | Performs a Left Outer Join to another collection. | `LEFT JOIN` |
| **`$merge`** | Writes the results of the pipeline into another collection (supports merge/upsert). | `INSERT INTO ... ON CONFLICT` |

---

## 2. Basic Aggregation Example

```javascript
db.orders.aggregate([
    -- Stage 1: Filter completed orders in 2026
    {
        $match: {
            status: "completed",
            order_date: { $gte: ISODate("2026-01-01") }
        }
    },
    
    -- Stage 2: Group by customer, summing totals and counting orders
    {
        $group: {
            _id: "$customer_id",
            total_spent: { $sum: "$amount" },
            order_count: { $sum: 1 },
            bought_items: { $addToSet: "$item_name" } -- Unique items list
        }
    },
    
    -- Stage 3: Filter customers spending more than $500
    {
        $match: {
            total_spent: { $gt: NumberDecimal("500.00") }
        }
    },
    
    -- Stage 4: Sort by total spend descending
    {
        $sort: { total_spent: -1 }
    }
]);
```

---

## 3. Unwinding Arrays ($unwind)

`$unwind` is used to split arrays into individual documents so that you can filter or group by elements inside the array.

```javascript
-- Sample Document: { name: "Alice", colors: ["red", "blue"] }
db.users.aggregate([
    { $unwind: "$colors" }
]);
-- Output Document 1: { name: "Alice", colors: "red" }
-- Output Document 2: { name: "Alice", colors: "blue" }
```

---

## 4. Joins ($lookup)

MongoDB supports Left Outer Joins between collections using `$lookup`.

### 1. Basic Equality Join
Joins local field key to foreign field key.

```javascript
-- Join orders collection to customers collection
db.orders.aggregate([
    {
        $lookup: {
            from: "customers",          -- Target collection
            localField: "customer_id",  -- Field in orders
            foreignField: "_id",        -- Field in customers
            as: "customer_details"      -- Output array field name
        }
    }
]);
```

### 2. Correlated Subquery Join (Complex Joins)
Allows writing an entire custom query pipeline on the foreign collection, referencing local fields via variables (`let`).

```javascript
-- Join orders to payments, matching ID and filtering payments over $100
db.orders.aggregate([
    {
        $lookup: {
            from: "payments",
            let: { order_id_var: "$_id" }, -- Define variable from local order document
            pipeline: [
                {
                    $match: {
                        $expr: {
                            $and: [
                                { $eq: ["$order_id", "$$order_id_var"] }, -- Ref local variable via $$
                                { $gt: ["$amount", 100] }
                            ]
                        }
                    }
                }
            ],
            as: "matching_payments"
        }
    }
]);
```

---

## 5. Multi-dimensional Aggregations ($facet)

`$facet` executes multiple pipelines simultaneously on the same input documents. Highly useful for generating dashboard stats (e.g. returning count by status AND group averages in a single call).

```javascript
db.products.aggregate([
    {
        $facet: {
            -- Pipeline 1: Categorization count
            "category_counts": [
                { $group: { _id: "$category", count: { $sum: 1 } } }
            ],
            -- Pipeline 2: Top rated products
            "top_rated": [
                { $sort: { rating: -1 } },
                { $limit: 3 }
            ]
        }
    }
]);
```

---

## 6. Output Destination ($merge)

Added in MongoDB 4.2+, `$merge` allows writing the aggregation results directly into a collection, performing updates, inserts, or discards if a conflict on the unique key occurs.

```javascript
db.orders.aggregate([
    { $group: { _id: "$customer_id", spent: { $sum: "$amount" } } },
    {
        $merge: {
            into: "customer_lifetime_value",
            on: "_id", -- Key to match on in destination collection
            whenMatched: "replace", -- Replace document if matched
            whenNotMatched: "insert" -- Insert document if not matched
        }
    }
]);
```
- **`$out` (Alternative)**: Truncates and fully replaces the target collection (not recommended for incremental updates).

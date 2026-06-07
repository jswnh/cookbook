# MongoDB Transactions & Concurrency Reference (v7.0 / v8.0)

MongoDB supports single-document atomic updates out of the box. For operations spanning multiple documents or collections, MongoDB supports full ACID multi-document transactions inside Replica Sets and Sharded Clusters.

---

## 1. Single-Document vs. Multi-Document Transactions

- **Single-Document Atomicity**: All writes to a single document are atomic. If you update nested fields or arrays within one document, the write is guaranteed to succeed or fail as a unit. Joins are not required, reducing the necessity for transactions in properly modeled schemas.
- **Multi-Document Transactions**: Used when writes to *multiple* separate documents or collections must commit or roll back as a single block.

---

## 2. Multi-Document Transaction Syntax (Shell/JS)

Transactions are executed within a client **Session**.

```javascript
// 1. Start a session
const session = db.getMongo().startSession();

// 2. Start a transaction block with optional configuration
session.startTransaction({
    readConcern: { level: "snapshot" },
    writeConcern: { w: "majority" }
});

try {
    // 3. Execute operations, explicitly passing the session object
    const accounts = session.getDatabase("bank").getCollection("accounts");
    
    // Debit Account 1
    accounts.updateOne(
        { account_id: 1, balance: { $gte: 100 } },
        { $inc: { balance: -100 } },
        { session }
    );
    
    // Credit Account 2
    accounts.updateOne(
        { account_id: 2 },
        { $inc: { balance: 100 } },
        { session }
    );
    
    // 4. Commit the transaction
    session.commitTransaction();
    print("Transaction committed successfully.");
} 
catch (error) {
    // 5. Abort the transaction on error
    print("Error encountered. Rolling back transaction: " + error);
    session.abortTransaction();
} 
finally {
    // 6. Close the session resources
    session.endSession();
}
```

---

## 3. Replica Set Concurrency Controls (Concerns)

MongoDB uses read concerns, write concerns, and read preferences to regulate consistency, durability, and high availability in replica sets.

### Write Concerns (`w` and `j`)
Regulates the acknowledgment behavior of write operations.

- **`w: 1`**: Default. Acknowledges write after it is committed to the local primary node.
- **`w: "majority"`**: Acknowledges write only after it is committed to a majority of data-bearing replica set nodes. **Highly recommended to prevent rollbacks during primary failovers.**
- **`j: true`**: Acknowledges write only after it is flushed to the on-disk journal log (guarantees durability against power failures).
- **`wtimeout`**: Limits acknowledgment wait times to prevent queries from blocking indefinitely if a replica node goes offline.

```javascript
-- Standard secure write configuration
db.products.insertOne(
    { name: "Tablet", stock: 10 },
    { writeConcern: { w: "majority", j: true, wtimeout: 5000 } }
);
```

### Read Concerns (`level`)
Regulates what data is visible to queries.

- **`"local"`** / **`"available"`**: Default. Returns the node's current local state. Data can be rolled back if the primary fails before replica synchronization.
- **`"majority"`**: Returns data acknowledged by a majority of nodes. Guaranteed never to be rolled back.
- **`"snapshot"`**: Returns a consistent snapshot of the data. Only available inside multi-document transactions.
- **`"linearizable"`**: Returns only data acknowledged by a majority of nodes, while verifying that the primary node is still active via concurrent node checks. Prevents reading stale data.

### Read Preferences
Controls which replica set nodes are queried for reads (scales read throughput).

- **`primary`**: Default. All reads go to the primary node.
- **`primaryPreferred`**: Reads primary if online, falls back to secondary replicas if offline.
- **`secondary`**: All reads go to secondary replicas.
- **`secondaryPreferred`**: Reads secondary replicas first, falls back to primary if none are available.
- **`nearest`**: Reads from the node with the lowest network latency (regardless of primary/secondary status).

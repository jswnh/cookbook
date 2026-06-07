# MS SQL Server Programmability Reference

SQL Server utilizes Transact-SQL (T-SQL) to write stored procedures, user-defined functions, triggers, and views.

---

## 1. Stored Procedures

Procedures can accept parameters, return multiple record sets, call transactions, and run administrative tasks.

### Creation and Call
```sql
CREATE OR ALTER PROCEDURE sales.GetCustomerBalances
    @MinBalance DECIMAL(10,2),
    @ActiveOnly BIT = 1, -- Default parameter
    @TotalCustomers INT OUTPUT -- Output parameter
AS
BEGIN
    SET NOCOUNT ON; -- Prevents returning "X rows affected" messages (optimizes network traffic)
    
    -- Query records
    SELECT id, name, balance 
    FROM sales.accounts 
    WHERE balance >= @MinBalance 
      AND (@ActiveOnly = 0 OR is_active = @ActiveOnly);
      
    -- Set output parameter value
    SELECT @TotalCustomers = @@ROWCOUNT;
END;
GO

-- Calling the stored procedure
DECLARE @Count INT;
EXEC sales.GetCustomerBalances @MinBalance = 500.00, @TotalCustomers = @Count OUTPUT;
SELECT @Count AS Matches;
```

---

## 2. User-Defined Functions (UDF)

Functions return values and can be embedded directly inside SELECT queries. In SQL Server, there are three types of UDFs:

### 1. Scalar Functions
Returns a single value.
```sql
CREATE OR ALTER FUNCTION dbo.fn_CalculateTax (@Amount DECIMAL(10,2))
RETURNS DECIMAL(10,2)
AS
BEGIN
    RETURN @Amount * 0.08;
END;
GO
```

### 2. Inline Table-Valued Functions (iTVF)
Returns a virtual table using a single `SELECT` block. Highly performant because the optimizer treats it like a View and compiles it inline with the outer query.
```sql
CREATE OR ALTER FUNCTION sales.fn_GetProductsByPrice (@MaxPrice DECIMAL(10,2))
RETURNS TABLE
AS
RETURN (
    SELECT product_id, name, price 
    FROM sales.products 
    WHERE price <= @MaxPrice
);
GO
```

### 3. Multi-Statement Table-Valued Functions (mSTVF)
Builds the output table procedurally within a `BEGIN...END` block.
- *Performance warning*: SQL Server treats mSTVFs as black boxes, making cardinality estimates difficult. Use Inline TVFs instead whenever possible.

```sql
CREATE OR ALTER FUNCTION sales.fn_GetCustomReport ()
RETURNS @ReportTable TABLE (id INT, description NVARCHAR(50))
AS
BEGIN
    INSERT INTO @ReportTable VALUES (1, 'Summary report');
    INSERT INTO @ReportTable VALUES (2, 'Detailed report');
    RETURN;
END;
GO
```

---

## 3. Triggers

Triggers execute in response to table events. They reference two virtual tables:
- **`inserted`**: Houses new/modified records.
- **`deleted`**: Houses deleted or original pre-modified records.

### AFTER Trigger
Executes after the database engine completes constraints validation and data modification.

```sql
CREATE OR ALTER TRIGGER sales.trg_AfterOrderUpdate
ON sales.orders
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;
    
    -- Only write audit log if status changed
    IF UPDATE(status) -- Evaluates true if status column was modified
    BEGIN
        INSERT INTO sales.order_history (order_id, old_status, new_status)
        SELECT i.order_id, d.status, i.status
        FROM inserted i
        INNER JOIN deleted d ON i.order_id = d.order_id;
    END;
END;
GO
```

### INSTEAD OF Trigger
Fires *instead* of the triggering action. Widely used to redirect insertions on complex non-updatable Views to their underlying target tables.

```sql
CREATE OR ALTER TRIGGER sales.trg_InsteadOfCustomerDelete
ON sales.customers
INSTEAD OF DELETE
AS
BEGIN
    -- Perform soft deletion instead of hard delete
    UPDATE c
    SET c.is_active = 0
    FROM sales.customers c
    INNER JOIN deleted d ON c.id = d.id;
END;
GO
```

---

## 4. T-SQL Control Flow

T-SQL loops are built using `WHILE`. There are no standard `FOR` loops in T-SQL.

```sql
-- 1. Conditional Branch
IF @Value > 100
    BEGIN
        PRINT 'Value is large';
    END
ELSE
    BEGIN
        PRINT 'Value is small';
    END;

-- 2. Loop
DECLARE @Counter INT = 1;
WHILE @Counter <= 5
BEGIN
    PRINT @Counter;
    SET @Counter = @Counter + 1;
    
    IF @Counter = 3
        CONTINUE; -- skip rest of loop
    IF @Counter > 4
        BREAK; -- break loop
END;
```

---

## 5. Views & Schema Binding

### Schema Binding (`WITH SCHEMABINDING`)
Prevents users from modifying the column types or dropping tables referenced by the View. It is required if you want to create an index on the View (Indexed/Materialized View).

```sql
CREATE OR ALTER VIEW sales.vw_ActiveOrders
WITH SCHEMABINDING -- Bind columns to schema
AS
SELECT order_id, customer_id, order_date
FROM sales.orders
WHERE status = 'Pending';
GO
```

---

## 6. Structured Error Handling

Wrap operations in a `TRY...CATCH` block. Raise custom exceptions using `THROW`.

```sql
BEGIN TRY
    -- Execute statements
    INSERT INTO sales.customers (name) VALUES (NULL);
END TRY
BEGIN CATCH
    -- Print details
    PRINT 'Error Number: ' + CAST(ERROR_NUMBER() AS VARCHAR(10));
    PRINT 'Error Message: ' + ERROR_MESSAGE();
    
    -- Throw error back to client
    THROW 50000, 'Customer insertion failed.', 1;
END CATCH;
```
- **`THROW`**: Standard exception raising. The format is: `THROW [error_number], [message], [state]`. The custom error number must be between 50000 and 2147483647.

# Foreign Keys

Foreign keys are constraints that enforce referential integrity between tables. They ensure that values in one table must exist in another table.

## Creating Foreign Keys

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Inline Definition

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT REFERENCES users(id)
);
```

## Named Foreign Key

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    CONSTRAINT fk_user
        FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

## Adding Foreign Key to Existing Table

```sql
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);
```

## Foreign Key Actions

### ON DELETE

```sql
-- CASCADE: Delete related rows
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE

-- SET NULL: Set foreign key to NULL
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL

-- RESTRICT: Prevent deletion (default)
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT

-- NO ACTION: Similar to RESTRICT
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE NO ACTION
```

### ON UPDATE

```sql
-- CASCADE: Update foreign key when primary key changes
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE CASCADE

-- SET NULL: Set to NULL when primary key changes
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE SET NULL

-- RESTRICT: Prevent update (default)
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE RESTRICT
```

## Composite Foreign Keys

```sql
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```

## Self-Referencing Foreign Keys

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(id)
);
```

## Dropping Foreign Keys

```sql
ALTER TABLE orders
DROP CONSTRAINT fk_user;
```

## Benefits

1. **Referential Integrity**: Ensures data consistency
2. **Data Quality**: Prevents orphaned records
3. **Cascading Actions**: Automatic cleanup of related data
4. **Documentation**: Makes relationships explicit

## Considerations

- Foreign keys add overhead to INSERT/UPDATE/DELETE operations
- Can impact performance on large tables
- Require indexes on foreign key columns (usually automatic)
- Can complicate data migration and cleanup

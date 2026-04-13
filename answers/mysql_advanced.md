# Advanced MySQL Concepts

## 1. Storage Engines: InnoDB vs MyISAM
- **InnoDB**:
    - Supports **Transactions** (ACID compliant).
    - **Row-level locking** (better for concurrent writes).
    - Supports **Foreign Keys**.
    - Data is stored in a clustered index (B+Tree).
    - Better crash recovery.
- **MyISAM**:
    - No transaction support.
    - **Table-level locking** (slow for concurrent writes).
    - No foreign keys.
    - Can be faster for read-heavy workloads with very few writes.
    - Supports Full-text searching (InnoDB also supports it since 5.6).

## 2. Indices Types
- **B-Tree Index**: The most common type (specifically B+Trees in InnoDB).
    - **Structure**: They are "horizontally large" (high fan-out) and shallow. Each node can contain hundreds of child pointers, which minimizes disk I/O by keeping the tree depth low.
    - **Performance**: Fast to filter (O(log n)) because the search space is halved (or more) at each level.
    - **Usage**: Good for exact matches (`=`), range searches (`>`, `<`, `BETWEEN`), and sorting (`ORDER BY`).
- **Hash Index**: Very fast for exact matches, but can't be used for range searches or sorting.
- **Full-text Index**: Used for searching text within large columns.
- **Spatial Index**: Used for geographical data (R-trees).

## 3. ACID Properties
- **Atomicity**: "All or nothing". Ensures that all operations within a transaction are completed; if any operation fails, the entire transaction is rolled back to its previous state.
- **Consistency**: Data must follow all database rules (constraints, triggers, etc.). Ensures that a transaction transforms the database from one valid state to another.
- **Isolation**: Each transaction is independent. Prevents concurrent transactions from interfering with each other, ensuring that the intermediate state of one transaction is invisible to others.
- **Durability**: Committed data is saved permanently. Once a transaction is committed, its changes survive even in the event of a system failure.

## 4. Transaction Control Commands
- `START TRANSACTION` or `BEGIN`: Marks the beginning of a transaction block.
- `COMMIT`: Saves all changes made during the current transaction, making them permanent and visible to other users.
- `ROLLBACK`: Reverts all changes made during the current transaction, returning the database to its state before the transaction began.
- `SAVEPOINT name`: Creates a point within a transaction that can be rolled back to later without affecting the entire transaction.
- `ROLLBACK TO name`: Reverts the transaction to the specified savepoint.
- `RELEASE SAVEPOINT name`: Removes a savepoint from the current transaction.

### Autocommit Mode
By default, MySQL runs with **autocommit** mode enabled. This means that as soon as you execute a statement that updates (modifies) a table, MySQL stores the update on disk to make it permanent. To group multiple statements into a single transaction, you must either:
1. Use `START TRANSACTION;`
2. Set autocommit to off: `SET autocommit = 0;`

## 5. Transaction Isolation Levels
Isolation levels define how transactions interact with each other and the trade-off between consistency and performance.

- **Read Uncommitted**: The lowest isolation level. A transaction can read data that has been modified by another transaction but not yet committed. This leads to **Dirty Reads**.
- **Read Committed**: Ensures that a transaction can only read data that has been committed. This prevents dirty reads, but still allows **Non-Repeatable Reads** (if data changes between two reads in the same transaction).
- **Repeatable Read**: Guarantees that if a transaction reads a row once, it will see the same data if it reads it again, even if another transaction commits changes in between. This is the **default for InnoDB**. It prevents dirty and non-repeatable reads.
- **Serializable**: The highest isolation level. It forces transactions to run in a way that is equivalent to serial (one after another) execution. It prevents all the above issues, including **Phantom Reads**, but significantly impacts performance due to heavy locking.

For a deeper understanding of isolation levels, check this article: [Understanding Database Isolation Levels](https://hosseinnejati.medium.com/understanding-database-isolation-levels-from-my-perspective-27f261eeb976)

### 5.1. Practical Examples of Isolation Levels

#### 1. Dirty Read (Read Uncommitted)
*   **Scenario**: Transaction A updates a user's balance from $100 to $200. Transaction B reads the balance as $200. Transaction A then encounters an error and rolls back the change.
*   **Problem**: Transaction B now has "dirty" data ($200) that was never actually committed to the database.
*   **Solution**: Use **Read Committed** or higher.

#### 2. Non-Repeatable Read (Read Committed)
*   **Scenario**: Transaction A reads a product's price as $50. Transaction B then updates the price to $60 and commits. Transaction A reads the same product's price again and sees $60.
*   **Problem**: Within the same transaction, two identical queries returned different results.
*   **Solution**: Use **Repeatable Read** or higher.

#### 3. Phantom Read (Repeatable Read)
*   **Scenario**: Transaction A queries all active users (finds 10). Transaction B inserts a new active user and commits. Transaction A queries active users again and finds 11.
*   **Problem**: A "phantom" row appeared that wasn't there during the first read.
*   **Solution**: Use **Serializable** (or rely on InnoDB's Next-Key Locking in Repeatable Read which handles many phantom read cases).

#### 4. When to use each?
*   **Read Committed**: The most common choice for most applications (default in PostgreSQL/Oracle). It offers a good balance between performance and consistency by preventing dirty reads.
*   **Repeatable Read**: The default for MySQL (InnoDB). Best when you need a consistent view of the data throughout a transaction (e.g., generating a complex report).
*   **Serializable**: Use only when absolute data integrity is required and concurrent access to the same rows is low (e.g., critical financial transfers).

### 5.2. Detailed Isolation Level Scenarios

These examples assume a table `accounts` with `id` and `balance`.

#### 1. Dirty Read (Read Uncommitted)
This occurs when Transaction 2 reads data that Transaction 1 has changed but not yet committed.

| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;` | `SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;` |
| 2 | `START TRANSACTION;` | `START TRANSACTION;` |
| 3 | `UPDATE accounts SET balance = 200 WHERE id = 1;` | |
| 4 | | `SELECT balance FROM accounts WHERE id = 1;` -- **Result: 200** |
| 5 | `ROLLBACK;` | |
| 6 | | `SELECT balance FROM accounts WHERE id = 1;` -- **Result: 100** |

**Analysis**: T2 made decisions based on $200, but that value "never existed" because T1 rolled back.

#### 2. Non-Repeatable Read (Read Committed)
Transaction 1 reads the same row twice and gets different results because Transaction 2 committed a change in between.

| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;` | `SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;` |
| 2 | `START TRANSACTION;` | `START TRANSACTION;` |
| 3 | `SELECT balance FROM accounts WHERE id = 1;` -- **Result: 100** | |
| 4 | | `UPDATE accounts SET balance = 150 WHERE id = 1;` |
| 5 | | `COMMIT;` |
| 6 | `SELECT balance FROM accounts WHERE id = 1;` -- **Result: 150** | |

**Analysis**: Even though T1 is still in the same transaction, the data changed. This breaks consistency if T1 was performing a multi-step calculation.

#### 3. Phantom Read (Repeatable Read)
Transaction 1 performs a range query twice and gets a different number of rows because Transaction 2 inserted/deleted a row.

| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;` | `SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;` |
| 2 | `START TRANSACTION;` | `START TRANSACTION;` |
| 3 | `SELECT COUNT(*) FROM accounts WHERE balance > 100;` -- **Result: 5** | |
| 4 | | `INSERT INTO accounts (id, balance) VALUES (99, 500);` |
| 5 | | `COMMIT;` |
| 6 | `SELECT COUNT(*) FROM accounts WHERE balance > 100;` -- **Result: 5** | |

**Analysis**: In MySQL InnoDB (Repeatable Read), the result is still 5 due to **MVCC (Multi-Version Concurrency Control)**. However, if T1 tried to `UPDATE accounts SET balance = balance WHERE balance > 100`, it might "discover" the new row. To fully prevent this, **Serializable** or explicit locking is needed.

---

## 6. Locking and Concurrency
- **Pessimistic Locking**: Assumes a conflict will occur and locks the row/table before modifying it.
  - **`SELECT ... FOR UPDATE` (Exclusive Lock / X-Lock)**: Locks the selected rows. Other transactions cannot modify them OR acquire any lock (Shared or Exclusive) on them until T1 finishes.
  - **`SELECT ... LOCK IN SHARE MODE` (Shared Lock / S-Lock)**: Other transactions can read the row and acquire their own Shared Locks, but NO transaction can modify the row until all Shared Locks are released.
  - **Use Case**: `FOR UPDATE` is for "I will definitely change this". `LOCK IN SHARE MODE` is for "I need to ensure this doesn't change while I read related data".

### 6.1. Locking Scenarios

#### 1. Exclusive Lock Conflict (`FOR UPDATE`)
| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `BEGIN;` | `BEGIN;` |
| 2 | `SELECT * FROM accounts WHERE id = 1 FOR UPDATE;` | |
| 3 | | `SELECT * FROM accounts WHERE id = 1 FOR UPDATE;` -- **BLOCKED** |
| 4 | `UPDATE accounts SET balance = 300 WHERE id = 1;` | |
| 5 | `COMMIT;` | |
| 6 | | T2 now receives the result and can proceed. |

#### 2. Shared Lock vs Update
| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `BEGIN;` | `BEGIN;` |
| 2 | `SELECT * FROM accounts WHERE id = 1 LOCK IN SHARE MODE;` | |
| 3 | | `UPDATE accounts SET balance = 400 WHERE id = 1;` -- **BLOCKED** |
| 4 | `COMMIT;` | |
| 5 | | T2 now proceeds with the update. |

#### 3. Deadlock Scenario
| Step | Transaction 1 (T1) | Transaction 2 (T2) |
| :--- | :--- | :--- |
| 1 | `START TRANSACTION;` | `START TRANSACTION;` |
| 2 | `UPDATE accounts SET balance = 10 WHERE id = 1;` | |
| 3 | | `UPDATE accounts SET balance = 20 WHERE id = 2;` |
| 4 | `UPDATE accounts SET balance = 30 WHERE id = 2;` -- **WAITING** | |
| 5 | | `UPDATE accounts SET balance = 40 WHERE id = 1;` -- **DEADLOCK!** |

**Result**: MySQL detects the cycle (T1 waiting for T2, T2 waiting for T1) and kills one transaction (usually the one that did the least work) with error: `Deadlock found when trying to get lock; try restarting transaction`.

- **Optimistic Locking**: Assumes no conflict; checks if the data has changed since it was read (usually via a version/timestamp column) before updating.
- **Advisory Locks**: Application-defined locks (e.g., `GET_LOCK('my_lock_name', 10)`) that don't lock database rows but provide a way to synchronize logic across different processes.
- **Deadlock**: A situation where two or more transactions are waiting for each other to release locks, causing a permanent block. MySQL automatically detects deadlocks and rolls back one of the transactions to break the cycle.

## 7. Practical Database Management
- **Selectivity**: The ratio of unique values in a column to the total number of records. Higher selectivity means an index on that column will be more effective.
- **ANALYZE vs EXPLAIN**:
  - `EXPLAIN`: Shows the execution plan *without* actually running the query.
  - `ANALYZE TABLE`: Updates statistics for the table to help the optimizer make better decisions. `EXPLAIN ANALYZE` (MySQL 8.0.18+) *runs* the query and provides real-time profiling.
- **CHAR vs VARCHAR**: `CHAR` is fixed-length (padded with spaces), while `VARCHAR` is variable-length. `CHAR` is slightly faster for very short, uniform data (like country codes), while `VARCHAR` saves space for varying text lengths.
- **ALTER TABLE on Live Tables**: For large tables (millions of rows), `ALTER TABLE` can lock the table and cause downtime. Solutions:
  - **pt-online-schema-change**: A tool from Percona that creates a shadow table, syncs data via triggers, and swaps them.
  - **gh-ost (GitHub Online Schema Migrations)**: Migrates schemas without triggers by tailing the binary log.
  - **Online DDL**: Since MySQL 5.6+, many `ALTER TABLE` operations can be performed without locking the table for writes.
  - **Pagination Optimization**: Avoid `LIMIT 1000000, 10` as MySQL must read and discard 1M rows. Use **Cursor-based pagination** (e.g., `WHERE id > :last_id LIMIT 10`) for O(log n) performance on large datasets.

## 8. Performance Tuning
### CEQS Principles
- **Connection Polling**: Reuse existing database connections, reduce the overhead and point of failure because opened connections.
- **EXPLAIN**: Use the EXPLAIN command to see that you did your query right and its time to complete, parameters, joins and indexes where selected right.
- **Query Caching**: Use application-level caching and invalidations to not bother your table for known results (long time ago written articles that would not be changes, products categories and tags, etc.)
- **Slow Query Log**: Use this feature and add some observability tool to it to identify the queries that took too long to complete. Optimize those queries and add necessary indexes if needed, or use FORCE INDEX and ANALYZE TABLE if the optimizer didn't see your indexes and went into full-text search mode.

## 9. MariaDB vs MySQL
MariaDB is a fork of MySQL created by the original developers in 2009 after Oracle's acquisition of MySQL.

- **Open Source Philosophy**: MariaDB is fully open-source (GPL) and promises to stay that way. MySQL has a dual-licensing model: a Community (GPL) version and a commercial Enterprise edition.
- **Performance**: MariaDB often introduces performance optimizations earlier, such as improved thread pooling (in the community version) and advanced query optimization.
- **Storage Engines**: MariaDB supports more storage engines out of the box (e.g., Aria, MyRocks, ColumnStore, Cassandra).
- **Features**: MariaDB has added features like Window Functions (before MySQL), Dynamic Columns, and Roles earlier than MySQL.
- **Compatibility**: MariaDB is designed to be a "drop-in replacement" for MySQL. For example, the `mysql` command works with MariaDB, and data files are often compatible between versions (though this has become harder with MySQL 8.0 and MariaDB 10.x).

## 10. MySQL vs PostgreSQL
MySQL is a Relational Database Management System (RDBMS), while PostgreSQL is an Object-Relational Database Management System (ORDBMS).

- **SQL Compliance**: PostgreSQL strictly adheres to SQL standards. MySQL has historically been less strict, though newer versions (8.0+) have significantly improved compliance.
- **Complexity**: PostgreSQL is better for complex queries, advanced indexing (GIN, GiST, BRIN), and massive datasets. MySQL is often favored for simpler, read-heavy web applications due to its historical speed and ease of setup.
- **Concurrency**: PostgreSQL uses Multi-Version Concurrency Control (MVCC) which allows for better write performance in some scenarios as readers don't block writers and vice-versa. MySQL's InnoDB also uses MVCC but behaves differently in some high-concurrency cases.
- **Data Types**: PostgreSQL supports a wider range of advanced data types natively, including JSONB (indexed JSON), Arrays, Geometric types, and custom types.
- **Extensibility**: PostgreSQL is highly extensible, allowing developers to create custom functions, types, and even index types without modifying the core.

## 11. Composite Index Strategy
When filtering by multiple columns (e.g., `sku`, `creation_date`, and a low-cardinality `status` field), a **composite index** (an index on multiple columns) is usually the most performant choice.

### Why a Composite Index is better than Separate Indexes:
1.  **One Index per Query**: By default, MySQL typically uses only one index per table for a query. If you have separate indexes on `sku`, `creation_date`, and `status`, MySQL must choose the most "selective" one, filter the results, and then scan those results for the other two conditions.
2.  **Index Merge Overhead**: While MySQL can sometimes use "Index Merge" to combine multiple indexes, this is significantly less efficient than a single composite index scan, as it involves intersecting or unioning sets of row pointers.
3.  **Left-to-Right Prefix Rule**: A composite index on `(sku, creation_date, status)` can also be used for queries that filter only by `sku` or by `sku` and `creation_date`. It cannot, however, be used for queries filtering only by `status`.
4.  **Selectivity and Order**:
    *   **High Selectivity First**: Usually, you put the most selective column (like `sku`) first. If a column is unique, the index becomes extremely fast for point lookups.
    *   **Equality before Range**: Columns used for exact matches (`=`) should come before columns used for range filters (`>`, `BETWEEN`, `LIKE 'abc%'`).
    *   **High Selectivity vs Low Cardinality for Equality**: Among columns used for equality, put the one with the highest selectivity first.
    *   **Example**: For a filter with `sku` (unique), `status_field` (values 1-15), and `creation_date` (range), the ideal order is `(sku, status_field, creation_date)`. Since `sku` is unique, it will immediately narrow the search to a single row. If `sku` was not unique but still high-cardinality, it should still come before `status_field`.
5.  **Covering Index**: If your `SELECT` statement only asks for these three columns (or a subset of them), MySQL can return the data directly from the index B-tree without ever touching the actual table rows (data pages). This is called a "Covering Index" and it's extremely fast.

### Why it's performant even if only `creation_date` is used:
Even if the query doesn't filter by the leading column of the composite index, it can still be performant for two main reasons:

1.  **Index Skip Scan (MySQL 8.0.13+)**: If the leading column (e.g., the `status_field` with values 1-15) has **low cardinality**, MySQL can "skip" through the index. It essentially performs a range scan for each distinct value of the prefix column. If we order our index as `(status_field, creation_date, sku)`, a query filtering only by `creation_date` will be very fast because MySQL only needs to perform 15 sub-scans.
2.  **Full Index Scan vs. Full Table Scan**: If the query is "covered" by the index (meaning all selected columns are in the index), MySQL can scan the entire index instead of the table. Since the index is much smaller and more likely to be cached in memory (the `innodb_buffer_pool`), a full scan of the index is significantly faster than a full scan of the entire table data.

### Comparison with No Index:
- **No Index**: Requires a **Full Table Scan**, reading every single row from disk. For a table with millions of rows, this is catastrophically slow.
- **Separate Indexes**: Better than no index, but still requires reading more data from the table than necessary.
- **Composite Index**: Minimizes I/O by narrowing down the result set to the exact rows needed within the index structure itself.

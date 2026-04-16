# EXPLAIN in MySQL

The `EXPLAIN` statement provides information about how MySQL executes a query. It works with `SELECT`, `DELETE`, `INSERT`, `REPLACE`, and `UPDATE` statements. Understanding its output is crucial for database optimization.

## Key Columns in EXPLAIN Output

| Column            | Description                                                                                            |
| :---------------- | :----------------------------------------------------------------------------------------------------- |
| **id**            | Sequential identifier for each `SELECT` within the query. Helps track the order of execution.          |
| **select_type**   | The type of `SELECT` (e.g., SIMPLE, PRIMARY, SUBQUERY, DERIVED, UNION).                                |
| **table**         | The name of the table the row refers to. Can also be a derived table (e.g., `<derivedN>`).             |
| **partitions**    | The partitions from which records would be matched.                                                    |
| **type**          | The join type (how MySQL joins tables). **Crucial for performance.**                                   |
| **possible_keys** | Indexes that MySQL _could_ choose from to find rows.                                                   |
| **key**           | The index that MySQL _actually_ decided to use.                                                        |
| **key_len**       | The length of the chosen key in bytes. Useful for checking if all parts of a composite index are used. |
| **ref**           | Which columns or constants are compared to the index named in the `key` column.                        |
| **rows**          | Estimated number of rows MySQL believes it must examine.                                               |
| **filtered**      | Estimated percentage of rows filtered by the table condition.                                          |
| **Extra**         | Additional information about how MySQL resolves the query.                                             |

## Detailed Column Explanations

### 1. id and select_type

- **SIMPLE**: A simple query without subqueries or unions.
- **PRIMARY**: The outermost `SELECT` in a complex query.
- **SUBQUERY**: A `SELECT` in a subquery (not in the `FROM` clause).
- **DERIVED**: A subquery in the `FROM` clause (a "derived table").
- **UNION**: The second or subsequent `SELECT` in a `UNION`.
- **DEPENDENT SUBQUERY**: A subquery that depends on the outer query (often slow).
- **UNCACHEABLE SUBQUERY**: A subquery whose result cannot be cached (e.g., uses `RAND()`).

### 2. Join Types (the `type` column)

Ordered from **best to worst** performance:

1.  **system**: The table has only one row (special case of `const`).
2.  **const**: The table has at most one matching row (e.g., primary key or unique index lookup with constants).
3.  **eq_ref**: One row is read from this table for each combination of rows from the previous tables (using a unique index).
4.  **ref**: All rows with matching index values are read (using a non-unique index or prefix).
5.  **fulltext**: The join is performed using a `FULLTEXT` index.
6.  **ref_or_null**: Like `ref`, but also searches for `NULL` values.
7.  **index_merge**: Multiple indexes are used and their results merged.
8.  **unique_subquery**: For `IN` subqueries that return unique results.
9.  **range**: Only rows in a given range are retrieved using an index (e.g., using `>`, `<`, `BETWEEN`, `IN`).
10. **index**: Full index scan (better than `ALL` because the index is usually smaller than the table).
11. **ALL**: Full table scan (worst performance).

### 3. Important "Extra" Values

- **Using index**: The query is resolved using only the index (Covering Index). No need to read the actual table rows.
- **Using where**: A `WHERE` clause is used to filter rows _after_ fetching them from the storage engine.
- **Using temporary**: MySQL needs to create an internal temporary table to hold the result (common with `GROUP BY` or `ORDER BY` on non-indexed columns). **Warning sign!**
- **Using filesort**: MySQL must do an extra pass to find out how to retrieve the rows in sorted order. **Warning sign!**
- **Select tables optimized away**: The optimizer determined that no tables need to be accessed (e.g., `MIN()` on an indexed column).
- **FirstMatch(table_name)**: A semi-join optimization that stops searching as soon as the first match is found in a subquery.

## Modern MySQL Features (8.0+)

### EXPLAIN FORMAT=TREE

Provides a hierarchical, tree-like view of the execution plan. It shows the cost of each operation and the estimated number of rows.

```sql
EXPLAIN FORMAT=TREE SELECT ...
```

### EXPLAIN ANALYZE

The most powerful tool for profiling. It **actually executes** the query and provides:

- **Actual time** to get the first row and all rows.
- **Actual number of rows** returned.
- **Number of loops** (how many times the operation was executed).

```sql
EXPLAIN ANALYZE SELECT ...
```

## How to Read EXPLAIN

1.  **Read from top to bottom**: MySQL executes the plan in the order shown.
2.  **Check `type`**: Aim for `const`, `eq_ref`, or `ref`. If you see `ALL` or `index`, consider adding indexes.
3.  **Check `rows` and `filtered`**: Multiply `rows * filtered` to see how many rows are passed to the next step.
4.  **Watch for `Extra` warnings**: `Using temporary` and `Using filesort` usually mean you need better indexing for `GROUP BY` or `ORDER BY`.
5.  **Use `SHOW WARNINGS`**: After running `EXPLAIN`, run `SHOW WARNINGS` to see how MySQL rewritten your query.

---

_Source: [Habr - Читаем EXPLAIN на максималках](https://habr.com/ru/companies/citymobil/articles/545004/)_

# PostgreSQL Table Relations

PostgreSQL supports standard SQL joins and set operations.

## 1. Joins

### INNER JOIN
Returns rows when there is a match in both tables.

### LEFT [OUTER] JOIN
Returns all rows from the left table, and the matched rows from the right table.

### RIGHT [OUTER] JOIN
Returns all rows from the right table, and the matched rows from the left table.

### FULL [OUTER] JOIN
Returns all rows when there is a match in either left or right table.

### CROSS JOIN
Returns the Cartesian product of the two tables.

## 2. Set Operations

### UNION
Combines the result of two or more SELECT statements (removes duplicates). Use `UNION ALL` to keep duplicates.

### INTERSECT
Returns only the rows that are present in both result sets.

### EXCEPT
Returns rows from the first result set that are not present in the second.

---
*Back to [PostgreSQL](../questions/09-postgresql.mdx)*

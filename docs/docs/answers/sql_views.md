# SQL Views and Materialized Views

SQL Views and Materialized Views are database objects used to simplify data access, improve security, and optimize performance.

---

## 1. Standard SQL Views (Virtual Tables)

A **View** is a virtual table based on the result-set of an SQL statement. It does not store data physically; instead, it stores the query definition and executes it whenever the view is queried.

### Purpose and Importance
- **Simplification:** Hides complex joins and calculations from the end user.
- **Security:** Restricts access to specific rows or columns (e.g., hiding a `salary` column).
- **Consistency & Abstraction:** Provides a unified interface even if the underlying table structure changes. This is crucial for **legacy systems** where you can rename columns or tables without breaking the application.
- **Data Integrity:** Using `WITH CHECK OPTION` ensures that data modified through the view stays within the view's scope.
- **Logic Centralization:** Encourages "Don't Repeat Yourself" (DRY) by centralizing complex business logic in one place.

### How to use Views

#### Creating a View
```sql
-- Simple View (from one table)
CREATE VIEW active_users AS
SELECT id, username, email
FROM users
WHERE status = 'active';

-- Complex View (from multiple tables)
CREATE VIEW order_details AS
SELECT o.id, u.username, p.product_name, o.amount
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN products p ON o.product_id = p.id;
```

#### Managing Views
```sql
-- Listing all views (MySQL example)
SHOW FULL TABLES WHERE table_type LIKE '%VIEW';

-- Listing views using information_schema (Standard SQL)
SELECT table_name 
FROM information_schema.views 
WHERE table_schema = 'your_db_name';

-- Deleting a view
DROP VIEW active_users;

-- Updating a view definition
CREATE OR REPLACE VIEW active_users AS
SELECT id, username, email, last_login
FROM users
WHERE status = 'active';
```

### Updating Data through Views
Not all views are updatable. To update data through a view, it must satisfy several rules:
1. The `SELECT` statement must not contain `GROUP BY`, `DISTINCT`, or aggregate functions (`SUM`, `COUNT`).
2. The view must be created from a **single** base table (most RDBMS).
3. The view must include all `NOT NULL` columns from the base table if you want to perform `INSERT`.

**`WITH CHECK OPTION`:**
This clause prevents you from performing `UPDATE` or `INSERT` that would make the row disappear from the view.
```sql
CREATE VIEW high_salary_employees AS
SELECT * FROM employees WHERE salary > 5000
WITH CHECK OPTION;

-- This will FAIL because it violates the WHERE condition of the view
UPDATE high_salary_employees SET salary = 4000 WHERE id = 1;
```

### Upsides and Downsides
- **Pros:** Real-time data freshness, zero storage overhead, enhanced security.
- **Cons:** Performance hit for complex queries (it runs the underlying query every time).

---

## 2. Materialized Views (Cached Tables)

A **Materialized View** is a database object that contains the *actual results* of a query, stored on disk like a physical table.

### Key Differences:
| Feature | Standard View | Materialized View |
| :--- | :--- | :--- |
| **Storage** | Virtual (only query definition) | Physical (cached data) |
| **Performance** | Depends on the query complexity | Extremely fast (like a table) |
| **Data Freshness** | Real-time (always current) | Periodic (updated via refresh) |
| **Indexes** | Limited (uses base table indexes) | Supports custom indexes on any column |

### How to use Materialized Views

#### PostgreSQL Syntax (9.3+)
```sql
-- Create and populate
CREATE MATERIALIZED VIEW sales_summary AS
SELECT product_id, SUM(quantity) as total_sold
FROM sales
GROUP BY product_id;

-- Refresh manually
REFRESH MATERIALIZED VIEW sales_summary;

-- Refresh concurrently (requires a unique index)
CREATE UNIQUE INDEX idx_product_id ON sales_summary(product_id);
REFRESH MATERIALIZED VIEW CONCURRENTLY sales_summary;
```

#### MySQL Workaround
Since MySQL lacks native materialized views, developers often use:
1. **Summary Tables:** A regular table populated via `INSERT INTO ... SELECT`.
2. **Triggers:** To update the summary table whenever the base table changes.
3. **Scheduled Tasks:** Refreshing the table via Cron or Event Scheduler.

### Upsides and Downsides
- **Pros:** Dramatic performance gain for heavy aggregations, reduced load on base tables.
- **Cons:** Data staleness (stale until refresh), storage overhead, maintenance complexity.

---

## FAQ based on the articles

### When should I use a View vs a Materialized View?
Use a **View** for security (hiding columns) or simplification of small/medium queries where real-time data is critical. Use a **Materialized View** for complex analytics, reporting, or aggregations on large datasets where performance is more important than 100% data freshness.

### Can I insert data into a view that joins multiple tables?
Generally, no. Most databases only allow updates on "simple" views that map to a single base table.

### Is a View faster than a regular query?
No. A view *is* the regular query. However, a **Materialized View** is much faster because it avoids re-calculating the result set.

---

### Sources:
- [SQL Views - GeeksforGeeks](https://www.geeksforgeeks.org/sql/sql-views/)
- [Importance of Views - Reddit](https://www.reddit.com/r/SQL/comments/10mof83/can_somebody_explain_the_importance_of_views/)
- [Getting Started with SQL Views - Medium](https://medium.com/learning-sql/getting-started-with-sql-views-20070b78ab5)
- [SQL Materialized View - DataCamp](https://www.datacamp.com/tutorial/sql-materialized-view)
- [Materialized View - Wikipedia](https://en.wikipedia.org/wiki/Materialized_view)
- [Views vs Materialized Views - Medium](https://medium.com/@artemkhrenov/database-views-and-materialized-views-the-smart-way-to-simplify-complex-queries-e674af9f2e5d)

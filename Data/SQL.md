# SQL

- SQL: Structured Query Language

Review focus
1. Universal SQL: JOIN, GROUP BY, HAVING, CASE, NULL handling, CTEs, window functions, anti-joins, deduplication, date logic.
2. Performance: indexes, query plans, sargability, cardinality, avoiding row-by-row logic, transaction isolation basics.
3. PostgreSQL-specific: DISTINCT ON, generate_series, arrays/JSONB, EXPLAIN ANALYZE, ILIKE, RETURNING.
4. T-SQL-specific: TOP, CROSS APPLY, OUTER APPLY, temp tables, table variables, stored procedures, MERGE caveats, TRY_CONVERT, GETDATE, DATEADD, DATEDIFF.

## Universal SQL

### CASE

Use CASE when you need conditional logic inside SQL.

#### Key Patterns

```SQL
-- Basic
CASE WHEN condition THEN value ELSE fallback END

-- Aggregation
SUM(CASE WHEN condition THEN 1 ELSE 0 END)

-- Sort
ORDER BY CASE WHEN condition THEN sort_value ELSE 0 END
```

#### Details

- Search `CASE`: most commonly used --> `PostgreSQL` evaluates from top to bottom, return *first* matching result
  ```SQL
  CASE
    WHEN condition_1 THEN result_1
    WHEN condition_2 THEN result_2
    ELSE default_result
  END
  ```
  - When compare to fixed values --> can "simplify" to: `WHEN value_1 THEN result_1`
- `CASE` can be used in `ORDER BY` and `GROUP BY` just like normal column
- `CASE` can be used in aggregation (`FILTER` is clearer, but PostgreSQL specific)
  ```SQL
  SELECT
    COUNT(*) AS total_orders,
    SUM(CASE WHEN status = 'completed' THEN 1 ELSE 0 END) AS completed_orders,
    COUNT(*) FILTER (WHERE status = 'cancelled') AS cancelled_orders
  FROM orders;
  ```

#### Common Error
1. `NULL` needs explicit handling: `WHEN col_name IS NULL THEN ...`
2. Result type *inconsistency* may throw error --> All THEN and ELSE branches should return compatible data types
3. Missing `END`
4. Incorrect order of conditions
5. Forgetting `ELSE` --> PostgreSQL will return `NULL`

### NULL handling

1. **NULL** comparison return `UNKNOWN` --> rows with `FALSE` and `UNKNOWN` will be filtered out by `WHERE`
   - Can change the result of `AND/OR/NOT` to `UNKNOWN`
   - Correct way to test for **NULL**: `IS NULL` or `IS NOT NULL`
2. Any arithmetic operation involving **NULL** usually returns `NULL`
   - Use `COALESCE` to provide fallback value: `salary + COALESCE(bonus, 0) AS total_pay` --> `COALESCE` return first non-null value
3. Aggregate functions usually ignore **NULL**
   - Important distinction: `count(*)`: counts rows <> `count(column)`: counts non-NULL values
4. `GROUP BY` treat **NULL** as one group
5. `ORDER BY`: **NULL** sorting varies by db system
6. `JOIN`: similar to `WHERE` --> **NULL** values are not matching
7. `IN` and `NOT IN` --> again, similar to `WHERE` ==> `id NOT IN (1, 2, NULL)` will return no rows
   - Safer version is `NOT EXISTS`
     ```SQL
     SELECT *
     FROM customers c
     WHERE NOT EXISTS (
        SELECT 1
        FROM orders o
        WHERE o.customer_id = c.customer_id
     );
     ```
8. NULL-safe equality: only supported by some db: `a IS NOT DISTINCT FROM b` --> **TRUE** when a=b=NULL

### Common Table Expression (CTE)
- A **CTE**, or **Common Table Expression**, is a temporary named result set (e.g. "table") defined with `WITH`.
  - Useful when a query has multiple logical stages
```SQL
WITH cte_name AS (
    SELECT ...
    FROM ...
)
SELECT ...
FROM cte_name;
```
- One `WITH` block can define multiple CTE --> later CTEs can reference earlier CTEs, but not the other way around
- **CTE** vs. **Subquery** --> Use **CTE* when:
  - Intermediate result has a clear meaning, could be re-used
  - Query has multiple steps (e.g. clean data > filter data > aggregate facts > final reports)
- CTEs and column aliases: can be defined within `SELECT` statement, or after CTE name --> first one is clearer
  ```SQL
  WITH client_spend(client_id, total_spend) AS (
    SELECT
      cust_id,
      SUM(order_amount)
    FROM orders
    GROUP BY cust_id
  )
  ```
- **Recursive CTEs**: supported by some CTEs, for hierarchical data (e.g. parent-child relationship such as folder structure, employee)
  - Structure: 2 parts
    1. Anchor query: returns the starting rows.
    2. Recursive query: repeatedly joins back to the CTE.
    ```SQL
    WITH RECURSIVE employee_tree AS (
        SELECT
            employee_id,
            manager_id,
            employee_name,
            1 AS level
        FROM employees
        WHERE manager_id IS NULL

        UNION ALL

        SELECT
            e.employee_id,
            e.manager_id,
            e.employee_name,
            et.level + 1 AS level
        FROM employees e
        JOIN employee_tree et
            ON e.manager_id = et.employee_id
    )
    SELECT *
    FROM employee_tree;
    ```

### Window functions

### Anti-Joins

### De-duplication

### date logic

## Performance

## PostgreSQL Specific

## T-SQL specific

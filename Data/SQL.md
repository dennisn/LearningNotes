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
- A **window function** calculates a value across a set of rows related to the current row
  - Different to `GROUP BY` --> not collapsing the rows

#### Syntax
```SQL
function_name(...) OVER (
    PARTITION BY ...
    ORDER BY ...
    ROWS BETWEEN ... AND ...
)
```

Main parts:
| Clause           | Purpose                                       |
| ---------------- | --------------------------------------------- |
| `PARTITION BY`   | Splits rows into groups/windows               |
| `ORDER BY`       | Defines ordering inside each window           |
| `ROWS` / `RANGE` | Defines the frame of rows used in calculation |

- `ROWS`: count physical rows
- `RANGE`: uses logical rows based on the `ORDER BY` --> include tied rows ==> multiple orders may have the same "running" total if they have the same "order_date" --> use `ROWS` unless specifically need `RANGE`

#### Functions
- `ROW_NUMBER()`: assigns a unique seuqnece number --> `ROW_NUMBER() OVER (..) AS order_rank`
  - Example: latest order per customer
    ```SQL
    WITH ranked_orders AS (
        SELECT
            customer_id, order_id, order_date,
            ROW_NUMBER() OVER (
                PARTITION BY customer_id
                ORDER BY order_date DESC
            ) AS order_rank
        FROM orders
    )
    SELECT *
    FROM ranked_orders
    WHERE order_rank = 1;
    ```
- `RANK()` and `DENSE_RANK()`: Use first when gaps matter (e.g. 1, 2, 2, 4), while later when you want consecutive rank (e.g. 1, 2, 2, 3)
- `LAG()` --> return previous row by an (optional) offset
  - Use case: revenue change, salary increase
- `LEAD()` --> return future/next row
- Aggregate function: `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()`
- `FIRST_VALUE()`
- `LAST_VALUE()` --> Last of **window** (i.e. often return current row)
  - Safer to do: `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`

#### Use cases
- Running totals: Order table --> for each customer, sort by order, then sum from the first row up to the current row
  ```SQL
  SELECT
      customer_id, order_date, amount,
      SUM(amount) OVER (
          PARTITION BY customer_id
          ORDER BY order_date
          ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
      ) AS running_total
  FROM orders;
  ```
  - Moving average: `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW` --> 6 rows, not necessary 6 calendar days
- Top **N** item: rank then select those with rank <= N
- De-duplication pattern: order by "DESC", then get the first ranked

#### Practical rule
1. Use ROW_NUMBER() for deduplication and latest-row selection.
2. Use RANK() or DENSE_RANK() when ties matter.
3. Use LAG() and LEAD() for comparing adjacent events.
4. Use aggregate window functions when you need group-level values without collapsing rows.
5. Add explicit window frames for running totals and moving averages.
6. Prefer ROWS over RANGE unless you intentionally need tie-aware logical ranges.
7. Always include a deterministic tie-breaker in ORDER BY

### Anti-Joins
- Rows from one table, where **no matching row** exists in another table --> `NOT EXISTS` with sub-query
```SQL
SELECT c.customer_id, c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```
- Alternative: `LEFT JOIN ... IS NULL` --> `IS NULL` check should be on a column in right hand table that is non-null when match exist (e.g. joined key or primary key)
- AVOID careless `NOT IN` --> `NULL` value in comparison set will return no rows

#### Summary
- An anti-join returns rows from the left table that have no matching rows in the right table. 
- The safest universal SQL pattern is usually `NOT EXISTS`, because it handles `NULL`s better than `NOT IN`. 
- Another common implementation is `LEFT JOIN` followed by `WHERE right_table.key IS NULL`. 
  - When using `LEFT JOIN`, right-table filters that define the match should go in the `ON` clause, not the `WHERE` clause.

| Pattern                 | Recommendation                                      |
| ----------------------- | --------------------------------------------------- |
| `NOT EXISTS`            | Best default choice                                 |
| `LEFT JOIN ... IS NULL` | Good and common, especially readable for join logic |
| `NOT IN`                | Use carefully; avoid unless you handle `NULL`s      |
| `EXCEPT` / `MINUS`      | Useful in some databases, but less universal        |

### De-duplication
- Remove "duplicated" rows --> simplest case is `DISTINCT` ==> only remove rows where **all columns** are identical
- **Important**:
  - What is "duplicated" ? --> business logic
  - Which row should survive ?

```SQL
SELECT *
FROM (
    SELECT
        t.*,
        ROW_NUMBER() OVER (
            PARTITION BY duplicate_key
            ORDER BY ranking_column DESC
        ) AS rn
    FROM table_name t
) x
WHERE rn = 1;
```

Common usage
| Rule                  | SQL pattern                     |
| --------------------- | ------------------------------- |
| Keep latest           | `ORDER BY updated_at DESC`      |
| Keep earliest         | `ORDER BY created_at ASC`       |
| Keep highest value    | `ORDER BY amount DESC`          |
| Keep best quality     | `ORDER BY quality_score DESC`   |
| Keep preferred source | `ORDER BY source_priority DESC` |

### Date logic

Distinction of separate date/time concepts
| Type                       | Meaning                                        |
| -------------------------- | ---------------------------------------------- |
| `DATE`                     | Calendar date only, e.g. `2026-07-08`          |
| `TIME`                     | Time only, e.g. `14:30:00`                     |
| `TIMESTAMP` / `DATETIME`   | Date + time, e.g. `2026-07-08 14:30:00`        |
| `TIMESTAMP WITH TIME ZONE` | Date + time with timezone-aware interpretation |

- `CURRENT_DATE` and `CURRENT_TIMESTAMP`: available for all --> preferable
- Filtering by exact date --> with TIMESTAMP column, better of using the "**inclusive start, exclusive end**" pattern
  ```SQL
  WHERE created_at >= start_date 
    AND created_at < end_date
  ```
- Careful with `BETWEEN` --> **inclusive** start & end
  - Work for `DATE` column, exclude most if not all of the last date with `TIMESTAMP` column
- Extracting parts of date
  ```SQL
  EXTRACT(YEAR FROM order_date)
  EXTRACT(MONTH FROM order_date)
  EXTRACT(DAY FROM order_date)
  ```
- Adding/subsctracting date --> slightly different syntax with each system
  ```SQL
  date + interval
  date - interval
  ```
- NOTE: Avoid using function on "indexed" date column --> prevent efficient index usage
  ```SQL
  -- DON'T DO THIS
  WHERE DATE(created_at) = DATE '2026-07-07'
  ```

## Performance

### Checking execution plan

Key skill:
- `EXPLAIN SELECT ...` --> What does the optimizer think it will do?
- `EXPLAIN ANALYZE` --> actually **RUN** the query ==> helps detect bad estimates by the optimizer
  - Safe for `SELECT`, but `INSERT`, `UPDATE` and `DELETE` need to be wrapped in a rollback transaction
- `SET STATISTICS IO ON;` --> SQL server: How much data did SQL Server need to read?
- `SET STATISTICS TIME ON;` --> SQL server: shows CPU time and elapsed time.

### Indexing fundamental

- Indexes help with `WHERE`, `JOIN`, `ORDER BY` and `GROUP BY`
  - Often do not help when you wrap columns in functions
- Composite index --> order matter ==> `Equality columns first, then range/sort columns` (e.g. greater/lesser than, order by, etc.)
- **SARGable** (i.e. Search ARGument ABLE) --> query condition that helps to efficiently use an index to find data
  - Avoid functions on column: `WHERE YEAR(date_col) = 2026` ==> `WHERE date_column >= '2026-01-01' AND date_column < '2027-01-01'`
  - Avoid leading wildcards: `LIKE '%value'` force a full scan --> `LIKE 'value%'` doesn't
  - Avoid negative operator: `NOT`, `!=` or `<>`
  - Avoid calculation in condition: e.g. `WHERE price * 1.1 > 100` ==> `WHERE price > 100 / 1.1`
  - Use `UNION` instead of `OR`
    ```SQL
    -- NOT THIS
    SELECT * FROM Products WHERE Category = ‘A’ OR Category = ‘B’;

    -- DO THIS
    SELECT * FROM Products WHERE Category = ‘A’
    UNION ALL
    SELECT * FROM Products WHERE Category = ‘B’;
    ```

### JOIN performance
- Use indexes on join keys
- JOIN before filtering --> better to filter into Common Table Expression (i.e. `WITH`) then `JOIN`
- Dangerous: *Many-to-Many* join --> can explode row counts

### Misc.
- For existence checks --> prefer `EXIST` over `IN`/`JOIN`
- `UNION` remove duplicates (i.e. extra works) vs. `UNION ALL`

### Summary

When asked “how would you tune this SQL query?”, use this structure:

01. Check the execution plan --> Identify the most expensive operation
03. Check whether filters are **SARGable**
04. Check indexes on:
    - filter columns
    - join keys
    - sort columns
    - group-by columns
05. Filtering (i.e. reduce rows) as early as possible
06. Avoid unnecessary columns
07. Check join cardinality
08. Rewrite subqueries, CTEs, or joins if needed
09. Consider partitioning or clustering for very large tables
10. Measure again after each change

## Query Optimization

### How SQL is logically written vs how the database physically executes it
- SQL is written declaratively --> *what result needed*
  - Db engine decides how to get it physically with optimiser
- Logically Processing order
  ```SQL
  FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
  ```
- Physical execution order
  ```text
  index scan → filter pushdown → join reorder → hash join → aggregate → sort → limit
  ```

### Search operation

| Operation            | Main purpose                              | Often good when                                |
| -------------------- | ----------------------------------------- | ---------------------------------------------- |
| **Table scan**       | Read table rows directly                  | Many rows are needed                           |
| **Index seek**       | Jump to matching index entries            | Filter is selective                            |
| **Index scan**       | Read many/all index entries               | Index is narrower or ordered                   |
| **Nested loop join** | Repeated lookup join                      | Small outer input + indexed inner input        |
| **Hash join**        | Build/probe hash table                    | Large unsorted equality joins                  |
| **Merge join**       | Join sorted inputs                        | Both sides sorted by join key                  |
| **Sort**             | Order rows                                | Required by `ORDER BY`, `GROUP BY`, merge join |
| **Aggregate**        | Summarise rows                            | `COUNT`, `SUM`, `GROUP BY`, etc.               |
| **Spill to disk**    | Temporary disk use due to memory pressure | Usually a warning sign                         |

### Practical rule of thumb

For query tuning, look for:
1. Large scans where a selective index seek would be better.
2. Nested loops over large row counts.
3. Hash joins/aggregates that spill to disk.
4. Sorts over large row counts.
5. Filters applied late when they could safely reduce rows earlier.
6. Bad estimates: estimated rows very different from actual rows.

*A good SQL execution plan is usually not just about avoiding scans. It is about reducing unnecessary work across the whole plan.*



## PostgreSQL Specific

## T-SQL specific

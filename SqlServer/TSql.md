  - T-SQL: transact SQL, evolve from Sybase
# Query Basic
  - Specify object by full name: [InstanceName].[DatabaseName].[Schema].[Object]
    - Except for "Object", other will use default values: `[CurrentInstance].[CurrentDatabase].[dbo]`
	  - "dbo": database-owner
  - Order of processing of "SELECT" query
    ```
	[5] SELECT
	[1] FROM
	[2] WHERE
	[3] GROUP BY
	[4] HAVING
	[6] ORDER BY
	[7] OFFSET - FETCH
	```
  - `TOP` vs. `FETCH NEXT X ROWS`
	+ `TOP`: proprietary syntax, can specify % of total rows, or include all the ties
	+ `FETCH`: conform to Ansi standard, similar to `LIMIT` in other database system\
  - "JOIN": 
    + Start with `CROSS JOIN`: cartesean-product of all rows from the two table
	+ `INNER JOIN`: after "cross join" above, then eliminate "joined" rows which doesn't satisfy the predicate condition
	+ `OUTER JOIN`: after "cross join" and elimination, add all rows from the "reserved" table which haven't got a "match"
  - Three-values language: yes | no | unknown ==> make some operation unsymetric
    - Eg: `x in (A, B, NULL)` vs `x not in (A, B, NULL)`
	  + The first can be true/false/unknown, while the second is always unknown
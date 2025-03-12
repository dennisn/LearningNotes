# Query Basic
  - T-SQL: transact SQL, evolve from Sybase
  - Order of processing of "SELECT" query
    ```
	[5] SELECT
	[1] FROM
	[2] WHERE
	[3] GROUP BY
	[4] HAVING
	[6] ORDER BY
	```
  - `TOP` vs. `FETCH NEXT X ROWS`
	+ `TOP`: proprietary syntax, can specify % of total rows, or include all the ties
	+ `FETCH`: conform to Ansi standard, similar to `LIMIT` in other database system
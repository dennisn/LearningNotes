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
	  
# Data Manipulation
  - Data manipulation language: `INSERT`, `UPDATE`, `DELETE` statements
  - `INSERT` of identity-column: normally not possible
    + Could still do but need to set `IDENTITY_INSERT` to ON
  - `BULK INSERT`: for insert multiple rows of data, normally from external data files
    - Often used with "**bcp**' utility which dump the data from table into external data file
  - `UPDATE`: will set the variable `@@ROWCOUNTS` to number of rows affected by the statement
  - Delete with "**JOIN**":
    ```
	DELETE FROM <Delete-Table>
	FROM <Delete-Table> JOIN <Other-Table> ON <Join-Predicate>
	WHERE <Filter-Predicate>
	```
  - `OUTPUT INSERTED.<Column-Name>`: can be used for INSERT, UPDATE and DELETE to return the values of rows being changed
    - For "DELETE", use `OUTPUT DELETED.<Column-Name>`
  - MERGE: used to synchronise 2 tables
    ```
	MERGE <Target-Table>
	USING <Source-Table>
	ON <Merge-Conditions>
	WHEN MATCHED
		THEN <Update-Statement>
	WHEN NOT MATCHED BY TARGET
		THEN <Insert-Statement>
	WHEN NOT MATCHED BY SOURCE
		THEN <Delete-Statement>
	```
	
## Transaction

Transaction statement keywords: BEGIN, SAVE, ROLLBACK, COMMIT, SET
  - Basic usage:
    ```
	BEGIN TRANSACTION
	<Data-Manipulation-Statements>
	...
	COMMIT TRANSACTION
	```
  - "**NOLOCK**" hint allows "SELECT" statements to do "dirty read" instead of waiting for "COMMIT"
	```
	SELECT * FROM <Table-Name>
	WITH (NOLOCK)
	WHERE ...
	```
  - `ROLLBACK TRAN` can be used to reverse a current transaction, or back to a specific save-point
    - `SAVE TRAN <Save-Point-Name>`: create a save-point with a name that can be refer to in `ROLLBACK TRAN`
  - For distributed transaction, use similar syntax but with "DISTRIBUTED" added

## Stored Procedure
  - Similar to View, but probably best for modify data, with complex logic
    - View can be reference in join, and can be materialized for performance
	- Stored procedure can contain logical statements, with integrated error handling
  - Main benefits:
    - Maintainability: all code in one place
	  + stored procedure can be shared across application
	  + Easier to debug performance issue since we can access execution plan
	- Security: more granular control
	  + allow execution of stored procedure, but not direct access to underlying table
	  + less data transmitted when called from client
	  + Help against "some" of the SQL injection attacks
	- Performance: mostly from execution plan cached and re-used, but is minimal for modern version of db system
  - Disadvantages:
    - Required true T-SQL expertise
	- Need a system approach to manage the creation & usage of stored procedure

### Syntax

  - Basic creation syntax:
	```
	CREATE OR ALTER PROCEDURE <Qualitifed-Name-Of-Store-Procedure>
		<Parameter-Declarations-Comma-Separated>
	AS
	BEGIN
		<SQL-Statements>
	END
	GO
	```
  - Basic execution syntax
    ```
    EXECUTE <Qualitifed-Name-Of-Store-Procedure> <Parameter-Assignment-Comma-Separated>
    GO
    ```
  - Naming convention: maybe "Verb" then "Noun" (e.g. `InsertUser`)
    + put into "Schema" as a way of grouping similar stored procedure
  - "OUTPUT" Parameter: 
    + In declaration: `@EmployeeId int OUTPUT`
    + Usage:
	  ```
	  DECLARE @EmployeeIdOutput int;
	  
	  EXEC <Store-Procedure> @EmployeeId = @EmployeeIdOutput OUTPUT;
	  ```
### Query in stored procedure
  - Temporary table: hold a subset of data during the stored procedure, can add indexes to increase performance
    + For complex procedure, may reduce lock by extract processing data into temp table first
  - Table variables: not suitable for large data sets
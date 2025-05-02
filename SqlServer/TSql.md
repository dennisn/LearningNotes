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
    + similar to set of tuples, where the table type is the tuple definition, and the rows are instances of tuple
	+ table type can't be modified --> each should be for specific purpose, maybe even specific stored procedure, as we will need to drop it & its stored procedure before re-create it with modification
  - Debug flag: good to view temp. table results where the stored procedure create multiple such tables over its lifetime
    + can view the temp. table during the sproc execution
	+ complicate to setup
	
### Optimise Store-Procedures
  - Turn on "`STATS IO`" for stored procedure, especially when coupling with viewing execution plan
    + Ref: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql
  - Temp tables affect performance:
    + stop plan re-used ? --> maybe in older version
	+ add indexes to temp table to increase their performance
  - "Parameter sniffing": First set of parameters may "bias" the execution plan, negatively affect later execution of other parameter sets
  
### Stored procedure template
  - Nice article from SQLShack: https://www.sqlshack.com/how-to-create-and-customize-sql-server-templates/

```
/*
Author: 

Description: <Detail description>

Update: <Name & descriptions of update>
*/

CREATE OR ALTER PROCEDURE <Schema-Name, sysname, Sales>.<Procedure-Name, sysname, Update_TableName>
	<Debug_Parameter, sysname, @Debug> <Date-Type-Debug, , bit> = <Default-Value, , 0>
AS
BEGEIN
	SET NOCOUNT ON;
	
	BEGIN TRY
		BEGIN TRANSACTION;
		
		/* Your main code is here */
		
		COMMIT TRANSACTION;
	END TRY
	
	BEGIN CATCH
		IF (@@TRANCOUNT > 0)
			ROLLBACK TRANSACTION;
			THROW;
	END CATCH
END
```

## RANK and PARTITION
Basic syntax
```
SELECT
  ...
  ROW_NUMBER() OVER (PARTITION BY <Partition-Columns> ORDER BY <Order-Columns) AS XXX
FROM 
```
where `PARTITON BY <Partition-Columns>` is optional, but `ORDER BY` is compulsory

  - ROW_NUMBER(): provide sequence number (when encounter tie, will produce arbitrary number)
  - RANK(): provide rank, where tie rows will have the same rank (e.g. 1, 1, 1, 4, 4, 6, 7, etc.)
  - DENSE_RANK(): similar to RANK(), but ensure the rank is sequential (e.g. 1, 1, 1, 2, 2, 3, 4, etc.)
  - NTILE(x): split the partition into "**x**" group
  - PERCENT_RANK(): rank in 100 percentile (e.g. 0, 0.33, 0.66, 100)
  - FIRST_VALUE(<Some-Function>) and LAST_VALUE(<Some-Function>): have a `RANGE BETWEEN XXX AND YYY`, which impact what values will be used
  
## Access remote table with ROWSET function
  - OPENXML: access an XML data source
  - OPENJSON: access a JSON data source
  - OPENDATASOURCE: access a remote SQL data source
  - OPENQUERY: access a remote SQL data via Linked Server
    - By default, SQL server doesn't allow access to remote server by default, hence need following configure
	  ```
	  sp_configure 'show advanced options', 1
	  reconfigure
      go
	  sp_configure 'Ad Hoc Distributed Queries', 1
	  reconfigure
	  ```
	- Query syntax: `OPENQUERY(<Linked-Server-Name>, <Select-Query-In-Single-Quote>)`
  - OPENROWSET: directly access a remote SQL data source
    - Syntax: 
	  ```
	  OPENROWSET 
	  (<Provider-Name: OLEDB Provider>, 
	  <Data-Source (e.g. Name of the server or IP address)>, 
	  <Select-Query-In-Single-Quote>)
	  ```
    - NOTE: May encounter collation error --> fix by change collation
      + Example: `on s.country_code collage SQL_Latin1_General_CP1_CI_AS = c.country_code`
  
## "CASE" statement
  - Conditional function, often in the `SELECT` part of the query --> evaluated for every row in the table
    + can also be used in other place
	+ can nested up to 10 levels
  - Basic syntax:
    ```
	CASE
	  WHEN <Conditional-Expression> THEN <Expression-When-True>
	  WHEN <Conditional-Expression> THEN <Expression-When-True>
	  ELSE <Expression-Default>
	END
	```
## String functions
  - STUFF: delete a specified length of characters in the first string at start position, then inserts the second string into the first string at the start position (e.g. similar as replace but use character count instead)
    ```
	STUFF(<String-Expression>, <Start-Position>, <Length>, <Replacement-Text>)
	```
  - LEFT, RIGHT, SUBSTRING: extract portion of string
  - STRING_SPLIT (Table function): split string by delimiter
    + Sample use case: First, cross apply string_split(description)
	+ Use ROW_NUMBER over primary case
	+ Pivot by MAX() or PIVOT function
	```
	SELECT 
	  Product_Code
	  , MAX(CASE WHEN AttributeID = 1 THEN Attribute else NULL END) AS Season
	  , MAX(CASE WHEN AttributeID = 1 THEN Attribute else NULL END) AS Product
	  ...
	INTO Product_Attributes
	FROM
	(
	    SELECT
		  Product_Code
		  , ROW_NUMBER() OVER (PARTITION BY Product_Code ORDER BY Product_Code) as AttributeID
		  , CASE ROW_NUMBER() OVER (PARTITION BY Product_Code ORDER BY Product_Code)
			  WHEN 1 then 'Season'
			  WHEN 1 then 'Season'
			  ...
			  ELSE 'ERROR'
			END AS AttributeName
		  , value Attribute
		FROM
		  Product_BASE
		  CROSS APPLY STRING_SPLIT(Product_Description, '-')) split
	GROUP BY Product_Code
	)
	```

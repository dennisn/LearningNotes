# Database Connections and Data Extraction with Python

- URL: <https://app.pluralsight.com/ilx/video-courses/python-database-connections-data-extraction/course-overview>

## Connecting to Relational Databases

- Relational databases
  - `psycopg2`: for PostgreSQL
  - `sqlalchemy`: ORM-based, support multiple db
  - `pyodbc`: ODBC-compatible Db (e.g. Ms SQL Server)
- NoSQL databases:
  - `pymongo`: direct MongoDB
  - `boto3`: AWS SDK for python (e.g. DynamoDB)
  - `redis-py`: Key-value store with Redis
- Normal database workflows
  1. Establish a connection --> `connection` object
  2. Create a cursor --> `cursor` object
  3. Execute query
  4. Fetch (for `select` query) or Commit/Rollback (if altering data)
  5. Close both cursor & connection

## Querying and Manipulating Data with Pandas and Database Connectors

- Reading SQL data into `pandas`
  - Using `sqlalchemy` -> create engine -> `pd.read_sql_query(selet_query, con=engine, params={})`
  - Using parameterised query: `query = sqlalchemy.text(...)` --> `pd.read_sql(query, con=engine, params={"key": "value"})`
  - Can also specify the data-type for input column: `dtype={"id": int, "salary": float}`
  - For large datasets, using `chunksize` to load data in chunk
  - Using `parse_dates` parameter with a array of datetime column
- Writing data from `pandas` to database: 
  - Dump the whole dataframe using `df.to_sql`
    - Can append current dataframe to existing table with `if_exists="append"` parameter
  - Using `df.iterrows()` to update one row at a time

## Working with NoSQL Databases

## Handling Large Queries and Cloud-hosted Database

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

## Working with NoSQL Databases

## Handling Large Queries and Cloud-hosted Database

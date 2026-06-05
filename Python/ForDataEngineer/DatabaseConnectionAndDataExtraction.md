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

- NoSQL: no schema, scale-out, often BASE transaction model (eventual consistency)
  - Use cases: Big-data, real-time apps (e.g. e-commerce carts, social media post, IoT logs)

### MongoDb

- Basic concepts
  - Database: same as in Relational Db
  - Collection ~ table in Relational Db, but won't actually created until data is inserted
- Performance consideration:
  - Scale by adding more nodes
  - Indexing & Partitioning for performance
  - Balance Read & Write
    - Read-heavy: use caching (Redis, Memcached)
    - Write-heavy: use batch writes & optimize schema --> avoid write bottleneck
    - Eventual consistency: better performance by may lead to stale data
- `pymongo` example
  ```python
  # connect
  client = pymongo.Mongo("mongodb://localhost:27017/")

  # Access Database or collection (after list_database_names() & list_collection_names())
  db = client["my_database_name"]
  collection = db["my_collection_name"]

  # Insert
  collection.insert_one({"name": "Alice", "role": "Developer"})
  collection.insert_many([{"name": "Bob"}, {"name": "Charlie"}])

  # Retrieve
  doc = collection.find_one({"name": "Alice"})
  results = collection.find({"hire_date": { "$gt": "2020-01-01"}})
  results = collection.find({"hire_date": { "$in": ["IT", "HR"]}}).sort("salary", pymongo.DESCENDING)

  # Update a single field
  collection.update_one(
      {"name": "Alice"}, 
      {"$set": {"role": "Senior Developer"}}
  )

  # Deletion
  collection.delete_one({"name": "Charlie"})

  # Close connection
  client.close()
  ```

## Handling Large Queries and Cloud-hosted Database

- Cloud-hosted db: managed infrastructure with scalability & high-availability
  - Data are often loaded into pandas dataframe, then process as with any other dataframes
- Challenges with large datasets: Can degrade performance if loading all data
  - Lazy loading: load data only when needed (e.g. Python generators)
  - Chunk processing: Pandas (`read_csv(chunk_size=...)`)
  - Dask processing: Parallel computation for big data (e.g. `dask.dataframe`)
- Security: even more important, especially encryption, as the data are transmitted over the internet

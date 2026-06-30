# DP-700 Microsoft Fabric Data Engineer Associate

- <https://learn.microsoft.com/en-us/credentials/certifications/fabric-data-engineer-associate/?practice-assessment-type=certification>
- Role: Data Engineer (Intermediate)
- Course: <https://learn.microsoft.com/en-us/training/courses/dp-700t00>

## Exercise

1.  [Create a Microsoft Fabric Lakehouse](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/01-lakehouse.html)
2.  [Analyze data with Apache Spark in Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/02-analyze-spark.html)
3.  [Use Delta Tables in Apache Spark](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/03-delta-lake.html)
4.  [Ingest data with a pipeline in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/04-ingest-pipeline.html)
5.  [Create & use Dataflows (Gen2) in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/05-dataflows-gen2.html)
12. [Work with data in a Microsoft Fabric eventhouse](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/12-query-data-in-kql-database.html)

## Ingest Data with Microsoft Fabric

### Ingest Data with Dataflows Gen2

- `Dataflows`: a type of cloud-base ETL tools --> visual low-code UI with `Power Query Online`
  - can be used to extract data from various sources to destination
  - used as a data source by data analysts
- Use cases:
  - Re-usable ETL logic --> destination is optional in `Dataflows`
  - Use **data pipeline** to orchestra (i.e. control wofklows) and move large files --> combined with `Dataflows` to "put" data to destination
- Benefits:
  - Extend data using a standardized dimension table (e.g., date dimension) for consistency. --> inconsistent, raw dates are mapped to a single, structured format
  - Enable self-service access to a curated subset of the data warehouse.
  - Improve performance with dataflows by extracting data once and reusing it, reducing refresh time for slow sources.
  - Reduce complexity by exposing only dataflows to broader analyst groups.
  - Improve data quality by allowing users to clean and transform data before loading.
  - Simplify data integration through a low-code interface that ingests data from multiple sources.
- Limitations:
  - Can't replace data warehouse --> performance ?
  - Row-level security isn't supported
  - Require Fabric capacity workspace

### Orchestrate processes and data movement

- `Data pipeline`: A sequence of *orchestrate* activities --> often used to automate the ETL
  - **Orchestrate**: control flow to manage branching/looping
  - can run interactively in Fabric, or via schedule
- Core concepts
  - `Activities`: executable tasks in the pipeline --> outcome (success/failure/completion) used to direct the flow
    - `Copy Data`: extract data from source & copy it to destination
    - `Dataflow Gen2`: apply transformations
    - `Notebook`: run Spark notebook
    - Execute a script/stored procedure
    - `Control flow`: for loop, conditional branch or manage variable & parameter value
  - `Parameters`: to provide specific values each time a pipeline is run (e.g. folder name by run date)
  - **Pipeline runs**: when a pipeline is executed --> has unique run ID, run details and settings
- Usages
  - order activities --> execute scripts/store procedures after a dataflow
  - pipelines can be scheduled/triggered to run --> automate dataflow execution to refresh data

### Use Apache Spark

- `Apache Spark` is a distributed data processing framework --> distribute the work accross multiple computers (i.e. `Spark pool`)
  - Code in Scala, Spark R, Spark SQL and PySpark --> often in **PySpark** and **Spark SQL**
- `Spark pools`:
  - **ONE** `head node`: coordinate distributed processes through a *driver* program
  - Multiple `worker nodes`: run *executor* processes to do the actual data processing
- In Fabric, `Spark pools` can be configured in Workspace settings -> Admin portal -> Capacity settings -> Data Engineering/Science settings
  - `Node Family`: type of virtual machines for Spark cluster node --> most suitable is *memory optimized*
  - `Autoscale`: switch for auto-provision nodes -> initial & max. number of nodes
  - `Dynamic allocation`: switch to dynamically allocate *executor* process on worker nodes --> depends on data volumes
  - Can set one as *default pool* for Spark job without specified pool
  - This could be disabled by Fabric admin at *Fabric Capacity* level

#### Runtimes & environments
- Spark runtime: multiple versions, each with different Spark, Delta Lake, Python & other core components --> multiple *environments* may be needed, each with specific runtime + *extra* libraries
- Spark environments in Fabric
  - Spark runtime + built-in libraries that are installed
  - Specific public libraries + custome libraries (e.g. from uploading package file)
  - The Spark pool that the environment should use
  - Spark configuration properties --> override default behavior
  - Extra resource files that are needed
- Extra configuration
  - **Native execution engine**: use vectorized processing engine (i.e. batch processing) that runs Spark operations directly on lakehouse infrastructure --> significant performance for large datasets
    - Enable it at environment or within notebook
      ```JSON
      %%configure 
      { 
        "conf": {
            "spark.native.enabled": "true", 
            "spark.shuffle.manager": "org.apache.spark.shuffle.sort.ColumnarShuffleManager" 
        } 
      }
      ```
  - **High concurrency mode**: share Spark sessions across concurrent users/processes
    - For a single user to share a session between multiple concurrent notebook --> reduce cost, improve multitask ==> single-user boundary, same Lakehouse configuration and Spark compute settings
    - Enabling via workspace settings -> Data Engineering/Science section
  - **Automatic MLFlow logging** --> enable by default, to log model training and management operations (e.g. deployment)
    - Can disable this in workspace setting

#### Run Spark code
- Can run Spark *notebook*, or a *Spark job* (i.e. spark/python script with specified lakehouse)
- Natively, Spark use RDD (`Resilient Distributed Dataset`), but most commonly used data structure in Spark is *dataframe*, part of `Spark SQL` library --> similar to `Pandas Dataframe`, but optimized for distributed processing
- When loading dataframe, schema could be inferred from header if not specified
  ```Python
  %%pyspark
  df = spark.read.load('Files/data/products.csv',
      format='csv',
      header=True
  )
  ```
  ```java
  %%spark
  val df = spark.read.format("csv").option("header", "true").load("Files/data/products.csv")
  ```
- Schema can also be explicitly specified --> improve performance
  ```python
  from pyspark.sql.types import *
  from pyspark.sql.functions import *

  productSchema = StructType([
      StructField("ProductID", IntegerType()),
      StructField("ProductName", StringType()),
      StructField("Category", StringType()),
      StructField("ListPrice", FloatType())
      ])

  df = spark.read.load('Files/data/product-data.csv',
      format='csv',
      schema=productSchema,
      header=False)
  ```
- Using `select` to filter column by names, `where` to filter rows by conditions, and `groupBy` to group rows for aggregation
  ```python
  # Select
  pricelist_df = df.select("ProductID", "ListPrice")
  pricelist_df = df["ProductID", "ListPrice"]
  
  # Select then filter
  bikes_df = df.select("ProductName", "Category", "ListPrice").where((df["Category"]=="Mountain Bikes") | (df["Category"]=="Road Bikes"))
  
  # Aggregation by group
  counts_df = df.select("ProductID", "Category").groupBy("Category").count()
  ```
- Saving dataframe into `parquet` file for later processing
  ```python
  # write into file, replace the existing one
  bikes_df.write.mode("overwrite").parquet('Files/product_data/bikes.parquet')
  ```
- Data can be partitioned with `partitionBy` --> when write, each partition will be written in subfolders named as "**column=value**" format
  - Can read whole dataset, or specific partitions by pointint to root or subfolder
  ```python
  # Partition by Category
  # Write into "Category=Mountain Bikes" and "Category=Road Bikes" subfolder
  bikes_df.write.partitionBy("Category").mode("overwrite").parquet("Files/bike_data")
  # Read a partition
  all_bikes_df = spark.read.parquet('Files/bike_data')
  road_bikes_df = spark.read.parquet('Files/bike_data/Category=Road Bikes')
  ```
#### Spark SQL
- Dataframe API is part of Spark SQL library
- Spark catalog: the metadata repository: a central dictionary of schemas, tables, views, and partitions --> allow SQL expressions (i.e. relational data style)
  - `view`: temporary --> delete at the end of current session
  - `table`: persisted in the catalog
  - `managed table` in Fabric stored their data in **Tables** storage location in data lake ==> delete `managed table` will delete its underlying data
    - Can use `partitionBy` as with write to file for performance
  - `external table`: metadata in catalog, but underlying data from external storage --> **path** must be specified on creation, data *not deleted* when table is removed
    ```Python
    # create temp. view
    df.createOrReplaceTempView("products_view")
    # create table & save data
    df.write.format("delta").saveAsTable("products")
    # Create the managed table
    df_managed = spark.catalog.createTable(
        tableName="default.users_managed",
        schema=schema,
        source="parquet",
        description="A managed table for user records"
    )
    # Create the external table mapping to an existing file location
    df_external = spark.catalog.createTable(
        tableName="users_external",
        path="s3a://my-bucket/data/users_csv/",
        source="csv",
        schema=schema,  # Optional if the source format can self-infer
        header="true",  # Forwarded option
        sep=","         # Forwarded option
    )
    ```
- Query data with SQL API
  ```python
  bikes_df = spark.sql("SELECT ProductID, ProductName, ListPrice \
                      FROM products \
                      WHERE Category IN ('Mountain Bikes', 'Road Bikes')")
  ```
- If using embed SQL expression --> return a resultset that is displayed in the notebook as a table
  ```SQL
  %%sql

  SELECT Category, COUNT(ProductID) AS ProductCount
  FROM products
  GROUP BY Category
  ORDER BY Category
  ```

#### Visualize data in Spark notebook
- Visualize data as charts --> intuitive ways to analyze
  - Notebooks in Fabric has some basic charting --> more can be added with Python graphics libraries
- Built-in notebook charts: switch output of Spark cell from table to chart, then customize it --> for quickly summarize the data visually
- Graphic packages (most built on `Matplotlib`) --> requires data to be Pandas dataframe, so use `toPandas()` to convert
  - Canalso use `Seaborn` to create charts
  ```python
  from matplotlib import pyplot as plt

  # Get the data as a Pandas dataframe
  data = spark.sql("SELECT Category, COUNT(ProductID) AS ProductCount \
                    FROM products \
                    GROUP BY Category \
                    ORDER BY Category").toPandas()
  # Clear the plot area
  plt.clf()

  # Create a Figure
  fig = plt.figure(figsize=(12,8))

  # Create a bar plot of product counts by category
  plt.bar(x=data['Category'], height=data['ProductCount'], color='orange')

  # Customize the chart
  plt.title('Product Counts by Category')
  plt.xlabel('Category')
  plt.ylabel('Products')
  plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
  plt.xticks(rotation=70)

  # Show the plot area
  plt.show()
  ```

### Work with real-time data in an EventHouse
- `Eventhouse` in Fabric: contain 1 or more `KQL` databases (i.e. **Kusto Query Language**): optimized for real-time data
  - Ingest: using `EventStream` or directly load into database
  - Query: Using KQL or T-SQL in KQL queryset
  - Visualize with **Real-Time Dashboard**
  - Automate with Fabric Activator
- `KQL database` characteristics
  - partition data by ingestion time --> best for time-series data, 
  - Time-series data: each is an immutable event (i.e. append-only pattern, rarely change, timestamp is as important as the value)
- `KQL database` ingestions
  - From local files, cloud files (e.g. Azure, Amazone S3, etc.)
  - Streaming: Azure Event Hubs, Fabric EventStream, Real-Time hub
  - Copy from other Azure datasource: OneLake, Dataflows, Data Factory Copy
  - Connectors to message sources: Apache/Confluent Kafka, Apache Flink, MQTT (Message Queuing Telemetry Transport), Amazon Kinesis, Google Cloud Pub/Sub
  - Other: 
    - "export": enable "**OneLake availability**" for databases/tables --> expose our KQL to other OneLake
    - "import": create shortcuts to other KQL databases, or Azure Data Explorer databases

#### Kusto Query Language (KQL)
- **KQL syntax**: case-sensitive
  - Uses a pipeline approach --> data flows from one operation to the next using the pipe "**|**" character
  - Sample: from *TaxiTrips* table -> only trips with fare over 20$ -> `project` (i.e. shows) only specific columns -> `take` the first 10 rows only
    ```KQL
    TaxiTrips
    | where fare_amount > 20
    | project trip_id, pickup_datetime, fare_amount
    | take 10
    ```
  - Can also aggregate data ưith `summarize`: count number of trips for each unique `taxi_id` as column `trip_count`
    ```KQL
    TaxiTrips
    | summarize trip_count = count() by taxi_id
    ```
- **KQL Queryset**:  workspace for running & managing queries against KQL database --> can save queries, organize multiple query tabs, share queries
  - can render query results as charts, tables or other visual
  - Can use Copilot to assist with KQL query --> Copilot for Real-Time Intelligence (need administrator enable)
- KQL optimization
  - Why:
    1. Run faster <-- reduce data
    2. Use fewer resource <-- less columns
    3. Scale with growing data
  - Key optimization
    - Filter data early & effectively --> filter on indexes, **time-based filtering** for Eventhouses with time-series data
    - Order filters --> put those eliminate the most first
    - Reduce columns early
    - Aggregation --> limit results when exploring data
    - Join --> put the smaller table first

#### Materialized views
- `Materialized views`: precomputed aggregation that be updated automatically --> treated as table
- Structure: 2 parts
  1. **A materialized part**: precomputed aggregation results from data that has been processed
  2. **A delta**: new data that has arrived since the last background update
- When query --> automatically combines both parts for latest results
  - In background, periodically update the **materialized part** with new data

```kql
.create materialized-view TripsByVendor on table TaxiTrips
{
    TaxiTrips
    | summarize trips = count(), avg_fare = avg(fare_amount), total_revenue = sum(fare_amount)
    by vendor_id, pickup_date = format_datetime(pickup_datetime, "yyyy-MM-dd")
}
```

#### Stored Functions
- `Stored function`: encapsulate a query, with parameter --> reuse & share of repeat common queries
```kql
.create-or-alter function trips_by_min_passenger_count(num_passengers:long)
{
    TaxiTrips
    | where passenger_count >= num_passengers 
    | project trip_id, pickup_datetime
}
```
- Usage: like a table
```kql
trips_by_min_passenger_count(3)
| take 10
```

## Implement a Lakehouse with Microsoft Fabric

### Introduction to end-to-end analytics using Microsoft Fabric
- `Fabric`: (*SaaS*) end-to-end analytics platform based on unified data lake (i.e. `OneLake`)
  - Data engineer works on organizing & governing data --> benefit business analysis & AI workloads
- `OneLake`
  - Built on **Azure Data Lake Storage Gen2** (i.e. `ADLS Gen2`) --> support formats: Delta, Parquet, CSV, JSON, etc.
    - `Fabric` analytical engine write data in Delta-Parquet format
    - All Fabric engines automatic store data in OneLake
  - `Shortcuts`: data stored in **external** sources (e.g. Azure Data Lake Storage, S3, Dataverse, etc.) --> accesible without copying/moving
  - Data are all stored in "open format" --> accessible to AI capabilities (e.g. Copilot, data agent, etc.)
- `Workspace`: logical container to manage/organise data, reports & other assets
  - Easier for security (e.g. access control)
  - Allow manage compute resources, version control (e.g. Git)
- **OneLake administration**: centralised
  - Manage groups & permission
  - Configure data sources & gateways
  - Monitor usage & performance
  - Automate common tasks & integration with other system
  - `OneLake catalog`: analyze, monitor & maintain data governance

#### Usage
- Previously: disconnect between data engineers vs. analyst vs. scientist --> require extensive coordination ==> delay & mis-interpretations
- With Fabrics
  - `Data engineers`: manage the ELT/ETL with pipelines --> clean & well-governed data
  - `Analytics engineers`: curate data assets, ensure data quality, create semantic models in `Power BI`
  - `Data analysts`: create interactive reports with `Power BI` with *Direct Lake* mode, or upstream data transform using dataflows
  - `Data scientists`: build ML models via integrated notebooks with Python & Spark on data in OneLake --> results as grounding data for Copilot & AI agents
  - `Low-to-no-code users`: 
    - Discover *curated datasets* via OneLake catalog
    - Create reports & dashboards with *Power BI templates*
    - Use dataflows for simple ETL
    - Use `Copilot` for data question

#### Enable & management of Fabric
- Administration: need one of
  - **Fabric administrator**: manages Fabric settings & configuration
  - **Power Platform administrator**: manage Power Platform services, which include Fabric
  - **Global Administrator**: implicit Fabric admin rights
- Enable: via **Admin portal > Tenant settings** in Power BI service
  - can enable for whole organisation, or specific Microsoft 365/Entra security groups
  - can delegate this ability to other users at capacity level
- Workspaces: manage lakehouses, warehouses & reports --> manage OneLake access
  - Data lineage view: visual view of data flow & dependencies
  - Settings:
    - License type --> Fabric features
    - OneDrive access
    - Azure Data Lake Gen2 Storage connection
    - Git integration
    - Spark workload --> performance optimisation
  - Access control via 4 roles: *admin, contributor, member and viewer* --> applied to all items in workspace ==> specific item also has item-level permissions
- OneLake catalog: only see items (e.g. data sources) that are accessible
  - Usage: narrow by workspaces or domains (if applicable) > default categories > keywork or item type
- Fabric workloads
  - `Data engineering`: create lakehouses & workflows
  - `Data factory`: ETL/ETL & orchestrate data
  - `Data Warehouse`: combine multiple sources for analytics
  - `Real-Time Intelligence`: for streaming data
  - `Industry Solutions`: third party data solution
  - `Data science`: identify trends, outliers & predict values using ML
  - `Power BI`: create reports & dashboards
  - `IQ (preview)`: ontologies, graphs & semantics models (i.e. metadata about entities, attributes/properties & relationship)
- Copilot in Fabric:
  - **Code completion & generation**
  - **Data transformation guidance**: in Data Factory --> code generation & plain-language explanation of complex logic
  - **Report & insigh generation**: from user question about data in natural language
- IQ workloads:
  - `Fabric IQ`: metadata help agents reason with data in OneLake & Power BI
  - `Foundry IQ`: connect structured & unstructured data from enterprise knowledgebase
  - `Work IQ`: captures workplace contexts from email, documents, meetings, chats & workflows for agent

### Get started with lakehouses in Microsoft Fabric
- `Lakehouse`: 2 main data areas
  - **TABLES**: structured, query-able table
    - ACID transaction, direct SQL query --> automatic optimization & maintenance
    - Accessed by Power BI for reporting
  - **FILES**: raw or semi-structured data file --> any format (e.g. CSV, JSON, Parquet, images, documents, etc.)
    - Flexible storage for exploration & processing --> staging areas before transform into table
    - No schema, not support direct SQL queries
  - Normal usage: Spark notebook or Dataflow Gen2 --> process *files* before loading into *tables*
- `Delta Lake tables`: open-source, Parquet files plus transaction log for changes
  - **Efficient** updates & delete
  - **Relational-like characters**: Schema enforced, ACID transaction supported
  - **Time travel**: point-in-time data
- Lakehouse access:
  - *Workspace roles*: control access to all items in the workspace
  - *Item-level sharing*: read-only access for specific needs (e.g. analytics or Power BI report)
  - *Row-level* and *Column-level* security, event *Schema-level* security: for SQL analytics endpoint only
- AI integration: Fabric IQ, Copilot in Power BI can also use SQL analytics endpoint as data input

#### Lakehouse manipulation
- On creation --> `schemas` are enabled by default, with `dbo` automatically created
  - Schemas: organize tables into logical groups by business domains/functions (e.g. sales, marketing, hr, etc) --> greate for permissions & AI data agent ==> access via `workspace.lakehouse.schema.table`
- Access lakehouse in 2 modes
  - `Lakehouse explorer`: browse & manage "items" in lakehouse (e.g. tables, files, folder, shortcuts), manage & upload data
  - `SQL analytics endpoint`: access Delta table in *read-only* --> can't modify underlying data, but could create views, function, SQL security
- **Ingest data**:
  - *Upload*: files/folders directly
  - *Load to Table*: from files/folders in Lakehouse to Delta Table --> for Parquet & CSV files
  - *Data factory pipelines*: Using **Copy** to load data from external sources
  - *Dataflow Gen2*: Using Power Query for ingestion
  - *Notebook*: Using Apache Spark
  - *Shortcuts*: for external storage --> can also create **schema shortcuts**

#### Lakehouse data query
- Via SQL analytics endpoint: for access to lakehouse **tables** with T-SQL queries
  - **Use cases**: ad-hoc queries, BI connection for tools (e.g. Power BI, Excel, Azure Data Studio, etc), data validation
  - Can create SQL views for reusable query logic --> apply business rule, hide complex join, provide curated data for downstream
  - Support **row-level/column-level** security
- Via Spark notebooks: flexible, code-based environment for querying & analyzing data
  - **Use cases**: exploratory (e.g. patterns, outliers, relationship), complex transformation, cross-workspace query --> prepare data for ML
- Via Power BI: in 2 ways
  . Query `SQL analytic endpoint`: BI tools (e.g. Power BI, Excel, etc.) --> ad-hoc queries & explore data
  - `Semantic model`: use `Direct Lake` mode, defines relationships, measures & business logic  --> read data directly from Delta Lake Parquet files ==> fast query & latest data. Can also re-used by AI

### Work with Delta Lake tables in Microsoft Fabric
- `Delta Lake`: add schema abstractions over data file
  - Structure: each table = folder of *Parquet* files, and a **_delta_log** folder: transaction details in JSON format
  - Benefit: 
    - Relational tables: **CRUD** via Spark, **ACID** transactions
    - Data versioning & *time travel*
    - Support batch & streaming
    - Standard format --> inter-operability

#### Create Delta tables
- Creating table in Fabric Lakehouse --> define a delta table in lakehouse's metastore, data is stored in Parquet file
  - Basic **PySpark**: `df.write.format("delta").saveAsTable("MyTable")` --> data stored in `Tables` storage location
    - *External* tables: as above, but specificed **path** --> data stored in `Files`, or external sources ==> delete table won't delete the data file
    - **NOTE**: same syntax can use to save data without create tables: `df.write.format("delta").mode("overwrite").save(delta_path)`
  - Create **table metadata**
    - Use `DeltaTableBuilder` API:
      ```Python
      from delta.tables import *

      DeltaTable.create(spark) \
        .tableName("products") \
        .addColumn("Productid", "INT") \
        .addColumn("ProductName", "STRING") \
        .addColumn("Price", "FLOAT") \
        .execute()
      ```
    - Use `Spark SQL` --> adding `LOCATION` to create *external* tables ==> for table reference existing data
      ```Python
      %%sql

      CREATE TABLE salesorders
      (
          Orderid INT NOT NULL,
          OrderDate TIMESTAMP NOT NULL,
          CustomerName STRING,
          SalesTotal FLOAT NOT NULL
      )
      USING DELTA
      # LOCATION 'Files/mydata'
      ```

#### Optimize delta tables
- Spark parallel processing + parquet files are immuable (i.e. new files for every update/delete) --> can have large number of small files (i.e. *small file* problem)
- `OptimizeWrite`: help prevent *small files problem* by shuffle write (i.e. combined write)
  - enabled by default, but can change it at session level via `spark.microsoft.delta.optimizeWrite.enabled`

##### Lakehouse operations for optimization
- Operation `OPTIMIZE`: consolidates Parquet files via **Lakehouse explorer > ... > Maintenance**
  - V-Order function: can enable with `Optimize` operation
    - a **write-time optimization** that enhances **read speeds** by applying special *sorting, row group distribution, dictionary encoding, and compression*
    - Write: 15% slower --> READ 10-50% faster, 50% more compressed
    - Compliant with open-source Parquet
    - **NOT** useful for write-intensive use, such as in staging where data only read once or twice
- Operation `VACUUM`: remove old data file via **Lakehouse explorer > ... > Maintenance** --> reduce "*time-travel*"
  - Depend on: retention & regulatory requirements, data change frequency, data size & storage cost
  - can also trigger in notebook
    ```python
    %%sql
    VACUUM lakehouse2.products RETAIN 168 HOURS;
    ```
- `PARTITION` delta tables --> fixed data layout, so think about how data is used and its granularity
  - Useful with **large** amounts of data, which could be split into a **few** large partition
  - Not useful: *small* data volume, *high cardinality* (i.e. large number of unique values), or column which partitioning would result in multiple levels (e.g. timestamp with implicit multi-level --> year/month/day)
  ```python
  df.write.format("delta").partitionBy("Category").saveAsTable("partitioned_products", path="abfs_path/partitioned_products")
  ```
  ```python
  %%sql
  CREATE TABLE partitioned_products (
      ProductID INTEGER,
      ProductName STRING,
      Category STRING,
      ListPrice DOUBLE
  )
  PARTITIONED BY (Category);
  ```

#### Spark with delta tables
- Using **Spark SQL**: `spark.sql("INSERT INTO products VALUES (1, 'Widget', 'Accessories', 2.99)")`
  - Alteratively, with `%%sql` --> SQL syntax: `UPDATE products SET ListPrice = 2.49 WHERE ProductId = 1;`
- Use the **Delta API**
  ```Python
  from delta.tables import *
  from pyspark.sql.functions import *

  # Create a DeltaTable object
  deltaTable = DeltaTable.forPath(spark, "Files/mytable")

  # Update the table (reduce price of accessories by 10%)
  deltaTable.update(
      condition = "Category == 'Accessories'",
      set = { "Price": "Price * 0.9" })
  ```
- Use *time travel* with table version
  - First, use `DESCRIBE` SQL command to show history
    ```Python
    %%sql
    DESCRIBE HISTORY products
    # also work on delta file: DESCRIBE HISTORY 'Files/mytable'
    ```
  - Then, retrieve the specific version, or timestamp
    ```python
    df = spark.read.format("delta").option("versionAsOf", 0).load(delta_path)
    # df = spark.read.format("delta").option("timestampAsOf", '2022-01-01').load(delta_path)
    ```

#### Delta tables with streaming data
- **Spark Structured Streaming**: constantly read data from *source* as *boundless dataframe* -> (optionally) process data: select fields, aggregate/group values -> write result to *sink*
  - Can read data from various streaming sources: network ports, file locations, message services (e.g. Kafka, Azure Event Hubs, etc.)
- **Delta table**: can be used as *source* or *sink* for streaming
  - As source: 
    ```Python
    stream_df = spark.readStream.format("delta") \
        .option("ignoreChanges", "true") \
        .table("orders_in")
    ```
    - NOTE: with `readStream` --> only *append* is valid. Any *update/delete* will cause error unless `ignoreChanges` or `ignoreDeletes` is enabled
    - Check if the datafram is "streaming" with `stream_df.isStreaming` --> should be True
  - Transform: as with normal Spark dataframe (i.e. filter, withColumn, etc.)
  - As sink: `deltastream = transformed_df.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(output_table_path)`
    - `checkpointLocation`: for checkpoint file --> enable recovery from failure at the point where stream processing left off
    - Stop streaming: `deltastream.stop()`

### Organize a Fabric lakehouse using Medallion architecture design

## Implement Real-Time Intelligence with Microsoft Fabric

## Implement a data warehouse with Microsoft Fabric

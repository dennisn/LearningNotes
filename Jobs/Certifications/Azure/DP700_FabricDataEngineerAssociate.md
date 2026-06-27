# DP-700 Microsoft Fabric Data Engineer Associate

- <https://learn.microsoft.com/en-us/credentials/certifications/fabric-data-engineer-associate/?practice-assessment-type=certification>
- Role: Data Engineer (Intermediate)
- Course: <https://learn.microsoft.com/en-us/training/courses/dp-700t00>

## Exercise
- [Create & use Dataflows (Gen2) in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/05-dataflows-gen2.html)
- [Ingest data with a pipeline in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/04-ingest-pipeline.html)
- [Analyze data with Apache Spark in Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/02-analyze-spark.html)

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

## Implement a Lakehouse with Microsoft Fabric

## Implement Real-Time Intelligence with Microsoft Fabric

## Implement a data warehouse with Microsoft Fabric

# DP-900 Azure Data Fundamental

- <https://learn.microsoft.com/en-us/credentials/certifications/azure-data-fundamentals/?practice-assessment-type=certification>
- Role: Data Engineer (Beginner)
- Course syllabus: <https://learn.microsoft.com/en-us/training/courses/dp-900t00#course-syllabus>

## Achievements:

- <https://learn.microsoft.com/api/achievements/share/en-us/DennisNguyen-2220/9AXA3TCU?sharingId=A7653B4AD5E9934>

## Core concepts

### File formats

- Delimited text files, JSON (Javascript Object Notation), XML (Extensible Markup Language), Binary Large Object (BLOB)
- Optimized file formats: for storage space or processing
  - `Parquet`: columnar data format, de facto standard for modern data lakehouse: store column data together in row groups --> for nested data types with efficient compression & encoding schemes
  - `Avro`: row-based format --> good for compressing, minimizing storage & bandwidth requirement
  - `Delta Lake`: adding transaction log to Parquet --> enables ACID transactions, data versioning & time travel

### Modern analytics platforms

- `Microsoft Fabric`: SaaS analytics platform (Software-as-a-Service): brings storage, data engineering & warehousing, and reporting capability together
- `Azure Databricks`: cloud analytics platform using "Delta Lake" as storage format --> for large-scale data engineering & data science. 
- `Microsoft Purview`: unified data security, governance & compliance --> manage all data sources

### Organizing data with the "Medallion architecture"

- `Bronze`: raw data --> as-is from source
- `Silver`: cleansed & conformed data (de-duplication & standardised data types)
- `Gold`: aggregated for specific use cases (e.g. report, analytic)

### Job role in world of Data

- **Database administrators**: manage databases & storage (e.g. users, backup/restore)
- **Data engineers**: manage infrastructure & processings of data (e.g. cleaning, governance rules, data pipelines)
- **Data analysts**: analyzes data to find trends & insights
- **AI Engineers**:  builds AI models & turn data into intelligent solutions
- Potential others: **Data Scientist** and **Data Architect**

### Data services

- `Azure SQL`: family of solution based on Microsoft SQL database
  - **Azure SQL database**: fully managed Platform-as-a-Service (PaaS)
  - **Azure SQL Managed instance**: SQL server with automated maintenance --> more configuration & adminstrative for the owner
  - **Azure SQL VM**: virtual machine with SQL Server --> full management responsibility to owner
- `Open-source databases in Azure`: managed services for `MySQL` & `PostgreSQL`
- `Azure Cosmos DB`: fully managed, globally distributed NoSQL, but support multi-models (e.g. can be used as MongoDb, Cassandra, or even relational Db)
- `Azure Storage`: for data storage as blob containers, files or tables.
- `Azure Data Factory`: for building & manage data pipelines
- `Microsoft Fabric`: unified Software-as-a-Service (SaaS) analytics platforms (e.g. all data related)
  - `Microsoft Fabric IQ`: unifies data across OneLake and give it consistent business meaning
- `Power BI`: business intelligence & data visualization platform
- `Azure Databricks`: cloud analytics platform build on `Apache Spark` for large-scale data processing
- `Azure Stream Analytics`: real-time stream processing engine
- `Azure Data Explorer`: fully managed, stand-alone big data analytics platform for log & Internet-of-things (IoT) telemetry data
- `Microsoft Purview`: a solution for enterprise-wide data governance & discoverability --> map of data and track data lineage across multiple data sources & systems
- `Microsoft Foundry`: unified Azure Paas for enterprise AI

## Relational data in Azure

- Exercise: [Explore Azure Database for PostgreSQL](https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-01a-postgresql-lab.html)

### Core concepts

- `SQL` or *Structured Query Language* --> popular dialects
  - `T-SQL` (Transact-SQL): for Microsoft
  - `pgSQL`: implemented by PostgreSQL
  - `PL/SQL` (Procedural Language/SQL): by Oracle
- SQL statements are grouped into 3 main group
  - `DDL` (Data Definition Language): manage table, stored procedures, views, etc.
  - `DCL` (Data Control Language): manage access
  - `DML` (Data Management Language): manipulate & retrieve rows in tables
- Level of normalization (**Normal Forms**)
  1. Cells must hold single, atomic value
  2. Remove partial dependencies (i.e. non-key columns depend on the *whole* primary key - no partial dependencies)
  3. No transitive dependencies (i.e. non-key columns depend *only* on the primary key, not on onther non-key columns)
     - Example: "Employee" tables with key "EmployeeId", have column "DepartmentId" and "DepartmentName" --> "DepartmentName" depends on "DepartmentId", so it shouldn't be in "Employee" table

### Azure SQL services & capabilities

1. `SQL Server on Azure VMs`: IaaS, replicate the on-prem. database --> can scale up by allocate more resources
2. `Azure SQL Managed instance`: can have multiple databases, less administrative
3. `Azure SQL Databases`: fully managed, high-availability, can scale up & down quickly --> can pool resources to share among multiple databases, other cloud-benefits (e.g. replicated to different region, security & auditing tracks, point-in-time restore, etc.)

## Non-relational data in Azure

### Exercise

- [Explore Azure Storage](https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-02-storage-lab.html)
- [Explore Azure Cosmos DB](https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-03-cosmos-lab.html)
- [Explore data analytics in Microsoft Fabric](https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-04b-fabric-lake-lab.html)

### Azure Blob storage

- `Blobs` are stored in containers, retrieved via blob name. Blob name can use '/' to organise into namespaces, but not real folder (i.e. no folder-level operation or access control) 
- Blob types:
  1. `Block Blobs`: vary blocks, each up to 4000 MB, max 50k blocks (190.7 TB) --> for large but infrequently changed binary objects
  2. `Page Blobs`: fixed 512-byte pages, max 8TB total, good random read/write --> VM virtual disk
  3. `Append Blobs`: append-only, block size up to 4MB, max 195 GB total --> for log files
- For access tiers:
  1. `Hot`: default, highest storage cost, lowest access cost --> high-performance media
  2. `Cool`: less storage cost, high access cost, min 30 days or penalty --> infrequent access (i.e. out-dated data)
  3. `Cold`: event less storage cost, higher access cost by still fast retrieval, min 90 days --> short-term backups
  4. `Archive`: lowest storage cost, need up to 15h rehydration before accessible, min 180 days --> offline storage
- Common access tier usage: lifecycle management polity (i.e. based on no. of days since modification) --> move a blob down the access tier, or even delete outdated blobs
- Redundancy options:
  1. `Locally Redundant Storage (LRS)`: lowest cost, 3 copies within same data center (availaiblity zone)
  2. `Zone-Redundant Storage (ZRS)`: Spread copies across 3 availability zones in a primary region
  3. `Geo-Redundant Storage (GRS)`: Replicate to secondary region from 1 data center in primary region
  4. `Geo-Zone-Redundant Storage (GZRS)`: Replicate to secondary region, while spread copies across availability zones in primary region

### Azure Data Lake Storage Gen2

- An improve version of Azure Blob Storage with hierarchical namespace support (i.e. folder structure, file system interface)
- Requirement: enable "**Hierarchical Namespace**" option of an Azure Storage account
  - NOTE: Unable to revert back to flat namespace

### Microsoft OneLake in Fabric

- `OneLake` is automatically provision by Microsoft Fabric, built on `Azure Data Lake Storage Gen2`, support structured & unstructred data
- **Benefits**:
  - a single, unified data lake for organisation
  - Distributed ownership & collaboration: within a tenant (i.e. organisation), each parts (i.e. department) have their own workspaces to manage their data --> promote collaboration yet maintain governance boundary
  - Open and Compatible: Stores data in Delta Parquet format --> compatible with current applications
  - Easy to navigate: from Windows using "OneLake file explorer"

### Azure Files

- Replace the file shares on local network: accessible from anywhere, scales with large users
- Storage account: 
  - Up to 256 TB (SSD, higher throughput) or more with HDD (lower cost)
  - Max file size: 4 TB per file, quota per share, up to 2k concurrent handles per file/directory
  - Upload via web portal, `AzCopy` utility or `Azure File Sync`
- Two common network file sharing protocol
  1. `Server Message Block (SMB)`: commonly used across OS (Windows, macOS, Linux)
  2. `Network File System (NFS)`: used by Linux, but not directly on Windows/macOS --> use use SSD with virtual network for access controlled

### Azure Tables

- Use Tables for key -> value(s), where key is "partition key + row key"
  - Same concepts to `Azure Cosmos DB for Table`
  - Each rows also has a `timestamp` column for modification date & time
  - Data (i.e. value) is often **denormalized**, with varies number of fields
  - Partions: for grouping related rows, can grow or shrinks independent of each other --> for scalability &performance

### Azure Cosmos DB

- Fully managed service --> all underlying infrastructured is handled for you
- Cosmos DB is **schema-agnostic**: items don't need to share the same structure/schema
- Logical organisation:
  1. `Account`: Top level resource, contain unlimited databases
  2. `Database`: logical namespace to group related container (e.g. "table")
  3. `Container`: main unit of storage & scaling --> configure partition key, throughput, indexing policy & optional time-to-live (TTL)
  4. `Items`: individual data items (i.e. documents, rows, nodes or edges)
     - Cosmos DB automatically creates and maintains indexes on all item properties by default.
- Built for global distribution --> automatically replicates to each "added" region in an account, reads & writes to nearest replica
- Offers 5 **consistency levels**
  1. `Strong`: every read got the most recent write
  2. `Bounded staleness`: reads lag writes by a configurable interval of time or version count
  3. `Session`: most widely used, guarantee consistency within a client session
  4. `Consistent prefix`: Reads may see stale data, but never *out-of-order writes*
  5. `Eventual`: lowest guarantee but highest availability, replicas converge over time
- Throughput measures in `Request Units per second (RU/s)` ~ reading 1KB item/s
  - Normal throughput consumption: 
    - Read/Delete: 1 RU/s
    - Write: 5 RU/s
    - Query/Search: 2.3 RU/s
  - Three throughput modes:
    - `Dedicated`: throughput reserved exclusively for a single container
    - `Shared`: throughput at database level, shared across up to 25 containers
    - `Serverless`:n o provision upfront, but pay per request --> limited to a single Azure region. For multiple regions, use a provisioned mode instead
    - **Autoscale**: set a maximum RU/s --> scale capacity automatically within that range based on demand
- Use case:
  - IoT and telemetry: high-frequency data, near real-time processing
  - Gaming: for fast response times
  - Retail & e-Commerce:  catalogs, shopping carts & order pipelines at scales
  - Web & mobile apps
  - NOT for:
    - Complex multi-table join --> Azure SQL
    - Larg-scale historical analytics --> Fabric or Synapse Analytics
- API models: supported 5 APIS --> For ease of migration (e.g. the API as as an abstraction on top)
  - `NoSQL`: data as JSON documents, query with SQL-like syntax --> recommended for enw application
    - Can be replicated into Fabric automatically without pipeline for analytics --> no impacts on transactional workload
  - `MongoDB`: Data as BSON (Binary JSON) and query with MongoDB Query Language (MQL)
  - `Table`: same as Azure Table Storage  --> recommend as a replacement
  - `Apache Cassandra`: using Cassandra Query Language (CQL)
  - `Apache Gremlin`: designed for graph data, where entities are represented as vertices (nodes) and relationships as edges --> useful when the connection are as important as the entity itself (e.g. social network, recommendation engines, fraud detection, etc.)

## Fundamentals of large-scale analytics

- `Data Lakehouse`: combined Data Warehouse & Data Lake
  - `Data Warehouse`: from relational databases --> aggregate into Data Warehouse --> BI & multi-dimensional models (reports, daskboards and OLAP analysis)
  - `Data Lake`: from multi-format data sources (e.g. JSON, CSV, streaming) --> batch/real-time data into Data Lake --> Data processing & analytics with Spark

### Excercise

- Explore Microsoft Fabric Real-Time Intelligence: <https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-05c-fabric-realtime-lab.html>

### Large-scale data analytics architecture

1. Data ingestion & processing:  ETL/ELT --> data is cleaned, filtered & restructured (transformed) for analysis ==> data processing may be done by distributed, parallel system
2. Analytical data store: the data after ETL/ELT
3. Analytical data model: pre-aggregation of analytical data store for reports, dashboards and interactive visualizations
   - Prefer approache now is `semantic model`: defines tables, relationshhips, hierarchies and measures in  DAX (Data Analysis Expressions - formula language used to define calculations) --> aggregations computed at query time
4. Data visualization: built from analytical models, may be performed by non-technology professionals
5. AI-assisted analytics: extend analytical self-service to users who do not write queries or build reports

Two main offering:
- `Microsoft Fabric`: unified SaaS platform --> browser-based workspace, on top of shared storage `OneLake`
  - `Fabric Lakehouse`: stored data in **Delta Lake** format --> access via SQL analytics endpoint
  - `Fabric Warehouse`: fully managed SQL Server compatible data warehouse --> structured analytics with strong schema
  - `Fabric Data Factory`: data pipelines & transformation
  - `Power BI`: reports, dashboards and semantic models directly from `OneLake`
- `Azure Databricks`: cloud analytics platform built on Apache Spark, use `Delta Lake` for storage --> for code-first Spark and notebook-based workflows
  - `Databricks Lakehouse`: unified storage layer on `Delta Lake`
  - `Databricks SQL`: serverless SQL warehouse --> analytical queries directly against Delta tables
  - `Databricks Notebooks`:  for data engineering pipelines, exploratory & machine learning
  - `Unity Catalog`: unified governance layer for data & AI assets --> access control, data lineage & discovery
  - `Genie`: AI-power interface --> can ask natual language question about the data ==> return results by generate & executes SQL automatically

### Data ingestion pipelines

Three main solutions:
1. `Fabric Data Factory`: via two tools: **Pipelines** (e.g. chaining activities in sequence/parallel) and **Dataflows Gen2** (visually building data transformation with Power Query)
   - `OneLake shortcuts`: Live reference to external storage (e.g. AWS S3, Google Cloud or another OneLake) --> for when data must stay in its original location due to compliance or cost, but still needs to be queryable from Fabric
   - `Mirroring`: replicates external database directly into OneLake in near-real-time
   - `Eventstream`: for real-time streaming ingestion from Azure Event Hubs, Apache Kafka, IoT Hub and custom endpoints --> enabling near real-time analytics on continuously arriving data
   - `Fabric Notebooks`: for custom data ingestion, or transformation logic
2. `Azure Data Factory`: standalone service for pipelines outside of `Fabric` (e.g. destination is Azure SQL DB or external services)
3. `Azure Databricks`: Databricks-based approaches
   - `Lakeflow Declarative Pipelines`: declarative framework for pipoelines on `Delta Lakes` (i.e. define what the output tables should contains, Databricks automate the execution)
   - `Databricks Notebooks`: for ad-hoc or exploratory ingesting work
 
### Analytical data stores

#### Data Warehouse

- Relational database optimized for analytics (not transactional)
- Often, data is transformed into Star/Snowflake schema: one central **FACT** table with numeric values, related to 1..* **DIMENSION** tables that represent entities by which the data can be aggregated

#### Data Lakes

- File store, often distributed for high performance data access
- Often apply **schema-on-read**: define tabular schemas when reading for analysis, without applying constraints when it's stored

#### hybrid approaches (Data Lakehouse)

- Raw data is stored as files (Delta Lake)
- Schema + transactional consistency on top of Parquet files
- Expose SQL analytics endpoint

## Fundamentals of Real-Time Analytics

### Batch and Stream processing

- Differences:
  - **Data scope**: all data in datasets vs. most recent data, or a rolling time window
  - **Data size**: efficient with large datasets vs. individual records or **micro batches** of few records
  - **Performance**: latency of few hours vs. seconds/milliseconds
  - **Analysis**: complex analytics vs simple functions/aggregations
- Combine batch & stream processing (common in real-life): streaming data are inputs to both real-time analytics & stored for later batch processing
  - Result of stream processing can be saved for historical reports & analysis

#### Basic architecture of stream processing

1. Event generates some data: e.g. emitted signal from sensor, log entry being written, etc.
2. Data is captured in streaming **source**: folder/table in database, or queue (i.e. order processing & exactly once)
3. **Perpetual Query**: filter & project/aggregate value
4. Results are written to an output (e.g. **sink**)

- Real-time analytics service:
  - `Microsoft Fabric Real-Time Intelligence`: toolset built into `Microsoft Fabric`, include `EventStream` (i.e. ETL/ELT), `Eventhouse` (i.e. db), `Real-Time Dashboards` and `Activator` (i.e. trigger automated action when certain conditions are met)
  - `Spark Structured Streaming`:  open-source library based on Apache Spark
  - `Azure Stream Analytics`: PaaS for standalone or hybrid streaming outside of `Fabric`
- Streaming sources:
  - `Azure Event Hubs`: event data queue service, to ensure processing order & exactly once
  - `Azure IoT Hub`: event data queue service optimized for IoT
  - `Azure Data Lake Store Gen 2`: scalable storage service, often used for **batch processing**, but can also be used for streaming
  - `Apache Kafka`: pub/sub message queue
- Streaming sinks:
  - `Azure Event Hubs`: queue
  - `Azure Data Lake Store Gen 2`, `Microsoft OneLake`, or `Azure blob storage`: results as files
  - `Azure SQL Database`, `Azure Databricks` or `Microsoft Fabric`: results in table
  - `Microsoft Power BI`: for real-time data visualization in reports & dashboards

### Microsoft Fabric Real-Time Intelligence

- Set of tools built into `Microsoft Fabric` for streaming data: full pipeline from arrival to visualization & automated action (i.e. Event Stream --> Event house --> Reflect)
- `Real-time hub`: centralized data catalog

### Apache Spark structured streaming

- `Spark structured streaming`: library built into Spark for streaming --> treat live data stream as table with growing rows
- `Delta Lake`: add traditional database characteristics to data lake storage:
  - **Reliability**: track data change --> partial/failed writes won't corrupt data
  - **Schema enforcement**: only accept data matching a defined structured
  - **Unified batch & streaming**: can serve as both streaming sink & source for batch queries

## Fundamentals of data visualization

### Exercise

- [Explore fundamentals of data visualization with Power BI](https://microsoftlearning.github.io/DP-900T00A-Azure-Data-Fundamentals/Instructions/Labs/dp900-pbi-06-lab.html)

### Power BI tools and workflow

- Power BI: to build interactive data visualizations
- Typical workflows:
  1. `Power BI Desktop`: from diverge data sources, combine & organise into analytics data model --> reports with interactive visualizations
  2. `Power BI service`: cloud service for publishing Power BI reports --> only limited edit & modelling using web browser --> to schedule refreshes of data, share reports, combined into dashboards & apps
  3. `Power BI phone app & web`: for consuming published reports

### Power BI in Microsoft Fabric

- In Fabric, Power BI lives in shared workspaces --> depend on the same `OneLake` storage
- Related Fabric services:
  - `Workspaces`: shared environments for collaborated reports & semantic models --> no need for `Power BI Desktop`
  - `Semantic models`: defines measures, relationships & hierarchies for one or more reports
  - `Direct Lake mode`: directly query data from `OneLake` files
  - `Web-based report editing`: create & update reports entirely in browser

### Copilot in Power BI

- Available in both destkop and Power BI service, but require Fabric capacity (> F2) or Power BI Premium (> P1) 
- Available service
  - **Summarise a report**
  - **Create a report pages & charts** based on user data & prompt
  - **Generate DAX measures** from natural language description
  - **Smart narrative visual** (don't need Copilot license): generate text summary describes the data in the report, which updates dynamically as data changes

### Semantic models

- Is analytical models to support analysis
  - Defines the numeric values for analyze/report as `measures` (aka `Facts`)
  - Entities to aggregate them by as `dimensions`
  - E.g. Sales as `fact`, while Customer, Product & Time as `dimension`
  - If dimention table further related to other detail tables --> `Snowflake schema`. Otherwise it's `Star schema`
- Hierarchies: defines the level of aggregation over a dimension
  - e.g. **Time** dimension, hierarchy may group days into months, and months into years

### Considerations for data visualization

- Common data visualization
  - Tables & text: simplest --> when numerous related values must be displayed
  - Bar and column charts: compare numeric values for discrete categories
  - Line charts: compare categorized values & examine trends, often over time
  - Pie charts: compare values as proportions of a total
  - Scatter plots: compare 2 numeric measures & identify relationship/correlation between them
  - Maps: compare values for different geographic areas/locations
- In Power BI, visual elements for related data are automatically linked (e.g. select a category in one visualization will automatically highlight that category in related visualizations in the report)
- AI-power visualization
  - **Smart narratives**
  - **Q&A visual**: user ask questions in plain English --> chart answers based on the semantic model
  - **Key influencers**: identify factors most strongly drive selected metric, then display as an interactive visual
  - **Decomposition tree**: interactive drill-down across multiple dimensions --> what is contributing to a value
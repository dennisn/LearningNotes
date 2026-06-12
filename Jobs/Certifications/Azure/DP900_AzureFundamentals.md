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
  - `Parquet`: columnar data format de facto standard for modern data lakehouse: store column data together in row groups --> for nested data types with efficient compression & encoding schemes
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

### Core concepts

- `SQL` or *Structured Query Language* --> popular dialects
  - `T-SQL` (Transact-SQL): for Microsoft
  - `pgSQL`: implemented by PostgreSQL
  - `PL/SQL` (Procedural Language/SQL): by Oracle
- SQL statements are grouped into 3 main group
  - `DDL` (Data Definition Language): manage table, stored procedures, views, etc.
  - `DCL` (Data Control Language): manage access
  - `DML` (Data Management Language): manipulate & retrieve rows in tables
- Level of normalization (Normal Forms)
  1. Cells must hold single, atomic value
  2. Remove partial dependencies (i.e. non-key columns depend on the *whole* primary key - no partial dependencies)
  3. No transitive dependencies (i.e. non-key columns depend *only* on the primary key, not on onther non-key columns)
     - Example: "Employee" tables with key "EmployeeId", have column "DepartmentId" and "DepartmentName" --> "DepartmentName" depends on "DepartmentId", so it shouldn't be in "Employee" table

### Azure SQL services & capabilities

1. SQL Server on Azure VMs: IaaS, replicate the on-prem. database --> can scale up by allocate more resources
2. Azure SQL Managed instance: can have multiple databases, less administrative
3. Azure SQL Databases: fully managed, high-availability, can scale up & down quickly --> can pool resources to share among multiple databases, other cloud-benefits (e.g. replicated to different region, security & auditing tracks, point-in-time restore, etc.)
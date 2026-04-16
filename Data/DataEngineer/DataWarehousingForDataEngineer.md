# Data Warehousing for Data Engineer

- Course URL: <https://app.pluralsight.com/ilx/video-courses/data-warehousing-data-engineers/course-overview>

## Data Warehousing Design and Architecture

- Data Warehouse: data management system designed for business intelligence activities, especially analytics
  - A centralized repository of data from multiple sources, integrated via ETL (Extract-Transform-Load) together --> for querying & analysis (OLAP), not transactional (OLTP)
- Purpose:
  - Intergration --> unified view
  - Time-variant --> to track trends/patterns over time
  - Non-volatile storage --> often not changed after import
  - Improve data-quality --> cleaning as part of ETL
  - Support business intelligence tools
  - Optimised for retrieval & access --> handle large data volumes & varieties of data

### Components

1. Data sources: both internal & external of organisation, documents, and social media data etc.
2. ETL processes: extract data from sources; cleansing and converting it into a consistent format; and load
3. Data storage
   - Relational databases
   - Data marts: subsets of data warehouses tailored for the needs of specific departments
   - OLAP cubes: pre‑aggregate data to facilitate complex queries and multidimensional analysis
   - Cloud storage --> for scale
4. Analysis tools
   - Reporting tools --> generated consolidated reports from data in warehouse
   - Data mining tools --> discover patterns & relationships in large data sets
   - OLAP tools: deisgned specifically for querying & analyzing multi-dimensional data
   - Predictive tools
   - Dashboards --> display key metrics & trends in real-time
5. Meta-data management --> help users understand the data and its context
   - Store data descriptions & its sources, transformation & storage structure
6. Data governance: roles, policies, standards & processes
   - Ensure data quality
   - In compliance with regulations & internal policies

### Design

- `Dimensional modeling` --> aim for making queries simpler and faster
  - Combine **FACT** (quantified data, such as measurement, metrices, etc.) with **DIMENSIONS** (e.g. attributes to FACT, such as time, location and product details)
- `Star schemas`: **FACT** table at the center, linked to **DIMENSION** tables surround it
  - **DIMENSION** tables: contains attributes that describe the facts and are used for filtering and aggregations
  - Example: "Fact": Sales table, "Dimenion": Time, Product, Payment, Customer and Employee

    ```mermaid
    ---
    title: Snowflake example
    ---
    mindmap
      root((Sales))
        id(Time)
        id(Payment)
        id(Client)
        id(Product)
        id(Employee)
    ```

- `Snowflake schemas`: Similar to `Star schemas`, but some dimension tables are connected to other dimension tables
  - Different to "Star schemas": dimension data is normalized --> more joins are required --> more complex query

    ```mermaid
    ---
    title: Snowflake example
    ---
    mindmap
      root((Sales))
        id(Time)
        id(Payment)
        id(Client)
          City
        id(Product)
          Category
        id(Employee)
          Department
    ```

- Key differences:
  - Star: better performance, easier to understand & maintain
  - Snowflake: less torage cost & higher data integrity <-- data more normalized

### Performance optimization

- Main aim: faster retrieval, yet control cost
- Main techniques:
  - **Indexing**: increase storage, overhead cost (e.g. writing & maintenance) --> require on-going monitoring & adjustment to maintain efficiency
  - **Partitioning**: improve retrieval & storage (e.g. different storage strategy/devices for different partition) --> complex, with increase maintenance cost (e.g. each partition may need to be managed & tuned individually)

### Big Data technologies

- Big data: **3 Vs**
  - `Volume`: very large data size (e.g. social media, e-commerce, IoT, etc.)
  - `Variety`: handle structured, semi-structured and unstructured data (e.g. text, images, video, logs, etc.)
  - `Velocity`: real-time processing of streaming data
- Characteristics:
  - Horizontal scaling: adding more nodes to the cluster --> more flexible & cost effective
    - Often use commodity hardware: cheaper
    - Distribute data storage & processing: more fault tolerance yet lower cost (e.g. Hadoop)
  - Support advanced analytics & machine learning directly on stored data --> no need to moving data to analytics platform (e.g. Spark)
  - Real-time processing: for time-sensitive decisions (e.g. Kafka, Storm)
  - Integrate data from diverse sources (e.g. Apache Flume, Sqoop)
- Benefits:
  - Data discovery: across more complex datasets
  - Data visualization

### Cloud-based data warehousing

- Advantage: scalability, cost & ease of maintenance
  - Ease of scale
  - Cost effectiveness: lower initial setup cost
  - Maintenance & upgrade: often managed ty provider
  - Accessibility & integration: accessible everywhere, and often easily integrate with other cloud services
  - Performance: can handle large data & complex queries effieciently
- Challenges: security, cost management & vendor lock-in
  - Data security & privacy: storing off-site
  - Depend on internet connectivity
  - Cost unpredictable
  - Vendor lock-in
  - Complexity in data management

## Data Warehousing Platforms

- Setup data warehousing: planning --> data modeling --> data integration, transformation and loading --> automation & optimization --> testing & deployment --> maintenance & monitoring
  1. Planning: define objective & scope, select technology, and create detailed project plan with timelines
  2. Data modeling: Develop high-level conceptual model --> logical model --> physical model: tailor & optimized for the chosen db management system
  3. Data integration, transformation & loading (ETL)
  4. Automation (e.g. schedule ETL jobs using Airflow) & optimization (e.g. tuning db with indexing & partitioning)
  5. Testing & deployment: validate the data warehouse meets all business requirement
  6. Maintenance & monitoring

### Leading solutions

- Amazon Redshift
  - Fully managed cloud data warehouse
  - Key features:
    - Scalability: from 100 GB to Peta-bytes
    - Complex queries optimization using columnar storage, data compression & zone maps
    - Strong integration with other AWS services
    - Comprehensive security: encryptions, VPC (virtual private cloud), identity & auditing
    - On-demand & reserved pricing
  - Use cases: large scale data anlytics, data warehousing for data lakes
- Google BigQuery
  - Serverless, multi-cloud data warehouse that run superfast SQL queries
  - Key features:
    - High scalability
    - Fast distributed queries
    - Strong integration with Google Cloud
    - Robust security
    - Pay-as-you-go with resource caps
  - Use cases: real-time analytics, machine learning & large-scale data processing
- Ms Azure Synapse Analytics
  - Analytics service for immediate BI and machine learning needs
  - Key features:
    - Integrate analytics (e.g. combining big data and data warehousing in a single service)
    - Massively parallel processing --> fast complex queries
    - Strong integration with Azure
    - Advance security
    - Pay-as-you-go & reserved capacity
  - Use cases: advanced analytics & BI, big data processing
- Snowflake
  - Cloud-based data warehousing platform for fast & scalable data processing across multiple cloud environments
  - Key features:
    - Integrated analytics for structured & semi-structured data format
    - Massively parallel processing
    - Multi-cloud support --> avoid Vendor lock-in
    - Advanced security
    - Flexible pricing
  - Use cases: data warehousing & analytics, data lakes, ML & data science, and real-time data processing

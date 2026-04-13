# Data Modeling for Data Engineers

- Course URL: <https://app.pluralsight.com/ilx/video-courses/data-modeling-data-engineers/course-overview>

## Conceptual and Logical Data Modeling

- Data types: structured, semi-structured and non-structured
  - **Structured**: often in tabular form, with a data model (e.g. relational Db, Excel, etc.)
  - **Semi-Structured**: with some kind of structure, but no data model (e.g. Xml, Json)
    - Self-describing structure: tag/marker with strict hierarchies of values & fields --> provide semantics meanings
  - **Non-Structured**: without predifined structure, nor predefined model  (e.g. audio, video, NoSQL Db)
- Data model types: Conceptual vs. Logical vs. Physical
  - **Conceptual**: high-level overview --> for business stakeholders, manager
  - **Logical**: more logical details --> for data users (engineers, analysts)
  - **Physical**: low level design --> for Db developer & admin --> platform -specific, optimise for performance & storage

### Conceptual data model

- Provides a simplified picture of the business workflows within the organisation --> use easy-to-understand terms, for non-technicals
  - Describe main attributes of entities, and their relationships
- Aims: a tool to communicate & clarify business workflows between technicals & non-technicals
  - Explore hidden requirements, un-documented assumptions

### Logical data model

- Evolve from conceptual model, but fill out the details
- Aims: understand business requirements and how to translate them into efficient data model
- Benefits:
  - Enclose gaps & issues --> quality assurance test
  - Iteration & fine-tuning continuously --> Agile data modeling
  - Blueprint for physical model
- Key steps:
  1. Identify attributes of entities
  2. Identify candidate keys (i.e. attributes/set of attributes uniquely identify a specific entity)
  3. Choose primary key
  4. Apply normalization/denormalization
  5. Set relationships between entities --> may break entity into smaller entities to reduce relationship complexity
  6. Identify the relationship cardinality (e.g. 1-1, 1-*, *-*)
  7. Iterate & fine-tune

### Normalization & Entity-Relationship Diagrams

- **Normalization**: a systematic process to minimize redundancy & ensuring data integrity
  - Process: divide large, complex table into smaller, related tables
  - Aims: reduce/eliminate redudancy --> improve data integrity
- Often target 3rd Normal form (3NF)
  - **1NF**: each table cell contains a single value, each row has unique identifier (i.e. primary key)
  - **2NF**: Eliminates `partial` dependencies --> every non-key column must depend on the entire primary key (relevant for composite keys)
  - **3NF**: Eliminates `transitive` dependencies --> non-key columns depend only on the primary key and not on other non-key columns.
- **Entity-Relationship diagrams**: describe how entities relate to each other within the system
  - Entity === Noun
  - Relationship === Verb
  - Attributes === Entity's properties

## Physical Data Modeling and Database Design

- Key steps:
  1. Choose the platform
  2. Translate logical entities into physical tables
  3. Establish relationships
  4. Apply normalization/denormalization
     - OLTP (Online Transactional Processing): production data --> 3NF
     - OLAP (Online Analytical Processing): analysis of large scale historical data --> de-normalized to eliminate joints & improve performance
  5. Apply table constraints --> ensure integrity
  6. Create indexes and/or partitions --> increase efficiency
  7. Extends with programmatic object (i.e. stored procedures, triggers, functions, etc.)
- Main aim: ensure efficiency, optimal performance, and scalability

### NoSQL dabase

- To store non-structured/semi-structured data --> increasingly needed
- Key characters:
  - flexible schemas --> mostly not support ACID transactions
  - horizontal scaling (i.e. add machines to increase capacity)
  - fast queries: by eliminate complex `JOIN` & optimize data for specific access patterns
- Types:
  - Document: such as JSON, XML
  - Key-Value: similar to dictionary or hash map
  - Wide-columns: with table & rows, but columns is not dynamic (i.e. rows may have different # of columns)
  - Graph database: store data as nodes (e.g. people, places, things, etc.) and edges (e.g. information about relationship between nodes)

### Optimization techniques

#### Table partition

- split one into multiple smaller sub-tables --> reduce the scanning time

#### Index

- Index types: column-store vs. row-store, clustered vs non-clustered
- Clustered index: used to sort the physical data --> at most **1 clusterd index**/table, often the primary key --> stored with the data
- Non-Clustered index: stored separately from data, can have more than **1 non-clustered index**/table --> need extra storage, normally slower (i.e. need to perform look up)
- Columnstore indexes: store data by column --> higher compression --> **faster analytical** queries that scan a few columns only on large Db, but **slower** on small Db, and bad at small/frequent inserts/updates
  - Clustered index can be one of rowstore or columnstore
  - But rowstore table can have non-clustered column store indexes, and vice versa
- Cost of indexes:
  - Space: for storage
  - Time: for Insert/Update/Delete statements
- Design choice:
  - Workload type: Rowstore for OLTP vs. columnstore for OLAP
  - Filtering conditions: focus on columns used in `WHERE`, `HAVING`, `JOIN`
  - Column data type: less costly on integers vs. text/variable string (i.e. more variants, also slower arithmetic operation)
  - Data selectivity: index on column with large cardinality (i.e. more distinct values)
  - Column order matters: multi-columns index --> sort by ordered of columns

## Data Modeling in Different Contexts

- ACID:
  - Atomicity: as a single unit
  - Consistency: db in valid state after finishing (irrespective of success or failure)
  - Isolation: valid state even with concurrent transactions
  - Durability: once committed, the changes are saved (i.e. not loss)

- OLTP (Online Transactional Processing): for high volume of transactions (i.e. "WRITE" heavy)
  - Often normalized to 3NF --> faster "change-processing" (e.g. Insert/Update/Delete)
  - Convenient for ad-hock queries
  - Focus on data integrity
- OLAP (Online Analytical Processing): focus on aggregated data (i.e. "READ" heavy)
  - Can be de-normalized to increase aggregation performance
  - Data often from multiple source --> collected into some repository
  - Repository: data are transformed/cleaned into a central location (single source of truth)
    - Transform data: to address data anomalies, cleaning missing/dirty data, convert to suitable format

### Big data

- Recommend data modeling strategy:
  - Key characteristics: understand the structure; schema on read vs. write
  - Data architecture requirement: centralized vs. de-centralized; batch vs. real-time
  - Business need: key requirements & constraints, reporting needs
  - Modeling approache: relational vs non-relational vs dimensional (i.e. OLAP), or a combination
  - Technology stack: matching modeling approach ?

- Implementation notes:
  - Data profiling & cleaning --> improve data quality
  - Data governance: for data management in the whole organisation
    - Roles/responsibility
    - Data dictionaries
    - Standardise data formatas & naming convention
  - Scalability & performance: ensure performance is not reduced when data size growth
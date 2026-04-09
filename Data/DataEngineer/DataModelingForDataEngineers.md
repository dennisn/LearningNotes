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

## Data Modeling in Different Contexts

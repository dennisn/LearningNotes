# Big Data for Data Engineer

- Course URL: <https://app.pluralsight.com/ilx/video-courses/big-data-engineers/course-overview>

## Understanding the Fundamentals of Big Data

- Big Data: datasets that are too large or complex to be dealt with by traditional data processing application software
  - Structured data: highly organized, following specific format (e.g. CSV, excel file, etc.)
  - Semi-structured data: some organizational properties (i.e. tags, markers, hierarchy, etc.) --> XML, JSON, Log, NoSQL
  - Unstructured data: no defined structure/format, can't fit into traditional db/tables --> text, images, video, audio
- The five "V" of Big Data
  - Volume: Large amount of data
  - Velocity: speed of data generation & processed
  - Variety: structured vs. semi-structured vs. un-structured
  - Value: importance & usefulness of data
  - Veracity: reliability & accuracy of data
- Opportunities
  - Generate insights
  - Drive new innovation
  - Personalization
- Use cases in Technology
  - Business Intelligence & analytic:
    - Generate actionable insight --> enhance decision making & strategic planning
    - Real-time data analysis & reporting
  - Cybersecurity:
    - Thread detection & prevention
    - Ability to identify vulnerabilities & mitigate risks
  - IoT: integrate & analyse data from IoT device
  - Data science & machine learning: build predictive models, handling complex tasks & processes
- Challenges: mostly come from the five "V" above
  - Additional challenges: privacy & security, infrastructure & scalability, and skills shortage

## Introducing Big Data Technologies & Ecosystems

### Principle of designing a scalable architecture

- Ingestion: batching processing vs. streaming vs. event-drivent
- Storage: distributed file systems, NoSQL and cloud storage
- Processing: batch vs stream
- Visualization: graphical representation for easy consumption --> focus on clarity
- Governance: framework for managing data --> focus on define roles & responsibilities, standards

### Key technologies

- Four major points: storage, processing, streaming & management
- Data lake: flat architecture, which can store raw data at scale (structured & un-structured)
  - Using tools to assign unique identifiers/tags to data elements --> subset of relevant data is queried to analyse a business question
- Data processing
  - Apache Spark: distributed processing of data --> parallel
  - Map-Reduce: break down the processing task into smaller, manageable chunks that can run simultaneously on different machines
- Stream:
  - Kafka: high throughput, distributed messaging system --> scalable real-time data pipelines
  - Flink: low latency, real-time processing with state management --> good for both batch & streaming
- Data management:
  - NoSQL: ability to handle both structured & un-structured data

### Examples

- Storage
  - Data lakes: S3, Azure Data Lake, Google cloud storage --> transform to data warehouse: AWS Redshift, Azure Synapse, Google BigQuery
  - Data Lake-House: DataBricks LakeHouse: combine data lake + data warehouse
- Data Management: MongoDb (JSON-based), Cassandra (Wide-column format), Redis (In-memory storage)

## Understanding Big Data Analytics and Insights

- Techniques in Big Data analytics
  - Data wrangling & preprocessing: transforming and mapping the raw data that you have into a more useful format
    - How: data collection --> cleaning --> transformation --> organizing
    - Why: ensure quality & integrity
  - Data mining: discovering patterns & relationships in large datasets
    - How: clustering, classification & anomaly detection
    - Why: uncover hidden patterns, predict trends
  - Predictive analytics: analyze historical data and making predictions about future events or outcomes
    - How: regression analysis & time series analysis
    - Why: pro-active decision making, reduce risk & optimized operations
  - Machine learning: learn from data to make predictions or decisions
    - How: Supervised/Unsupervised learning, Re-inforced learning
    - Why: automate analytical model building, handle "very" complex data/model
- Ethical consideration & challenges
  - Privacy: data anonymization, consent management, data breaches
  - Security: encryption, access control, incident response
  - Bias & fairness: algorithmic bias, representation, fairness in decision-making
  - Transparency & accountability: explainability, auditability & regulatory compliance

# ETL Processes and Tools for Data Engineer

- Course URL: <https://app.pluralsight.com/ilx/video-courses/etl-process-tools-data-engineers/course-overview>

## Fundamentals of ETL Processes and Tools

- ETL = Extract-Transform-Load: collect data from various sources, cleaning & organizing it into a consistent format in a central system
  - Aim: make it easy to use and make smart decisions
  - Transform: include `filtering` (remove unnecessary data points), `cleaning` (remove inconsistencies) and `merging`
- Tools
  - Traditional (on-prem): Informatica, IBM DataStage and Talend (open-source)
  - Modern (Cloud-based): AWS Glue, Azure Data Factory, Google Cloud Dataflow --> managed services
- Challenges in ETL process
  - Data inconsistency: diverse format, varying logic & non-standard naming
  - Data loss: Schema change, network failure or in-adequate error handling (not enough log files, data not saved correctly)

## Designing and Implementing ETL Solutions

- ETL Pipeline: set of automated processed that perform ETL from end to end
- ETL pipeline design steps:
  1. Requirement analysis
  2. Data extraction design: data sources & access credential
  3. Data transformation design
  4. Data loading design
  5. Testing & validation
  6. Deployment: deploy the pipeline to production environment
  7. Monitoring & maintenance

## Managing and Optimizing ETL Processes

- Logging for audit & identify error
- Parallel tasks to increase performance
- Future trends
  - ELT: loading data before transform --> for performance increase ==> Using data warehouse for processing, so lower latency
  - Machine learning & AI
  - Cloud & hybrid solution
  - Real-time data integration
  - Predictive integration system
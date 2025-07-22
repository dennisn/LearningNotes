# Some basic Software Engineer principle

  1. [Object-oriented](./ObjectOriented.md)
  1. [Microservices](./Microservices/README.md)
  1. [Security](./Security.md)
  1. CI/CD
     - [Jenkins](./CI_CD/Jenkins.md)
     - GitHub Actions
  1. [Blockchain & Crypto-Currency](./BlockchainFundamentals.md)
	
## Misc.

### Clustered vs Non-Clustered index

The main differences between clustered and nonclustered indexes are: 
  - **Storage**: Clustered indexes determine the order of rows in a table, so they don't require additional disk space. Nonclustered indexes are stored separately from the table, so they require additional storage space. 
  - **Speed**: Clustered indexes are faster for retrieving large ranges of sequential data. Nonclustered indexes are faster for retrieving small sets of data or for sorting and aggregating data. 
  - **Number of indexes**: There can only be one clustered index per table, but you can create multiple nonclustered indexes on a single table. 
  - **Usage**: Clustered indexes are often used in tables where the data is frequently accessed in a range of values or in a sorted order. Nonclustered indexes are often used to improve the performance of frequently used queries not covered by the clustered index

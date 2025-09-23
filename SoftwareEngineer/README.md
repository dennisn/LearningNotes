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

## Software Architect
  - Architect vs Team lead: architect focuses on making the product better vs making their team better
    + Architecture is about the important "things", which are changing overtime, but have long-term impact & implication of a piece of software
  - Tools: 
    + Mind-map: divide a complex problems, yet keep track of the overal view
    + Pareto principle (80/20 rule): maximise effects with limited resources, or identify "hard" problem with minimal returns
  - Conway's Law: software structure is reflective of organisation "communication" structure
    + Implication: solution should fit business culture
    + However, software solution can be used to redesign the structure of the organisation
  - Architect vs Engineer: architect focus on satisfying needs vs. engineer on detecting & solving problems
    + Pro-active vs re-active mindset
  - Normal architect workflow: (1) detect the need --> (2) possible approaches, main problems, market need --> (3) resources & technical feasibility --> optimal solution
    + Need information of all kinds for (2), experience of other architects
  - Domain: each have its own rules, needs & perculiar --> can't just focus on technically correct solution
    + Also other needs like values over times, iterative development
  - Architectural patterns: focus on structure, communication & operation of the entire software system (vs. design patter focus on code). Common patterns:
    + Layered Architecture: Distributes roles and responsibilities hierarchically, ensuring effective separation of concerns.
    + Event-Driven Architecture: Centers on capturing, communicating, processing, and persisting events, like in Node.js.
    + Microkernel Architecture: Separates a minimal functional core, which can be used as base for more complex "plugin" on top --> quick & drastic changes can be made
    + Microservices Pattern: Uses small, independent services to avoid issues like cascading failures and scalability problems in monolithic systems.
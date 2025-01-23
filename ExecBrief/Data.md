# Data

## Big data
  - With better tech., and lower cost, more data are collected (into Data Lake)
  - Collected data can be structured (e.g. relational data), semi-structured (json, xml) or no structured
  - Hadoop: revolutionised big data processing, by process them in parallel, then merge the result together (Map-Reduce)
  - Many new commercial platforms provide service to process big data
  
## Data in the cloud
  - Offer many benefits, but also some constraints
  - Hard to choose because of so many different services/offerings
  - Try to avoid "Lift & Shift" (e.g. simply existing things from on-premise to cloud), as it is the simplest but least beneficial
    - Differences between cloud & on-premise need a rethink of how to solve the requirement
  - Some challenges:
    1. Access: access to raw data (for SaaS solution), control of server settings, update & integration with other system
	2. Security: more exposure to the internet --> more risk
	3. Regulation: where the physical data center
	4. Talen gap: differences skills needed for on-premise vs cloud

## Blockchain
  - Start of from a whitepaper for crypto-currency
  - Now mainstream use is for creating distributed ledgers

### Distributed ledgers
  - Block of data, chain together in **fixed ordered**.
  - Each block of data use hash to ensure data integrity: not prevent tampering but ensure any changes will make that block, or its data record, invalid
  - Ledger is distributed, and achieve the source of truth via concensus --> prevent both malicious or unintentional corruption, and allow recovery from such events

==> **USAGE**: distributed database, for example: sharing information between players in supply chain management, providers in healthcare

### Smart contract
  - A further use of distributed ledgers
  - Instead of "block of data", we have "block of contract", where contract define the action to be taken when certain conditions are met: date time, stock price, a transaction completed, etc.
  - Platforms are built to allow these smart contracts to be built & use by users
  
## Quantum computing
  - bits vs. qubits: instead of represent 1/0, now represent 1/0/superstate
    - super-state: between state where there are possibility of 0 or 1, but not yet decided
  - Should be good at solving certain hard problem for classical computing: some of intractable problems
  - Intractable problems: problems we know how to solve, but required resources/time make it impractical
    - factor large number in breaking encryption
    - "traveling saleman problem" in optimisation
  - Quantum interference and entanglement: properties of quantum physics that help with solving problems
    - interference: reinforce or cancel each other out
    - entanglement: states of multiple qubits are linked
  - Some practical impacts: 
    - Research on how to use quantum computer for encryption
    - Research on how to use classical computer for encryption that can't be craced by quantum computer
  - Still some time to comes when quantum computer can actually be used in problems that they are best for, **alongside** classical computer
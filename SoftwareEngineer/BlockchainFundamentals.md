# Blockchain Fundamentals

## Concept
  - Using cryptography & distributed network to ensure transparent & tamper-free transactions ==> ensure trust between parties participate in transactions
  - public vs private: similar to distributed storage shared between everyone/community vs. group of organisations
  - Permissioned vs Consortium
    + Permissioned: need invitation, may not have the same access rights
    + Consortium: for organisation, with shared responsibility
  - Characteristics: 
    + Global singleton
    + Non-stoppable
    + Accessible: anyone can access it
    + Verifiable: anyone can verify the transactions on it
  - Hashing: to **quickly** verify that the content **hasn't been changed**
    + Content is not exposed (i.e. very hard to work out the content from hash value)
    + Nounce: single use value that can be added to content to control the format of the hash value (e.g. starts with "ff")
    + Merkel Tree: using hash of hashes --> can use 1 hash to verify that multiple hashes are still consistent
  - The block structure: 
    + block no: to order blocks in chain
    + message
    + Nounce
    + Hash
    + Timestamp
    + Previous
  - Proof of work vs proof of stake
    + Proof of work: low initially, but becomes extremely high now --> inefficiency
    + Proof of stake: replace proof of work, to ensure everyone acting nicely 
  - Data: blockchain store the transactions --> need to re-run the tranactions to know the current state
    + Everyone has access to the chain can see the data --> to keep content private, have to obsfucate the id, or encrypt the actual content
  - Forks: change in blockchains
    + Soft: as a software upgrades, to add new functionalities/fixes --> backward compatible
    + Hard: breaking changes, major disputes in the community --> often resulted in separated chains
  
### Acquired blockchain
  - Mining: not so feasible now
  - Buying through unregulated market: may unwittingly participate in money laundering
  - Buying through an exchange: need sign up and verifying identity
    + Need care of picking the exchange, as your private info. has to be provided

### Usages:
  - Internet of things: secure transaction between devices
  - Product lifecycle: attached information about the product throughout its lifecycle
  - Certification: issue certifications in tamper-free manner
  - Secure information sharing: similar to certification, but can also used for sharing of sensitive info. such as health information
  - Virtual products: use blockchain to control access to digital products
  - NFT: Non-fungible token: 

When "**NOT**" to use Blockchain
  - When data is shared in a closed-system between known & trusted parties --> add no value, but overhead  

## Smart Contract
  - Similar to physical contract, but run by blockchain network --> no human intervention, scalability
    + can be programmed to self-destructed, but otherwise can run forever (in theory) until there is no fund left to run the contract
  - Smart contract: can hold a balance, read other contract, decide to do action locally, or send money/execute other contracts
  - Gas: payment for node to process (validate/mine) the block
  - Important design point:
    + contract live forever/a long time: need built-in versioning/upgradability
    + Emergency stop: needed for catastrophic failure
    + Composability: build from components --> re-usability & reduce complexity/mistake
  
## Development environment
  - Tools: Winget, NodeJS, VS Code, Chrome, Security configuration, React App, MetaMask
    + getting eth for testing
    ```
    # install NodeJS using winget
    winget install OpenJS.NodeJS.LTS

    # Visual Studio code
    winget install Microsoft.VisualStudioCode -e

    # Chrome
    winget install --id=Google.Chrome -e
    ```
    + Change script execution policy: risky but needed: `set-executionpolicy remotesigned`
    + Create react app tool: `npm install -g create-react-app`
  - MetaMask and "Eth" for testing
    + MetaMask: chrome extensions --> create crypto wallet
    + Show test networks --> join Sepolia test network
    + Test fund: Alchemy faucet (sepoliafaucet.com) or Infura (infura.io)

## Smart Contract Basics with Solidity


## Sample Application: The Globomantics Bodymap
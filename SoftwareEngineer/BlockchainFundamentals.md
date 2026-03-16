# Blockchain Fundamentals

## Concepts

- Blockchain uses cryptography and distributed networks to enable transparent, tamper-resistant transactions.
- The goal is to establish trust between parties participating in transactions.
- Public vs private blockchain:
  - Public: shared across open communities.
  - Private: shared within controlled organizations/groups.
- Permissioned vs consortium:
  - Permissioned: invitation required; participants may have different access rights.
  - Consortium: operated by multiple organizations with shared responsibility.
- Core characteristics:
  - Global singleton.
  - Non-stoppable.
  - Accessible (participants can access network data by policy).
  - Verifiable (transactions can be independently verified).
- Hashing:
  - Used to quickly verify content has not changed.
  - Content is not directly exposed from hash output.
  - Nonce: single-use value added to input to influence hash output format.
  - Merkle tree: hash-of-hashes structure, enabling efficient verification of many records.
- Typical block structure:
  - Block number/index.
  - Message/data.
  - Nonce.
  - Hash.
  - Timestamp.
  - Previous hash.
- Proof of Work vs Proof of Stake:
  - Proof of Work: historically useful, but expensive and inefficient at scale.
  - Proof of Stake: alternative consensus model designed to reduce resource usage.
- Data model:
  - Blockchain stores transactions.
  - Current state is derived by replaying/processing transaction history.
  - If data privacy is needed, use obfuscation, encryption, or store only references on-chain.
- Forks (changes in blockchain rules):
  - Soft fork: backward-compatible upgrades.
  - Hard fork: breaking changes; can result in separate chains.

## Acquiring Blockchain Assets

- Mining: generally less feasible for individuals today.
- Buying via unregulated markets: higher legal/compliance risk.
- Buying via exchanges:
  - Requires account setup and identity verification.
  - Exchange selection matters because sensitive personal data is involved.

## Usage Examples

- Internet of Things: secure transactions between devices.
- Product lifecycle tracking: append tamper-resistant lifecycle events.
- Certification: issue verifiable credentials.
- Secure information sharing: controlled sharing of sensitive records (for example, health data).
- Virtual products: control ownership/access for digital assets.
- NFT: non-fungible tokens for unique digital items.

## When Not to Use Blockchain

- Do not use blockchain if data is shared in a closed system among trusted parties and no decentralization is required.
- In those cases, blockchain often adds cost and operational overhead without clear value.

## Smart Contracts

- Smart contracts are like digital contracts executed by blockchain nodes.
- They run without manual intervention and can scale across participants.
- Contracts can be programmed with lifecycle controls (for example, pause/stop patterns).
- Smart contracts can:
  - Hold balances.
  - Read from other contracts.
  - Perform local logic.
  - Send funds or invoke other contracts.
- Gas:
  - Transaction fee paid for network computation/validation.
- Important design considerations:
  - Upgradability/versioning for long-lived contracts.
  - Emergency stop mechanism for catastrophic failures.
  - Composability to improve reuse and reduce complexity.

## Development Environment

- Typical tools: `winget`, `Node.js`, `VS Code`, `Chrome`, security configuration, React app tooling, `MetaMask`.
- Setup examples:

```powershell
# Install Node.js LTS using winget
winget install OpenJS.NodeJS.LTS

# Install Visual Studio Code
winget install Microsoft.VisualStudioCode -e

# Install Chrome
winget install --id=Google.Chrome -e
```

- PowerShell script policy (use with caution): `Set-ExecutionPolicy RemoteSigned`
- Create React app tooling: `npm install -g create-react-app`

### MetaMask and Test ETH

- MetaMask:
  - Chrome extension used as a crypto wallet.
  - Enable test networks and connect to Sepolia.
- Test funds/faucets:
  - Alchemy faucet: `sepoliafaucet.com`
  - Infura: `infura.io`

## Smart Contract Basics with Solidity

- Placeholder for Solidity basics notes.

## Sample Application: The Globomantics Bodymap

- Placeholder for sample app notes.

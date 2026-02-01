# Blockchain Ballots

A decentralized voting system built on Ethereum, developed during a hackathon. Voters cast ballots through a React frontend that interacts with a Solidity smart contract, which enforces single-vote semantics, supports vote delegation, and tallies results entirely on-chain.

## Features
- **On-chain vote recording** — ballots are stored on Ethereum, providing a transparent and tamper-resistant audit trail
- **Double-vote prevention** — the smart contract enforces one vote per address
- **Vote delegation** — voters can delegate their voting power to a trusted proxy, with loop protection
- **Real-time tallying** — winner determined directly from on-chain state
- **Event-based audit trail** — `GaveRight`, `Delegated`, `Voted`, and `UpdatedMessages` events emitted for off-chain indexing

## Tech Stack
| Layer | Technology |
|-------|------------|
| Frontend | React, React Router |
| Backend| Flask |
| Blockchain Interaction | Web3.js |
| Smart Contracts | Solidity (0.5.x), Truffle |
| Local Chain | Ganache |
| Hosting | Firebase Hosting |

## Architecture
```
React UI (browser)
  → Web3 provider (Ganache / MetaMask)
    → Ballot smart contract (Solidity)
      → On-chain state + events
```

## Smart Contract Design

The `Ballot` contract manages the full voting lifecycle:

**Data model**
- `chairperson` — deployer address with administrative authority
- `proposals[]` — list of candidates/options, each with a `voteCount`
- `voters[address]` — per-voter record tracking weight, delegation, and vote status

**Core functions**
| Function | Purpose |
|----------|---------|
| `constructor(bytes32[], string)` | Initializes ballot with proposals and sets deployer as chairperson |
| `giveRightToVote(address)` | Grants voting rights to an address |
| `delegate(address)` | Delegates voting power with circular-delegation protection |
| `vote(uint)` | Casts a vote and increments the selected proposal's count |
| `winningProposal()` / `winnerName()` | Returns the current winner from on-chain state |

**Voting flow**
1. Contract is deployed with a list of proposals
2. Chairperson grants voting rights to eligible addresses
3. Voters either vote directly or delegate to another address
4. Contract enforces single-vote semantics and updates counts on-chain
5. Anyone can query the winner via `winnerName()`

## Project Structure
```
.
├── migrations/               # Truffle migration scripts
├── public/                   # Static assets
├── src/
│   ├── abis/                 # Truffle build artifacts (ABI + bytecode)
│   ├── components/           # React UI (Home.js, Voting.js)
│   └── contracts/            # Solidity smart contracts
├── truffle-config.js         # Truffle network + compiler settings
└── firebase.json             # Firebase Hosting config
```

## Local Development

**Prerequisites:** Node.js, npm, Ganache (or another local Ethereum RPC on port 7545), and optionally MetaMask

```bash
# Install dependencies
npm install

# Start Ganache on http://127.0.0.1:7545

# Compile and deploy contracts
npx truffle compile
npx truffle migrate --reset

# Start the frontend
npm start
# UI available at http://localhost:3000
```

## Future Improvements
- Integrate formal identity verification (current UI has a webcam placeholder)
- Add privacy-preserving voting (e.g., zero-knowledge proofs) — votes are currently public on-chain
- Implement end-to-end cryptographic auditability
- Deploy to an Ethereum testnet for broader testing

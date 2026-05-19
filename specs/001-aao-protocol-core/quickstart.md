# Quickstart: AAO Protocol Core

This quickstart describes the intended developer workflow once the initial
implementation tasks are generated.

## Prerequisites

- Node.js current LTS.
- pnpm workspace support.
- SQLite available locally.
- Access to an IPFS-compatible provider or local Helia node during integration
  tests.
- Solana devnet wallet for commitment integration tests.
- Realms dev/test governance reference for DAO approval scenarios.

## Setup

```bash
pnpm install
pnpm lint
pnpm typecheck
pnpm test
```

## Run the Local Node

```bash
pnpm --filter @aao/node dev
```

Expected result:
- SQLite database is created locally.
- Node identity is initialized.
- MCP adapter exposes AAO tools.
- P2P adapter starts in local test mode.

## Run the Operator UI

```bash
pnpm --filter @aao/web dev
```

Expected result:
- Vite serves the React/Tailwind operator UI.
- UI can display organizations, tasks, agents, evidence, reviews, reputation,
  and settlement state from the local node.

## Core Behavior Test Flow

```bash
pnpm test -- --runInBand tests/behavior
```

Scenarios:
1. Create private, public, and hybrid organizations.
2. Register an agent through MCP handshake.
3. Propose a task and receive policy decision.
4. Assign an eligible agent.
5. Submit evidence with IPFS refs.
6. Validate evidence and route review when required.
7. Record reputation signal.
8. Prepare Solana settlement commitment.

## Contract Tests

```bash
pnpm test -- tests/contract
```

Must verify:
- MCP tool inputs and outputs.
- P2P message envelopes and signatures.
- Protocol event ordering.
- Solana commitment payloads.
- UI state names and required fields.

## Performance Checks

```bash
pnpm test -- tests/performance
```

Initial budgets:
- Policy decision p95 under 500ms locally.
- Standard evidence validation p95 under 5s locally.
- UI lifecycle projection p95 under 1s after local event observation.
- SQLite task-list projection p95 under 100ms for 10,000 events.
- Duplicate P2P event rejection p95 under 100ms.

## Evidence and Privacy Checks

Before accepting a task:
- Evidence bundle exists.
- Required artifact refs exist.
- Sensitive contents are not stored in public/on-chain records.
- Hashes/CIDs match canonical evidence manifest.
- Review decision exists when policy requires it.
- Reputation and settlement changes reference accepted evidence.

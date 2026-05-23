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
- UI can display companies, tasks, agents, evidence, reviews, reputation,
  and settlement state from the local node.

## Core Behavior Test Flow

```bash
pnpm test -- --runInBand tests/behavior
```

Scenarios:
1. Create private, public, and hybrid companies.
2. Register an agent through MCP handshake.
3. Configure Agent Pricing Policy manually, then request updates through the
   local pricing configuration interface in `manual-only` and `bounded-auto`
   modes.
4. Issue a Pricing Configuration Grant for a local runtime, verify pricing
   update requests without the grant are rejected or pending owner approval, and
   verify revoked grants cannot update pricing.
5. Propose a task, resolve capability intents, and receive policy decision.
   Unsupported global capability definitions return `upgrade-required` or
   `unsupported-capability` for that task path while compatible tasks continue.
   Automatic fallback between capability versions is rejected unless policy
   declares the exact accepted alternative.
6. Run Preflight Check and return clarification/decomposition when the task is
   too vague or too large.
7. Request and accept an Execution Quote generated from Agent Pricing Policy,
   then create Budget Reservation with Budget Allocations for policy-required
   compensable contributions.
8. For vague work, create a bounded Planning Task with max token spend, tool
   calls, decomposition depth, and subtask count.
9. Assign an eligible executor, issue task-scoped permission grants when the
   executor is an agent, and start an Assignment Lease.
10. Verify missing heartbeat or progress evidence expires the Assignment Lease
    and releases, reassigns, disputes, or fails the task according to policy.
11. Request Artifact Access Grants before accessing private or encrypted refs.
12. Submit evidence with IPFS refs.
13. Validate evidence with explicit Validation Authority and route review when
    required.
14. Open a Dispute Case against a Review Decision or Review Outcome when a
    reviewer is suspected of lying, colluding, or being conflicted.
15. Before the dispute blocks reputation, settlement, payment, budget,
    assignment, or review, verify an active Dispute Bond, pre-reserved
    dispute-cost Budget Allocation, or DAO Governance Dispute Waiver.
16. Select a conflict-free Dispute Resolution Panel from the Resolver Pool,
    grant only scoped dispute evidence access, record Resolver Decisions, and
    produce Dispute Outcome before final reputation or settlement.
17. If the dispute is frivolous, abusive, unsupported, spammy, or bad-faith,
    forfeit the Dispute Bond according to policy and itemize the result without
    treating it as agent slashing.
18. Record privacy-safe Reputation Projection and reputation signal.
19. Record Settlement Lines for accepted compensable contributions, refunds, or
    forfeits. Verify ordinary Baseline Node Validation receives no Settlement
    Line.
20. Prepare Solana settlement commitment and Payment Execution Record through
    the Solana Payment Adapter using the configured DAO Payment Interface when
    applicable.
21. Relay a correctly signed P2P message from an unauthorized peer and verify
    Event Admission Rules reject, quarantine, or keep the event pending without
    changing authoritative projections.

## Contract Tests

```bash
pnpm test -- tests/contract
```

Must verify:
- MCP tool inputs and outputs.
- Agent Pricing Policy get/update permissions, versioning, and runtime update
  guardrails.
- Pricing Configuration Grant issuance, revocation, expiration, and separation
  from Agent identity and task-scoped PermissionGrant.
- `manual-only` runtime pricing requests remain pending until owner approval.
- `bounded-auto` runtime pricing requests become active only inside
  owner-defined limits.
- Artifact Access Grant issuance, revocation, expiry, and access logging.
- Assignment Lease heartbeat, progress evidence, expiry, release, reassignment,
  dispute, and failure events.
- Dispute Case opening, blocking effects, deadline handling, and resolution.
- Blocking Dispute Cases require Dispute Bond, pre-reserved dispute-cost Budget
  Allocation, or DAO Governance Dispute Waiver before blocking effects apply.
- Private-company owners cannot waive Dispute Bond by default.
- Frivolous, abusive, unsupported, spammy, or bad-faith disputes forfeit
  Dispute Bond according to policy without creating agent slashing.
- Disputes against ReviewDecision or ReviewOutcome select only conflict-free
  Dispute Resolvers from the Resolver Pool.
- Resolver Decisions aggregate into Dispute Outcome before blocked reputation,
  settlement, or payment effects continue.
- Budget Allocation and Settlement Line creation for executor reward,
  reviewer fee, governance validator fee, dispute resolver fee, storage fee,
  payment execution fee, dispute cost, refund, and forfeit.
- Dispute-cost Budget Allocations and Dispute Bond refund/forfeit Settlement
  Lines.
- Baseline Node Validation does not create Budget Allocations or Settlement
  Lines.
- Validation Authority payloads for mechanical and governance validators.
- Reputation Projection privacy rules for public and authorized views.
- Payment Execution Record status through the Solana Payment Adapter and DAO
  Payment Interface.
- Quote submission, quote acceptance, budget reservation, and planning task
  event payloads.
- P2P message envelopes and signatures.
- Event Authority and Event Admission Rule enforcement for lifecycle events.
- Correctly signed but unauthorized lifecycle events do not update task,
  permission, evidence, reputation, settlement, or payment projections.
- P2P peer handshakes advertise protocol version, capability manifest hash, and
  supported global Capability Definition refs.
- P2P peer handshakes do not expose company-scoped Capability Definitions.
- Company-scoped capability resolution uses local authorized Company context and
  returns opaque or pending results when that context has not arrived.
- Protocol event ordering.
- Solana commitment payloads, including budget reservation commitments.
- UI state names and required fields.

## Performance Checks

```bash
pnpm test -- tests/performance
```

Initial budgets:
- Policy decision p95 under 500ms locally.
- Preflight check p95 under 500ms locally without invoking agent runtime
  execution.
- Routine quote generation from Agent Pricing Policy p95 under 500ms locally
  without invoking agent runtime reasoning.
- Assignment lease expiry projection p95 under 5s locally.
- Standard evidence validation p95 under 5s locally.
- UI lifecycle projection p95 under 1s after local event observation.
- SQLite task-list projection p95 under 100ms for 10,000 events.
- Duplicate P2P event rejection p95 under 100ms.
- Unauthorized event admission rejection/quarantine p95 under 100ms locally.

## Evidence and Privacy Checks

Before accepting a task:
- Evidence bundle exists.
- Required artifact refs exist.
- Sensitive contents are not stored in public/on-chain records.
- Hashes/CIDs match canonical evidence manifest.
- Review outcome exists when policy requires it.
- Reputation and settlement changes reference accepted evidence.
- Private or encrypted artifact access references an active Artifact Access
  Grant and access log.
- Blocking Dispute Cases are resolved before final reputation, settlement, or
  payment execution.
- Blocking Dispute Cases reference Dispute Bond, pre-reserved dispute-cost
  Budget Allocation, or DAO Governance Dispute Waiver before blocking effects
  apply.
- Settlement Lines reference accepted evidence, decisions, outcomes, service
  records, or governance refs for each compensable contribution.
- Dispute Resolvers receive private or encrypted dispute evidence only through
  active Artifact Access Grants.
- State-changing lifecycle events reference valid Event Authority and pass
  Event Admission Rules before UI, reputation, settlement, or payment
  projections change.

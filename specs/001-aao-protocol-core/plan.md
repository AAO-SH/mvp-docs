# Implementation Plan: AAO Protocol Core

**Branch**: `001-aao-protocol-core` | **Date**: 2026-05-19 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-aao-protocol-core/spec.md`

## Summary

Build the first executable slice of AAO Protocol: a governed lifecycle for AI
agent work across organization setup, agent handshake, task proposal, policy
decision, assignment, evidence submission, review, reputation update, and
settlement commitment. The implementation will be a strict TypeScript monorepo
using Clean Architecture and DDD, with protocol state persisted locally in
SQLite, evidence artifacts addressed through IPFS, P2P propagation through
libp2p, agent/runtime integration through MCP, DAO governance references through
Realms, Solana commitments through Solana Kit, and an operator UI built with
Vite, React, and Tailwind.

## Technical Context

**Language/Version**: TypeScript strict mode on current LTS Node.js. All packages
must use `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, and
ESM-compatible module settings.

**Primary Dependencies**: Jest, libp2p, Helia/IPFS, Solana Kit, Realms
Today/SPL Governance references, MCP TypeScript SDK, SQLite, Vite, React,
Tailwind.

**Storage**: SQLite for local node state, projections, audit indexes, and
outbox/inbox queues. IPFS/Helia for content-addressed artifact and evidence
references. Solana records commitments only, never raw sensitive data.

**Testing**: Jest for BDD/TDD, unit tests, contract tests, integration tests,
and performance-budget checks. Test suites must begin with failing behavior
tests for domain and policy changes.

**Target Platform**: Local AAO node process, browser operator UI, external agent
runtime adapters over MCP, libp2p network peers, IPFS storage providers, and
Solana governance/commitment integrations.

**Project Type**: TypeScript monorepo with protocol packages, node runtime,
operator web app, adapter packages, contract documentation, and tests.

**Performance Goals**: 95% of valid task proposals receive policy decision plus
assignment/rejection in under 30 seconds in controlled evaluation; UI state
changes appear within 1 second after local node observes an event; evidence
validation for standard bundles completes in under 5 seconds locally; P2P event
deduplication keeps duplicate processing below 1% in test networks.

**Constraints**: No sensitive company data on-chain. Domain rules remain
framework-independent. Solana commitments occur only after accepted validation
path. IPFS stores encrypted or privacy-safe artifacts where needed. Agents are
untrusted by default. Runtime adapters must not bypass policy or evidence gates.

**Scale/Scope**: Initial slice targets single-node and small test-network
operation: up to 100 agents, 1,000 tasks per organization, 10,000 lifecycle
events, and 1,000 evidence bundles in local evaluation.

**AAO Zones Affected**: Runtime, Agent Adapter, P2P Network, Company Registry,
Policy & Governance, Project Execution Graph, Workspace, Storage, Secure Node
Environment, Core Execution & Proof Pipeline, Solana Layer.

**Governance Model**: Public Company, Private Company, and Hybrid Company. DAO
approval and review references integrate with Realms Today/SPL Governance.
Private owner and hybrid partner/C-level/member rules are modeled in the policy
engine.

**Evidence Model**: Evidence bundles include task id, organization id,
requester id, policy decision id, executor agent id, artifact refs, workspace
snapshot refs, logs/summaries, hashes/CIDs, validation result id, review refs
when required, and settlement commitment refs when produced.

**On-chain/Off-chain Boundary**: Solana stores commitments to policy, evidence,
review, reputation impact, and settlement outcome. SQLite and IPFS store local
state, encrypted/private artifacts, indexes, manifests, snapshots, and detailed
evidence records. Realms proposal/vote ids are referenced as governance inputs.

**Bounded Contexts & Domain Model**: Company Registry, Agent Registry, Policy &
Governance, Task Execution Graph, Evidence & Validation, Review, Reputation,
Settlement, Workspace, Storage, Secure Node, P2P Propagation, Runtime Adapter,
Operator UI.

**Architecture Pattern**: Clean Architecture. `domain` owns entities, value
objects, domain events, invariants, and state machines. `application` owns use
cases and ports. `adapters` implement MCP, libp2p, IPFS, SQLite, Solana/Realms,
and UI integration. `infrastructure` owns concrete runtimes, configuration, and
provider wiring.

**Testing Standard**: Jest for JavaScript/TypeScript. Each user story requires
Given/When/Then behavior tests, domain unit tests, adapter contract tests,
integration tests for lifecycle paths, and targeted performance checks.

**UX Consistency Requirements**: UI uses the same domain terms as specs and
audit records. Task states are `proposed`, `blocked`, `approved`, `assigned`,
`running`, `evidence-submitted`, `needs-review`, `validated`, `accepted`,
`rejected`, `settlement-pending`, `settled`, `slashed`, and `failed`.

**Performance Budgets**: Local policy decision p95 <500ms for standard tasks;
standard evidence validation p95 <5s; UI lifecycle update p95 <1s after local
event observation; SQLite query p95 <100ms for task list projections up to
10,000 events; Solana commitment preparation p95 <2s before wallet/signing
latency; P2P duplicate event rejection p95 <100ms.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **Evidence-first execution**: PASS. Evidence bundle fields, artifact refs,
  validation results, review refs, and settlement commitments are part of the
  domain model and contracts.
- **Policy-driven autonomy**: PASS. Every task proposal receives a policy
  decision before assignment; autonomy, permission, review, and settlement rules
  are explicit.
- **Verifiable reputation**: PASS. Reputation signals derive only from accepted
  validation/review/settlement events.
- **Human + AI governance**: PASS. Public, private, and hybrid governance
  authorities are modeled, including Realms references for DAO governance.
- **On-chain settlement, off-chain privacy**: PASS. Solana records commitments;
  SQLite/IPFS keep detailed and sensitive data off-chain.
- **Secure node boundaries**: PASS. Secure node environment is a bounded context
  with identity, wallet, secrets, permissions, and signer ports.
- **Verification coverage**: PASS. BDD, Jest, contract, integration, and
  performance checks are required for lifecycle, adapter, storage, and Solana
  boundaries.
- **Clean code and modularity**: PASS. Monorepo is organized by DDD contexts and
  Clean Architecture layers with explicit ports/adapters.
- **Clean Architecture and DDD**: PASS. Domain rules are isolated from SQLite,
  libp2p, IPFS, MCP, Solana, Realms, and UI frameworks.
- **BDD/TDD with Jest**: PASS. Jest behavior tests are mandatory before
  implementation.
- **UX consistency**: PASS. State names and terminology are fixed across UI,
  logs, specs, evidence, and audit records.
- **Performance as contract**: PASS. Initial budgets are declared and will be
  verified in tests.

Post-design re-check: PASS. Research, data model, contracts, and quickstart
preserve all constitutional gates. No violations require Complexity Tracking.

## Project Structure

### Documentation (this feature)

```text
specs/001-aao-protocol-core/
|-- plan.md
|-- research.md
|-- data-model.md
|-- quickstart.md
|-- contracts/
|   |-- mcp-tools.md
|   |-- p2p-messages.md
|   |-- protocol-events.md
|   |-- solana-commitments.md
|   `-- ui-state.md
`-- checklists/
    `-- requirements.md
```

### Source Code (repository root)

```text
apps/
|-- node/
|   |-- src/adapters/
|   `-- tests/
`-- web/
    |-- src/
    |   |-- components/
    |   |-- pages/
    |   |-- application/
    |   `-- adapters/
    `-- tests/

packages/
|-- core/
|   |-- src/domain/
|   |-- src/application/
|   `-- tests/
|-- adapter-mcp/
|-- adapter-p2p/
|-- adapter-ipfs/
|-- adapter-solana/
|-- storage-sqlite/
|-- ui-contracts/
`-- test-support/

tests/
|-- behavior/
|-- contract/
|-- integration/
|-- unit/
`-- performance/
```

**Structure Decision**: Use a TypeScript monorepo with `packages/core` as the
domain/application center. Runtime and provider integrations live in adapter
packages, while `apps/node` composes the node process and `apps/web` composes
the operator UI. This keeps domain rules independent from frameworks and lets
adapter contracts be tested independently.

## Complexity Tracking

No constitutional violations. The number of packages is justified by hard
runtime boundaries: MCP, P2P, IPFS, Solana/Realms, SQLite, core domain, UI, and
test support must remain independently testable adapters.

# Ingested Decisions

Source set: Speckit artifacts, Grill with Docs language model, and ADRs approved before
2026-05-23.

## Locked ADR Decisions

### ADR-0001: Event Authority Before Event Admission

- A signed P2P message proves sender or relay identity, not lifecycle authority.
- Every state-changing lifecycle event needs a valid Event Authority.
- Event Admission Rules decide whether an event mutates protocol state.
- Invalid or unauthorized events are rejected or quarantined and do not advance task state.

### ADR-0002: Independent Dispute Resolution

- ReviewDecision and ReviewOutcome can be challenged through Dispute Case.
- Disputes are resolved by conflict-free Dispute Resolvers or a Dispute Resolution Panel.
- A resolver cannot be the requester, executor, challenged reviewer, or direct beneficiary.
- Dispute access to private evidence is grant-gated and purpose-scoped.

### ADR-0003: Compensable Contributions and Baseline Validation

- SettlementLine records only policy-authorized, attributable, evidenced contributions.
- Baseline Node Validation is required protocol participation and is not compensable by
  task settlement.
- Specialized validation, review, dispute resolution, storage, or relay can be
  compensable only when explicitly authorized by policy and budget.

### ADR-0004: Dispute Bonds and Governance Waivers

- A blocking Dispute Case requires a Dispute Bond, a pre-reserved Dispute Cost
  Allocation, or a DAO-governed waiver.
- Private owners cannot open bondless blocking disputes by default.
- A Frivolous Dispute may forfeit its bond into Dispute Cost Allocation.
- Bond forfeiture is not Slashing; Slashing is reserved for Agent executors in core
  protocol behavior.

## Research Decisions

- Use a strict TypeScript monorepo.
- Use Clean Architecture plus DDD bounded contexts.
- Use Jest-first BDD/TDD with unit, contract, integration, and end-to-end coverage sized
  by risk.
- Use libp2p for peer discovery and propagation.
- Use IPFS through Helia for evidence and artifact references.
- Use SQLite for local node state and audit projections.
- Use MCP TypeScript SDK for runtime adapter contracts.
- Use quote-gated economics with AgentPricingPolicy so agents do not spend tokens before
  a task is valid, fundable, and accepted.
- Use Solana Kit for commitment writing.
- Use Realms Today/SPL Governance references for DAO governance semantics.
- Use grant-gated private artifacts and privacy-safe reputation projections.
- Use Assignment Leases to protect availability when work is claimed but not delivered.
- Use bounded disputes and modular payment execution.
- Use Vite, React, and Tailwind for operator UI.
- Keep on-chain data to commitments and economic effects; keep private details off-chain.

## Grill with Docs Decisions

- Company Capability Key remains the term, but it means a company-scoped executor
  capability definition, not a capability owned or performed by the company itself.
- Global Capability Keys are protocol-defined compatibility terms. Nodes can remain
  interoperable across protocol versions when they support the relevant capability
  definitions and compatibility manifests.
- Company-scoped capabilities are not advertised publicly through peer compatibility
  manifests; they resolve inside authorized company context.
- A human executor must be a Stakeholder of the company that owns the task.
- Executor identity and executor interface are separate:
  `executorKind` distinguishes Agent versus Stakeholder, while `executorInterfaceKind`
  distinguishes Agent Adapter versus Human Interface.
- Human Stakeholder failures do not receive protocol Slashing in core behavior. They
  produce audit, governance, and company-policy consequences.
- Task Policy can add task-specific constraints but cannot weaken Company Policy.
- Agent Permission is task-scoped operational authority granted after assignment; it is
  not merely a static policy rule.
- Capability Claim should use structured keys/tags backed by versioned Capability
  Definitions, not free text.
- A task Policy Decision records exact policy and capability definition versions so later
  governance changes do not silently mutate in-progress work.
- Vague or oversized task proposals become bounded Planning Tasks or return
  clarification requirements before agent token spend.
- Execution Quotes are generated from Agent Pricing Policy and must be accepted before
  Budget Reservation and assignment.
- Agent pricing is market-adjusted by agent owners and, when granted, by local runtime
  automation through a Local Pricing Configuration Interface. AAO exposes the local
  interface but does not define the runtime implementation.
- Runtime pricing updates require a Pricing Configuration Grant, obey Pricing Update
  Rules, and remain local-only except for accepted network-visible pricing outputs.
- The default Solana integration consumes the DAO Payment Interface through a modular
  Solana Payment Adapter.
- Disputes allow third parties to resolve reviewer dishonesty or mistaken validation.
- Not every entity that validates messages or tasks is paid; baseline protocol validation
  is part of network participation.

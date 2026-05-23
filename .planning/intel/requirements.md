# Ingested Requirement Themes

The original canonical requirements remain in
`specs/001-aao-protocol-core/spec.md`. This file groups them for GSD planning.

## Governance and Company Model

- Support Public, Hybrid, and Private Company models.
- Attach Company Policy to every Company.
- Enforce governance authority for policy creation, amendment, approval, revocation, and
  override.
- Prevent Task Policy from weakening Company Policy.

## Agent, Capability, and Policy

- Agents declare Capability Claims against versioned Capability Definitions.
- Agents have Agent Policy and task-scoped Agent Permissions.
- Global Capability Keys are protocol compatibility terms.
- Company Capability Keys are company-scoped executor capability terms resolved only
  inside authorized company context.
- Policy Decision must resolve exact policy versions and capability definition versions.
- Unsupported capability versions must deny or degrade safely instead of assuming
  fallback.

## Task, Quote, and Budget

- Task Proposals must pass Preflight Check before any Agent spends execution tokens.
- Vague or oversized proposals must become bounded Planning Tasks or return clarification
  requirements.
- Execution Quote must be generated from Agent Pricing Policy and accepted before Budget
  Reservation.
- Budget Reservation must exist before assignment and token-spending work.
- Quotes, reservations, assignments, and artifact grants must have expiration behavior.
- Agent owners may update pricing manually; authorized local runtime automation may also
  request pricing changes through local-only configuration grants.

## Assignment and Execution

- A Task has exactly one Executor.
- Executor can be an Agent or a human Stakeholder of the same Company.
- Executor Interface is explicit: Agent Adapter for agents, Human Interface for humans.
- Assignment Lease prevents claimed work from indefinitely blocking availability.
- Expired leases can release, reassign, dispute, or escalate according to policy.
- Multi-executor work is decomposed into multiple Tasks under a parent Goal.

## Evidence, Validation, Review, and Dispute

- Every Task requires an Evidence Bundle.
- Private artifacts require Artifact Access Grants that are purpose-scoped, revocable,
  time-bounded, and minimally exposed.
- Every Evidence Bundle requires Validation under a Validation Authority.
- Review is required when policy requires it or validation indicates subjective judgment.
- ReviewDecision and ReviewOutcome can be challenged by Dispute Case.
- Blocking disputes require Dispute Bond, Dispute Cost Allocation, or DAO-governed
  waiver.
- Dispute Resolution Panel access is grant-gated and conflict-free.

## Reputation, Settlement, and Payment

- Reputation Signal applies to Agents.
- Reputation Projection protects privacy while exposing useful trust signals.
- Settlement records authorized economic outcome for task results.
- SettlementLine must be policy-authorized, attributable, and evidenced.
- Baseline Node Validation is not compensable by task settlement.
- Solana Payment Adapter executes or observes payment through the DAO Payment Interface
  by default.
- Sensitive task details stay off-chain; on-chain records are commitments and economic
  effects.

## P2P, Events, UI, and Quality

- P2P signatures authenticate transport, not lifecycle authority.
- Every state-changing event must satisfy Event Authority and Event Admission Rule.
- Nodes announce global capability support through Capability Support Manifest without
  leaking company-scoped capability context.
- Operator UI must expose task intake, policy decisions, quotes, evidence, review,
  disputes, reputation, and settlement state.
- The implementation must preserve evidence-first execution, policy-driven autonomy,
  verifiable reputation, human and AI governance, secure node boundaries, and Solana
  settlement with off-chain private data.
- TypeScript strict mode, Clean Architecture, DDD bounded contexts, Jest BDD/TDD,
  accessibility, and performance budgets are mandatory.

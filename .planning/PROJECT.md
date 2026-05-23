# AAO Protocol

## What This Is

AAO Protocol, Agentic Autonomous Organization, is a decentralized protocol for
company-governed delegation of work to AI agents and authorized human stakeholders.

It provides the accountability layer between Companies, Tasks, Agent Adapters, Human
Interfaces, Evidence, Review, Dispute, Reputation, Settlement, and Solana payment
execution.

## Core Value

Companies can delegate work to unknown or semi-trusted executors only when the work is
policy-bound, budgeted, evidence-backed, reviewable, dispute-resolvable, and economically
accountable.

The protocol protects both sides:

- Companies avoid paying for invalid, unauthorized, or unevidenced work.
- Agents avoid spending tokens before a task is valid, possible, priced, and funded.
- Human stakeholders can execute manually only inside company-governed authority.
- Reviewers and dispute resolvers can judge outcomes without receiving unchecked payment
  or execution authority.

## Source of Truth

Primary source documents:

- `CONTEXT.md`
- `.specify/memory/constitution.md`
- `specs/001-aao-protocol-core/spec.md`
- `specs/001-aao-protocol-core/plan.md`
- `specs/001-aao-protocol-core/research.md`
- `specs/001-aao-protocol-core/data-model.md`
- `specs/001-aao-protocol-core/contracts/*.md`
- `docs/adr/0001-event-authority-before-admission.md`
- `docs/adr/0002-independent-dispute-resolution.md`
- `docs/adr/0003-compensable-contributions-and-baseline-validation.md`
- `docs/adr/0004-dispute-bonds-and-governance-waivers.md`

Precedence for overlaps:

1. ADRs for locked architectural decisions.
2. `CONTEXT.md` for domain language and resolved ambiguities.
3. Speckit spec, plan, data model, and contracts for canonical product and technical
   requirements.
4. `research.md` for accepted technology choices.
5. README, AGENTS, quickstart, and checklists for operational guidance.

## Active Requirements

The active v1 planning set contains 36 requirements in `.planning/REQUIREMENTS.md`.

They cover:

- company governance and policy;
- agent policy, permissions, pricing, and local runtime configuration;
- global and company-scoped capability definitions;
- task proposals, preflight, planning tasks, quotes, and budget reservations;
- assignment, executor interfaces, and leases;
- evidence, private artifact grants, validation, review, and disputes;
- reputation, settlement lines, Solana payment, and DAO payment interface;
- P2P event admission, compatibility manifests, operator UI, tests, performance, and
  security.

## Non-Goals

- Do not put raw private task data, sensitive artifacts, secrets, or private evidence on
  chain.
- Do not fix execution prices in protocol code.
- Do not trust agent self-claims without policy, evidence, and validation.
- Do not compensate baseline node validation through task settlement.
- Do not allow P2P signatures to bypass Event Authority or Event Admission Rules.
- Do not let Governance Override bypass Agent Policy or expand Agent Permissions.
- Do not define the internals of local agent runtimes; expose local configuration
  interfaces and grants instead.
- Do not split the foundation spec during this ingest. Phase implementation through GSD.

## Architecture

Target architecture:

- strict TypeScript monorepo;
- Clean Architecture;
- DDD bounded contexts;
- Jest-first BDD/TDD;
- libp2p for propagation;
- Helia/IPFS for artifact and evidence references;
- SQLite for local node state and audit projections;
- MCP TypeScript SDK for runtime adapter contracts;
- Solana Kit for commitments and payment integration;
- Realms/SPL Governance semantics for DAO governance references;
- Vite, React, and Tailwind for operator UI.

Domain code must not depend directly on framework, transport, storage, UI, Solana, or
runtime adapter implementation details.

## Key Decisions

| Decision | Current Rule |
| --- | --- |
| Event authority | P2P signatures authenticate transport only; lifecycle mutation needs Event Authority and Event Admission. |
| Capability model | Capability keys are versioned. Global keys are protocol-defined; company keys are company-governed executor capability terms resolved only in authorized company context. |
| Task validity | Agent token spend happens only after preflight, quote acceptance, budget reservation, and assignment gates. |
| Planning | Vague or oversized work becomes bounded Planning Task or returns clarification needs. |
| Pricing | Agent Pricing Policy is market-driven by owners or granted local runtime automation; protocol does not set fixed prices. |
| Assignment | A task has one executor and an Assignment Lease. Multi-executor work decomposes into child tasks. |
| Human executor | Human executor must be a Stakeholder of the task's Company and uses a Human Interface. |
| Evidence | Every task requires Evidence Bundle and Validation Authority. |
| Private data | Artifact access is grant-gated; private details stay off-chain. |
| Disputes | Blocking disputes require bond, dispute cost allocation, or DAO-governed waiver. |
| Compensation | Settlement pays only policy-authorized, attributable, evidenced contributions. Baseline node validation is unpaid. |
| Slashing | Core protocol Slashing applies only to Agent executors, not human Stakeholders. |
| Payment | Default Solana path consumes the DAO Payment Interface through a modular adapter. |

## Current Implementation Posture

Treat `specs/001-aao-protocol-core` as the foundation feature. The next useful move is
to implement Phase 1 from `.planning/ROADMAP.md`: project skeleton, domain language,
event admission primitives, security boundaries, tests, and measurable performance gates.

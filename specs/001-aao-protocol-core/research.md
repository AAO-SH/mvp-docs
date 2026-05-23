# Phase 0 Research: AAO Protocol Core

## Decision: Strict TypeScript monorepo

Use TypeScript strict mode across all protocol, node, adapter, and UI packages.
Use a workspace layout so bounded contexts and adapters can be versioned and
tested independently.

**Rationale**: The user specified TypeScript strict mode. Strict TypeScript
supports explicit domain contracts, safer event envelopes, and compile-time
pressure against ambiguous evidence/policy state.

**Alternatives considered**: Rust for core protocol was rejected for this slice
because the requested UI, MCP, libp2p JS, Helia, Jest, and Solana Kit stack is
TypeScript-centered. A single-package app was rejected because it would blur DDD
contexts and adapter boundaries.

## Decision: Clean Architecture plus DDD bounded contexts

Use `domain`, `application`, `adapters`, and `infrastructure` boundaries. Model
Company Registry, Agent Registry, Policy & Governance, Task Execution Graph,
Evidence & Validation, Review, Reputation, Settlement, Workspace, Storage,
Secure Node, P2P Propagation, Runtime Adapter, and Operator UI as bounded
contexts.

**Rationale**: AAO's core risk is semantic confusion between policy, evidence,
review, reputation, and settlement. DDD keeps language explicit; Clean
Architecture keeps domain rules independent from provider details.

**Alternatives considered**: CRUD-first services were rejected because they
would make lifecycle invariants harder to enforce and test.

## Decision: Jest-first BDD/TDD

Use Jest for all JavaScript/TypeScript test suites. Behavior tests use
Given/When/Then naming and run before implementation. Domain tests avoid mocks
except at external ports.

**Rationale**: The constitution and user input require BDD/TDD with Jest. Jest
also gives a single test vocabulary for protocol packages and UI-facing logic.

**Alternatives considered**: Vitest was rejected for this feature because the
user explicitly named Jest. Manual QA-only review was rejected because evidence,
policy, and settlement invariants need executable proof.

## Decision: libp2p for peer discovery and propagation

Use js-libp2p for P2P peer identity, discovery, pubsub propagation, direct
request/response, and deduped protocol events.

**Rationale**: libp2p is a modular network stack with js-libp2p available for
browsers and Node.js, fitting the TypeScript runtime choice and AAO's P2P
network requirements.

**Alternatives considered**: Centralized queues were rejected as the default
because AAO requires peer-to-peer discovery and propagation. A custom P2P stack
was rejected because identity, transport, discovery, and pubsub are already
hard, security-sensitive problems.

Source: official libp2p docs, https://docs.libp2p.io/

## Decision: IPFS through Helia for evidence and artifact references

Use Helia/IPFS for content-addressed artifact references and evidence bundle
storage. Store sensitive artifacts encrypted or private off-chain, and publish
only safe references, hashes, CIDs, or summaries.

**Rationale**: IPFS docs identify Helia as the lean, modern JavaScript
implementation and state that legacy js-IPFS has been superseded by Helia.

**Alternatives considered**: Raw filesystem paths were rejected because they are
not portable or content-addressed. On-chain artifact storage was rejected by the
constitution because sensitive content must stay off-chain.

Source: IPFS JavaScript docs, https://docs.ipfs.tech/reference/js/api/

## Decision: SQLite for local node state and audit projections

Use SQLite for local node persistence: companies, agents, policies, tasks,
event log, projections, inbox/outbox, evidence metadata, and settlement
commitment records. Use WAL mode with explicit checkpoint strategy.

**Rationale**: SQLite keeps the first node simple and portable. WAL mode enables
concurrent readers and append-style writes, but official docs warn that WAL size
and checkpointing need operational care.

**Alternatives considered**: PostgreSQL was rejected for the initial local node
because it adds deployment weight before the protocol lifecycle is proven. Pure
in-memory state was rejected because auditability requires durable records.

Source: SQLite WAL docs, https://www.sqlite.org/wal.html

## Decision: MCP TypeScript SDK for runtime adapter contracts

Use MCP as the adapter protocol between external AI runtimes and the AAO node.
Expose tools/resources for handshake, task intake, task status, evidence
submission, and adapter-normalized execution records.

**Rationale**: MCP official docs list a TypeScript SDK as Tier 1 and describe
support for servers/clients, tools, resources, prompts, local/remote transports,
and type safety.

**Alternatives considered**: Ad hoc JSON over HTTP was rejected because AAO
needs a standard tool/context surface for multiple AI runtimes. Direct runtime
plugins were rejected because they would couple AAO to specific agents.

Source: MCP SDK docs, https://modelcontextprotocol.io/docs/sdk

## Decision: Quote-gated economics with Agent Pricing Policy

Use deterministic preflight before agent runtime execution, then require
Execution Quote acceptance and Budget Reservation before model-token or
paid-tool spend. Routine quotes are generated from agent-owned Agent Pricing
Policy. The agent owner can update pricing manually or authorize the local
runtime to request or apply updates under owner-defined rules through a local
pricing configuration interface.
Authorized runtime updates require a separate owner-issued Pricing
Configuration Grant; agent identity and task-scoped permissions are not enough
to change economic configuration.

**Rationale**: Agents should not burn tokens on invalid, vague, unfunded, or
unauthorized work. Companies need predictable bounds before accepting spend,
while agents need market-adjustable pricing instead of protocol-fixed rates.
The protocol should define the local configuration boundary and validation
rules, not the runtime mechanism that decides when to call it. Pricing authority
belongs to the agent owner, so runtime automation needs explicit delegated
configuration authority.

**Alternatives considered**: Fixed protocol prices were rejected because they
cannot track market conditions or task risk. Free planning was rejected because
vague tasks can create unbounded agent cost. Company-controlled agent pricing
was rejected because the agent owner decides what work is economically viable
for that agent. Runtime-specific scheduling or chat automation was rejected as
protocol scope because cron jobs, heartbeats, chats, endpoint callers, and API
clients are implementation choices outside the domain contract.
Treating the Agent identity as pricing authority was rejected because execution
identity and owner-governed economic configuration are separate trust domains.

## Decision: Solana Kit for commitment writing

Use Solana Kit for TypeScript Solana interactions that prepare and submit
policy, evidence, review, reputation, settlement, and payment execution
commitments.

**Rationale**: Solana's official docs identify `@solana/kit` as the recommended
TypeScript SDK for building on Solana.

**Alternatives considered**: Legacy `@solana/web3.js` was rejected for new
development unless a specific integration needs it. Custom RPC wrappers were
rejected because signer, transaction, and subscription handling are core SDK
concerns.

Source: Solana official JavaScript/TypeScript SDK docs,
https://solana.com/docs/clients/official/javascript

## Decision: Realms Today/SPL Governance for DAO governance references

Use Realms Today/SPL Governance as the DAO governance reference path for public
and hybrid companies. AAO stores Realms proposal/vote/account references as
governance evidence rather than reinventing DAO voting in the first slice.

**Rationale**: Realms describes itself as a fully on-chain Solana platform for
DAO management, with DAOs, proposals, votes, and treasury-linked governance.

**Alternatives considered**: Building a custom DAO governance system was
rejected for this slice because AAO's core differentiator is execution
accountability, not DAO voting mechanics.

Source: Realms docs, https://docs.realms.today/

## Decision: Grant-gated private artifacts and privacy-safe reputation

Require Artifact Access Grants for any private or encrypted artifact access.
Expose public reputation through aggregate Reputation Projections while keeping
company, task, evidence, reviewer, and company-scoped capability detail behind
authorized projections.

**Rationale**: Evidence and reputation must be useful for verification and
matching without leaking private company work or raw artifacts.

**Alternatives considered**: Treating artifact refs as directly accessible was
rejected because privacy classes would become advisory only. Publishing raw
reputation logs was rejected because it can reveal confidential client work and
company-scoped capabilities.

## Decision: Assignment leases for availability protection

Every assignment that can block work has an Assignment Lease with heartbeat or
progress evidence requirements. Missing heartbeat or progress evidence applies
the Expiration Rule and can release, reassign, dispute, or fail the task.

**Rationale**: A node or executor claiming work and never delivering is an
availability attack. Leases make task ownership temporary and auditable.

**Alternatives considered**: Permanent assignment until manual intervention was
rejected because it lets malicious or failed executors hold task availability.
Heartbeat-only completion was rejected because heartbeat proves liveness, not
quality.

## Decision: Event authority before event admission

Treat P2P signatures as transport authentication only. Any lifecycle event that
changes task state, permissions, evidence, review, reputation, budget,
settlement, or payment must carry Event Authority and pass local Event
Admission Rules before it affects logs or projections.

**Rationale**: A malicious or mistaken peer can sign and relay a false event.
The protocol must distinguish "this peer sent the message" from "this actor was
authorized to originate the event."

**Alternatives considered**: Accepting any correctly signed peer message was
rejected because a valid relay signature can still carry an unauthorized
lifecycle claim. Centralized event admission was rejected because AAO's P2P
model requires each node to verify authority locally from policy, grants,
leases, adapters, and governance references.

## Decision: Bounded disputes and modular payment execution

Use Dispute Cases as bounded challenge flows that can block final reputation,
settlement, budget release, or payment execution. Keep Settlement Records
separate from Payment Execution Records. Use a modular Solana Payment Adapter,
defaulting to a configured DAO Payment Interface for DAO-governed treasury
flows. Disputes against ReviewDecision or ReviewOutcome use a policy-defined
Resolver Pool and conflict-free Dispute Resolution Panel so a dishonest or
conflicted reviewer is not the final authority over their own decision. Use
Budget Allocations and Settlement Lines to compensate only policy-authorized,
attributable, evidenced contributions. Baseline Node Validation is a
non-compensated protocol participation duty. Blocking disputes require a
refundable Dispute Bond, pre-reserved dispute-cost Budget Allocation, or
DAO-governed Governance Dispute Waiver before they can pause payment,
settlement, reputation, budget release, assignment, or review. Private-company
owners do not receive default bondless dispute authority. Frivolous, abusive,
unsupported, spammy, or bad-faith disputes may forfeit the bond to resolver
costs, affected-party costs, or treasury recovery through Settlement Lines;
this forfeiture is not agent slashing.

**Rationale**: Disputes need deadlines and authority. Settlement is the
authorized economic outcome, while payment execution is an adapter-driven
operation that may fail, retry, or require governance action without rewriting
evidence, review, reputation, or settlement history. Reviewer judgments are
also evidence-backed claims that can be challenged by independent resolvers.
Economic participation must be itemized because execution, review, governance
validation, dispute resolution, storage, refunds, and payment execution have
different evidence and authority gates. Paying ordinary node validation per task
would create spam and sybil incentives around work every node must already do
to protect local state and network legitimacy. Free blocking disputes would
create another denial-of-service vector against executors and reviewers, while
a bond or explicit dispute-cost reserve makes abuse economically accountable.
DAO-governed waiver exists for governance-protected challenges, but owner-only
private waiver was rejected because it would let the payer delay settlement at
no cost.

**Alternatives considered**: Folding disputes into review status was rejected
because disputes can target assignment, budget, reputation, or payment, not only
review judgment. Treating settlement as payment was rejected because Solana
execution and DAO treasury flows need modular adapters and separate failure
handling. Treating the original reviewer as final for disputes against their
own decision was rejected because it would make reviewer fraud or conflict
hard to challenge. Paying every node for baseline message or task validation
was rejected because baseline validation is necessary for protocol safety and
would make normal P2P participation look like a task-level service claim. Free
or owner-waived blocking disputes were rejected because they create payment
griefing incentives. Treating Dispute Bond forfeiture as Slashing was rejected
because Slashing remains agent-only in the core protocol.

## Decision: Vite, React, and Tailwind for operator UI

Use Vite + React + Tailwind for the operator UI. UI implements lifecycle status,
policy decision visibility, evidence/review inspection, Capability Claim and
Proven Capability discovery, and settlement traceability.

**Rationale**: The user specified this stack. It supports fast local iteration
and a componentized interface for consistent domain terminology and task states.

**Alternatives considered**: Server-rendered UI was rejected for the first slice
because lifecycle state will benefit from reactive client projections.

## Decision: On-chain commitments, off-chain details

Solana commitment records contain hashes and references to policy, evidence,
review, reputation impact, and settlement outcome. Detailed evidence, artifacts,
workspace snapshots, and sensitive data remain in SQLite/IPFS or private
providers.

**Rationale**: This follows the constitution's on-chain/off-chain boundary while
still preserving auditability and economic accountability.

**Alternatives considered**: Putting detailed evidence on-chain was rejected for
privacy and cost. Keeping all settlement local was rejected because AAO requires
transparent commitment records for DAO/economic flows.

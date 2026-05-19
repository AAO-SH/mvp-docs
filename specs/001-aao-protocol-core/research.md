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

Use SQLite for local node persistence: organizations, agents, policies, tasks,
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

## Decision: Solana Kit for commitment writing

Use Solana Kit for TypeScript Solana interactions that prepare and submit
policy, evidence, review, reputation, and settlement commitments.

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
and hybrid organizations. AAO stores Realms proposal/vote/account references as
governance evidence rather than reinventing DAO voting in the first slice.

**Rationale**: Realms describes itself as a fully on-chain Solana platform for
DAO management, with DAOs, proposals, votes, and treasury-linked governance.

**Alternatives considered**: Building a custom DAO governance system was
rejected for this slice because AAO's core differentiator is execution
accountability, not DAO voting mechanics.

Source: Realms docs, https://docs.realms.today/

## Decision: Vite, React, and Tailwind for operator UI

Use Vite + React + Tailwind for the operator UI. UI implements lifecycle status,
policy decision visibility, evidence/review inspection, agent capability
discovery, and settlement traceability.

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

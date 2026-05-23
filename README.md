# AAO Protocol

**A decentralized operating system for AI-agent work.**

AAO Protocol is a governance and accountability layer for autonomous AI-agent
execution. It helps companies, DAOs, founders, investors, operators, reviewers,
and AI agents coordinate real work without blindly trusting any single agent,
human, or platform.

The protocol turns agent work into a structured lifecycle:

```text
Task Proposal
  -> Policy Check
  -> Execution Quote
  -> Budget Reservation
  -> Agent Assignment
  -> Assignment Lease
  -> Runtime Execution
  -> Evidence Bundle
  -> Validation
  -> Review when required
  -> Dispute when required
  -> Reputation Update
  -> Reward / Slashing
  -> Settlement Commitment
  -> Payment Execution when enabled
```

In plain English: **AAO lets companies safely delegate work to AI agents by
making every task policy-bound, evidence-backed, reviewable, auditable, and
economically accountable.**

## Why AAO Exists

AI agents are becoming capable software workers. They can operate tools, write
code, move information, and produce business outputs. But at scale they are not
safe to trust blindly:

- they can hallucinate or produce low-quality work;
- they can act outside their intended permissions;
- they can fail silently or submit misleading claims;
- they can be difficult to audit after the fact;
- they can create economic disputes when rewards or penalties are involved.

AAO is designed to become the missing coordination layer for the agent economy:
a system where autonomous execution is governed by permission, proof, review,
reputation, and settlement.

## Core Idea

Traditional DAOs mainly vote on proposals. AAO goes further: it governs actual
execution.

A company can define:

- who can request work;
- which agents are eligible to execute it;
- what level of autonomy each task allows;
- what evidence must be produced;
- when human or DAO review is mandatory;
- how reputation changes after outcomes;
- when rewards or slashing can be recorded.

Agents remain useful but untrusted by default. Their work only advances when it
passes the required policy, evidence, validation, and review gates.
Agents also do not need to spend model tokens on execution before work is
scoped, quoted, budget-reserved, assigned, and permissioned.
Assigned work is protected by an assignment lease so an executor cannot claim a
task and silently hold it without heartbeat or progress evidence.
Network messages are also not trusted just because they are signed by a peer.
P2P signatures prove who sent or relayed a message; Event Authority and Event
Admission Rules decide whether the lifecycle event inside the message is allowed
to change task, evidence, reputation, settlement, or payment state.

## Protocol Pillars

### Evidence-first execution

AAO does not accept "the agent says it is done" as completion. A task must
produce an evidence bundle with artifact references, hashes or CIDs, validation
results, review links, workspace references, and audit records.

Sensitive company data does not need to be exposed publicly. The protocol can
commit to safe references, summaries, encrypted objects, hashes, and verification
records instead.
Private or encrypted artifacts require scoped Artifact Access Grants before an
agent, reviewer, validator, or stakeholder can open them.

### Policy-driven autonomy

Not every task deserves the same level of automation. Low-risk work may be fully
automated. High-risk, subjective, permission-expanding, or budget-sensitive work
can require approval or review.

AAO gives each company a way to define the autonomy boundary before an
agent acts.

### Quote-gated execution

AAO separates cheap deterministic checks from token-spending agent work. Nodes
can run preflight checks for policy, capability, risk, budget, and clarity
before asking an agent to execute.

If work is valid but needs a market price, agents submit execution quotes with
scope, price, assumptions, limits, and validity. If work is too vague or too
large, AAO can create a bounded paid planning task whose outcome is a plan,
clarification request, next planning request, or an accepted inability to plan
within limits.

Routine quotes come from an agent-owned Agent Pricing Policy, not from a fixed
protocol price or company-controlled table. The agent owner can update that
policy manually or authorize the local runtime to adjust it through protocol
configuration, while every quote records the pricing policy version it used.
AAO exposes the local configuration boundary and owner-defined limits; it does
not prescribe whether the runtime calls that boundary from a scheduled job,
heartbeat, chat flow, endpoint, or API client.
Runtime pricing updates also require a separate owner-issued Pricing
Configuration Grant; the agent's execution identity and task permissions do not
authorize economic configuration changes.
Those grants stay local-only: the network can audit pricing policy versions and
opaque references, but it does not receive the private grant contents.

### Human + AI governance

AAO supports multiple company models:

- **Public Company**: governed transparently by a DAO.
- **Private Company**: controlled by an owner.
- **Hybrid Company**: combines DAO ownership with partners, executives, or
  private governance rules.

This lets the protocol support real companies with different levels of
openness, decentralization, and control.

### Reputation economy

Agent reputation is not self-declared marketing. It is built from accepted
evidence, validation results, reviews, task outcomes, rewards, and slashing.
Public reputation is exposed through privacy-safe projections so aggregate trust
can be used for matching without revealing private company work.

AAO separates **Capability Claims** from **Proven Capabilities**.
Global capability definitions are part of the AAO Protocol source/version;
company-scoped definitions are governed by the owning company. Nodes can keep
working with the global definition versions they support, while unsupported
task requirements return an upgrade or unsupported-capability reason. Peers
announce protocol version plus a capability support manifest so they can find
the exact global definitions they have in common; company-scoped definitions
stay inside authorized company flows and describe executor capabilities in that
company's vocabulary, not capabilities of the company itself.

### On-chain settlement, off-chain privacy

AAO uses Solana for commitments, governance references, token/stake activity,
settlement records, and modular payment execution. Detailed artifacts, secrets,
private workspace data, and sensitive evidence stay off-chain.

Settlement records authorize economic outcomes. Real payment attempts are
recorded separately as payment execution records through a Solana payment
adapter. By default, DAO-governed treasury payment flows consume the configured
DAO payment interface.

Task budgets can be itemized. The executor reward is only one settlement line;
policy may also reserve allocations for reviewers, governance validators,
dispute resolvers, dispute costs, storage providers, payment execution costs,
refunds, or forfeits. Ordinary node work such as message validation, event
admission, deduplication, compatibility checks, and local projections is
baseline protocol participation and is not paid by a task settlement line.

Disputes that block payment, settlement, reputation, budget release,
assignment, or review need an economic anti-abuse guardrail: a refundable
Dispute Bond, a pre-reserved dispute-cost allocation, or a DAO-governed waiver.
Private-company owners do not get a default free path to block payment; if a
dispute is frivolous or abusive, the bond can be forfeited to resolver costs,
affected-party costs, or treasury recovery without treating it as agent
slashing.

The result is auditability without unnecessary exposure.

## Practical Example: Offline YouTube Delegation

Imagine a channel owner will travel without signal and wants someone on the
internet to keep a YouTube channel updated. In AAO terms, the owner creates a
private company, defines a task policy for posting videos, reserves the budget,
and assigns execution to a limited contractor identity. If the executor is a
human, they become a same-company stakeholder with a narrow member role; if the
executor is automated, they execute through an agent adapter.

The owner does not publish raw passwords or private content. Channel tokens,
scripts, drafts, and assets stay encrypted off-chain in the secure node or
storage provider. The task grants only scoped, time-bounded access: for example,
upload this prepared video, use this title/description, within this date range,
with limited attempts, and revoke access after the task or deadline. The network
sees safe references, hashes, CIDs, grant refs, and commitments, not secrets.

After posting, the executor submits an evidence bundle: signed upload receipt,
video URL or ID, timestamp, screenshots or metadata, hashes of the prepared
script/assets used, and any relevant logs. Mechanical validation can prove that
something was posted to the right channel. A trusted friend who understands the
content acts as reviewer or governance validator and signs whether the result
actually follows the script and quality rule. A trusted relative who controls
payment does not need to judge quality; they only execute payment after the
accepted review outcome authorizes settlement.

If the friend lies, the review is not final just because it was signed. The
owner, executor, payer, DAO, or another authorized actor can open a dispute
against the review decision or outcome. The task policy selects independent
dispute resolvers who were not involved in the task, gives them only scoped
access to the evidence they need, and aggregates their signed resolver decisions
into a dispute outcome before payment, reputation, or settlement proceeds.
If that dispute would block payment or reputation, it must have a Dispute Bond,
pre-reserved dispute-cost allocation, or DAO-governed waiver. That prevents the
owner or any other participant from delaying payment for free.

Economically, the YouTube task can reserve separate allocations: one reward for
the poster, a reviewer fee for the friend if the review is valid, resolver fees
if a dispute is needed, storage or payment execution costs if configured, and a
refund or forfeit line for unused or invalid work. The normal work that every
AAO node performs to validate and relay protocol messages is not paid from that
task budget.

Cryptographically, the flow is a chain of signed and hash-linked claims:
identity signatures prove who requested, executed, reviewed, and paid; hashes
and CIDs prove artifacts were not swapped; encrypted artifact grants control who
can open private material; on-chain commitments can record the policy/evidence/
review/settlement hashes without exposing content; Event Admission Rules reject
signed network messages from actors that lack authority for the event they are
claiming.

## System Zones

AAO is organized into three major zones:

| Zone | Purpose |
|------|---------|
| Runtime | External AI agents such as Codex, Claude, Cursor, Hermes, OpenClaw, or custom agents execute work outside the protocol. |
| Protocol | The coordination layer that handles policies, adapters, P2P propagation, evidence, validation, review, reputation, workspace records, and settlement logic. |
| Blockchain | Solana records DAO governance, treasury/token activity, policy commitments, evidence commitments, review commitments, and settlement commitments. |

## Planned Technical Stack

The MVP documentation currently plans for:

- **Language**: TypeScript in strict mode
- **Architecture**: DDD, Clean Architecture, high modularity
- **Testing**: BDD/TDD with Jest
- **P2P**: libp2p
- **Storage**: SQLite for local node state, IPFS/Helia for content-addressed
  artifacts
- **Agent interface**: MCP
- **Blockchain**: Solana Kit and Realms governance references
- **UI**: Vite, React, Tailwind

## Repository Map

```text
docs/
  constitution.md             # Project constitution and non-negotiable principles

specs/001-aao-protocol-core/
  spec.md                     # Product specification
  plan.md                     # Implementation plan
  research.md                 # Technical decisions and rationale
  data-model.md               # Domain model and lifecycle states
  quickstart.md               # Planned developer workflow
  contracts/                  # MCP, P2P, event, Solana, and UI contracts
  checklists/requirements.md  # Spec quality checklist
```

## What Is in the MVP Docs

The current MVP documentation defines the first executable slice of AAO:

1. Configure a governed company.
2. Register agents through a policy-bound handshake.
3. Propose work with risk, capability intents, evidence, and budget constraints.
4. Run policy and preflight checks before agent token spend.
5. Accept execution quotes and reserve budget before assignment.
6. Use bounded planning tasks for vague or oversized work.
7. Assign tasks only to eligible executors.
8. Protect assigned work with leases, heartbeats, progress evidence, and expiry
   handling.
9. Require artifact access grants for private or encrypted evidence.
10. Require evidence bundles before completion.
11. Route subjective or high-risk work to review and disputed work to bounded
    dispute cases.
12. Update privacy-safe reputation projections from accepted evidence and review
    outcomes.
13. Record reward, slashing, settlement commitments, and payment execution
    status when enabled.

## Good One-line Explanation

**AAO Protocol is the coordination infrastructure for the agent economy: a way
to make autonomous AI-agent work governable, auditable, and economically
accountable.**

## License

MIT. See [LICENSE](./LICENSE).

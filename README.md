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
  -> Agent Assignment
  -> Runtime Execution
  -> Evidence Bundle
  -> Review when required
  -> Validation
  -> Reputation Update
  -> Reward / Slashing
  -> Settlement Commitment
```

In plain English: **AAO lets organizations safely delegate work to AI agents by
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

An organization can define:

- who can request work;
- which agents are eligible to execute it;
- what level of autonomy each task allows;
- what evidence must be produced;
- when human or DAO review is mandatory;
- how reputation changes after outcomes;
- when rewards or slashing can be recorded.

Agents remain useful but untrusted by default. Their work only advances when it
passes the required policy, evidence, validation, and review gates.

## Protocol Pillars

### Evidence-first execution

AAO does not accept "the agent says it is done" as completion. A task must
produce an evidence bundle with artifact references, hashes or CIDs, validation
results, review links, workspace references, and audit records.

Sensitive company data does not need to be exposed publicly. The protocol can
commit to safe references, summaries, encrypted objects, hashes, and verification
records instead.

### Policy-driven autonomy

Not every task deserves the same level of automation. Low-risk work may be fully
automated. High-risk, subjective, permission-expanding, or budget-sensitive work
can require approval or review.

AAO gives each organization a way to define the autonomy boundary before an
agent acts.

### Human + AI governance

AAO supports multiple organizational models:

- **Public Company**: governed transparently by a DAO.
- **Private Company**: controlled by an owner.
- **Hybrid Company**: combines DAO ownership with partners, executives, or
  private governance rules.

This lets the protocol support real organizations with different levels of
openness, decentralization, and control.

### Reputation economy

Agent reputation is not self-declared marketing. It is built from accepted
evidence, validation results, reviews, task outcomes, rewards, and slashing.

AAO separates declared capability from evidence-backed capability.

### On-chain settlement, off-chain privacy

AAO uses Solana for commitments, governance references, token/stake activity,
and settlement records. Detailed artifacts, secrets, private workspace data, and
sensitive evidence stay off-chain.

The result is auditability without unnecessary exposure.

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

1. Configure a governed organization.
2. Register agents through a policy-bound handshake.
3. Propose work with risk, capability, evidence, and budget constraints.
4. Run policy checks before assignment.
5. Assign tasks only to eligible agents.
6. Require evidence bundles before completion.
7. Route subjective or high-risk work to review.
8. Update reputation from accepted evidence and review outcomes.
9. Record reward, slashing, or settlement commitments.

## Good One-line Explanation

**AAO Protocol is the coordination infrastructure for the agent economy: a way
to make autonomous AI-agent work governable, auditable, and economically
accountable.**

## License

MIT. See [LICENSE](./LICENSE).

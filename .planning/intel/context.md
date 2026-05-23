# Ingested Context

AAO Protocol, Agentic Autonomous Organization, is a decentralized protocol for
company-governed delegation of work to AI agents and authorized human stakeholders.

The protocol exists to make internet-native delegation accountable:

- a Company emits a Task;
- nodes propagate the Task;
- Agent Adapters preflight policy and pricing before invoking expensive AI runtimes;
- Executors act only inside assignment, policy, permission, budget, and lease boundaries;
- Evidence Bundles prove work;
- Validation, Review, and Dispute resolve whether work satisfies policy;
- Reputation and Settlement are derived from evidenced outcomes;
- Solana integration commits and executes economic results while private data remains
  off-chain.

## Current Documentation Shape

The project currently uses a single large foundation spec:
`specs/001-aao-protocol-core`. The user explicitly chose to keep it compressed for now
because splitting it into many specs would add process overhead before the foundation is
stable.

The Speckit artifacts define the canonical feature, plan, research decisions, data model,
contracts, checklist, and quickstart. The Grill with Docs sessions sharpened terminology
and resolved domain ambiguities in `CONTEXT.md` and ADRs.

## Practical Delegation Example

The project uses a real-world delegation pattern as a domain example:

- an owner will be offline and wants a YouTube channel maintained;
- an unknown executor receives limited access, attempts, deadlines, and payment promise;
- a trusted relative holds payment authority but cannot judge content quality;
- a trusted reviewer can judge content quality but should not control funds;
- disputes need independent third parties when a reviewer lies or makes a mistake;
- cryptographic evidence, scoped grants, review, dispute, and settlement make this
  delegation auditable.

This example motivates separation between permission, execution, evidence, review,
payment, and dispute authority.

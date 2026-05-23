# Ingested Constraints

## Constitutional Gates

- Evidence-first execution: task progress, reputation, and settlement require evidence.
- Policy-driven autonomy: agents and stakeholders act only inside resolved policy and
  permission boundaries.
- Verifiable reputation: reputation must be derived from policy-authorized events and
  evidence, with privacy-safe projections.
- Human plus AI governance: both automated validation and human review/dispute mechanisms
  must be first-class.
- Secure node boundaries: adapters and interfaces isolate runtimes, secrets, wallets,
  workspaces, and local configuration.
- Solana settlement with off-chain private data: chain data is commitments and economic
  effects; sensitive details remain off-chain.

## Architecture Constraints

- Domain code must stay independent from frameworks, P2P transport, IPFS, SQLite, Solana,
  UI, and runtime adapters.
- Use Clean Architecture and DDD bounded contexts.
- Use strict TypeScript.
- Use Jest for BDD/TDD, including contract tests for protocol boundaries.
- Runtime adapters are untrusted and must cross explicit application ports.
- Human execution must go through Human Interface; agent execution must go through Agent
  Adapter.

## Security and Privacy Constraints

- Do not put raw private task data, sensitive artifacts, secrets, or private evidence on
  chain.
- Artifact Access Grants must be purpose-scoped, time-bounded, revocable, and minimal.
- P2P relay identity is not authorization to mutate task state.
- Company-scoped capability data must not leak through public support manifests.
- Agent runtime pricing automation must be local-only and grant-bound.
- Governance Override cannot bypass Agent Policy or expand Agent Permissions.

## Economic Constraints

- Agents must not spend execution tokens before policy preflight, quote acceptance, and
  budget reservation.
- Planning Tasks must be bounded by policy, budget, and expiration to avoid infinite
  decomposition cost.
- Pricing cannot be fixed by protocol code; it comes from Agent Pricing Policy and market
  choice.
- Baseline Node Validation is unpaid protocol participation.
- Compensable contributions require explicit policy authorization, attribution, evidence,
  and budget.
- Blocking disputes require a bond, pre-reserved dispute cost, or DAO-governed waiver.

## Performance Constraints

- Local preflight and policy decision should meet p95 under 500 ms for normal proposals.
- P2P propagation and admission logic must avoid unnecessary agent runtime calls.
- Evidence and artifact access should prefer references and commitments over copying
  private payloads through the network.
- UI and node flows must remain measurable against the budgets defined in the current
  Speckit plan.

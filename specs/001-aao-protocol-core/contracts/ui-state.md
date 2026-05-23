# Contract: Operator UI State

The operator UI must use the same lifecycle language as the domain model,
evidence bundles, logs, and audit records.

## Task Lifecycle States

| State | Meaning | User Action |
|-------|---------|-------------|
| `proposed` | Task has been submitted for policy check | Wait or inspect proposal |
| `preflighted` | Cheap eligibility checks completed without execution | Request or compare quotes |
| `needs-clarification` | Task is too vague for quote or execution | Clarify scope or evidence |
| `needs-decomposition` | Task must be planned into smaller tasks | Create or approve planning quote |
| `quote-pending` | Eligible executors may submit quotes | Compare quotes |
| `budget-reserved` | Quote accepted and budget reserved | Proceed to assignment |
| `blocked` | Policy prevents progress until action occurs | Resolve required action |
| `approved` | Policy allows assignment | Assign or wait for eligible executor |
| `assigned` | Executor is bound to the task | Monitor execution |
| `running` | Executor work is underway | Monitor evidence requirements |
| `lease-expired` | Assignment lease expired without required heartbeat or progress evidence | Release, reassign, dispute, or fail |
| `evidence-submitted` | Executor submitted evidence bundle | Validate evidence |
| `needs-review` | Human or DAO review is required | Review evidence |
| `validated` | Evidence passed validation | Accept or route to settlement |
| `accepted` | Task outcome accepted | Prepare settlement |
| `rejected` | Task outcome rejected | Request new work or apply policy outcome |
| `settlement-pending` | Settlement commitment is being prepared | Sign or wait |
| `settled` | Reward/no-settlement outcome recorded | Inspect audit trail |
| `slashed` | Agent penalty outcome recorded | Inspect audit trail |
| `failed` | Executor or workflow failed | Reassign, cancel, or review |

## Required UI Fields

Every task detail view shows:
- task id and objective summary;
- company and governance model;
- current state;
- next required action;
- responsible party;
- policy decision;
- resolved capability definition versions;
- execution quote when present;
- pricing policy version used for the quote when present;
- budget reservation when present;
- assigned executor;
- assignment lease status when present;
- evidence bundle reference;
- artifact access grants when private or encrypted refs are involved;
- validation result;
- validation authority;
- review request when present;
- review outcome when present;
- dispute case when present;
- dispute bond, dispute cost allocation, or DAO waiver when a dispute blocks
  final effects;
- reputation impact when present;
- settlement commitment when present.
- payment execution record when payment is enabled.

## Consistency Rules

- UI labels must match domain state names or documented human-readable aliases.
- Error messages must include the policy or evidence reason where available.
- Task screens must show preflight result, quote state, and budget reservation
  before assignment whenever agent token spend or paid tool spend is expected.
- Quote screens must show the Agent Pricing Policy version used, quote
  validity, assumptions, and whether the quote came from routine pricing or an
  authorized paid planning/update flow.
- Planning screens must show max token spend, max tool calls, decomposition
  depth, subtask limit, accepted outcomes, and quote validity.
- Assignment screens must show lease expiry, heartbeat status, progress evidence
  deadline, and policy action on expiry.
- Artifact screens must show private/encrypted access as grant-gated and expose
  grant details only to authorized viewers.
- Validation screens must identify Mechanical Validator or Governance Validator
  authority for each Validation Result.
- Review screens must show evidence references, privacy classification, quorum
  rule, conflicts, decisions, and outcome.
- Dispute screens must show challenged refs, deadlines, blocking effects,
  Dispute Bond status, dispute-cost Budget Allocation, DAO waiver status,
  resolver authority, Resolver Pool, Dispute Resolution Panel, resolver
  conflict status, Resolver Decisions, Dispute Outcome, and any frivolous
  dispute finding.
- Dispute screens must distinguish emergency pause from a finalized dispute
  decision, and must show when a private owner lacks bondless waiver authority.
- Settlement screens must distinguish commitment recording from token transfer.
- Settlement screens must show Budget Allocations and Settlement Lines, and
  must distinguish compensable contributions from Baseline Node Validation.
- Payment screens must show Solana Payment Adapter, DAO Payment Interface,
  payment execution status, and Solana signature ref when available.
- Reputation screens must distinguish public aggregate Reputation Projections
  from authorized detail projections.
- Event/audit screens must distinguish transport sender from event issuer and
  show Event Authority, admission status, and quarantine reason when available.
- Agent pages must separate Capability Claims from Proven Capabilities and show
  capability namespaces and definition versions.
- Agent pages must show the active Agent Pricing Policy version, last updater,
  pricing update mode, pending runtime proposals, and whether local runtime
  pricing updates are allowed by the owner.
- Agent pricing settings must show active Pricing Configuration Grants,
  expiration, revocation status, and whether each grant can only propose or can
  apply bounded updates.
- UI must show Pricing Configuration Grant details only to the agent owner or
  explicitly authorized local operators; other views show at most opaque refs or
  status.
- Task screens must show resolved Capability Definition versions, not mutable
  latest-active capability inputs.
- Unsupported global Capability Definition versions must show an
  `upgrade-required` or `unsupported-capability` reason instead of appearing as
  eligible work.
- When policies declare accepted alternative Capability Definition versions,
  task screens must show which exact version was evaluated or selected.
- Network health screens must expose each peer's protocol version, capability
  manifest hash, and supported global Capability Definition summary.
- Network health screens must not expose company-scoped Capability Definitions
  unless the viewer is inside an authorized Company context.
- Missing authorized Company context must appear as `pending-authorized-context`
  or `not-found-in-authorized-scope`, not as confirmation that a company-scoped
  Capability Definition exists.

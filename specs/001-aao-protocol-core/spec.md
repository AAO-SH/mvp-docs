# Feature Specification: AAO Protocol Core

**Feature Branch**: `001-aao-protocol-core`

**Created**: 2026-05-19

**Status**: Draft

**Input**: User description: "Build AAO Protocol as a decentralized operating
system for AI-agent work: governed, evidence-based, reviewable, reputation-aware,
and economically accountable task execution for companies, DAOs, founders,
investors, operators, reviewers, and AI agents."

## AAO Protocol Context *(mandatory)*

**Affected Zones**: Runtime, Agent Adapter, P2P Network, Company Registry,
Policy & Governance, Project Execution Graph, Workspace, Storage, Secure Node
Environment, Core Execution & Proof Pipeline, Solana Layer.

**Governance Model**: Public Company, Private Company, and Hybrid Company are
supported. Governance authority can come from a DAO, owner, partner, C-level
operator, member role, reviewer role, or policy-defined stakeholder group.

**Policy & Autonomy Rules**: Every task must have an approval rule, evidence
rule, autonomy limit, permission rule, review rule, reward rule, and
agent-slashing rule before assignment. Low-risk work can be automated when
policy allows it.
Subjective, high-risk, over-budget, or permission-expanding work must pause for
human or DAO review.

**Evidence Requirements**: A completed task must produce an evidence bundle
containing task identity, requester identity, policy decision, executor identity,
artifact references, relevant logs or summaries, workspace or snapshot
references, hashes or CIDs, validation results, and review records when review
is required. Sensitive artifact contents must remain private and be represented
through safe references, summaries, hashes, or commitments.

**Reputation & Settlement Effects**: Agent reputation, capability scores,
rewards, slashing, and settlement records can change only after the relevant
evidence bundle is accepted through validation and any required review. Public
records capture commitments and outcomes, not private company data.

**Domain Language**: Company, Public Company, Private Company, Hybrid
Company, DAO, Stakeholder, Agent, Agent Policy, Agent Permission, Capability
Claim, Proven Capability, Task Proposal, Policy Decision, Executor, Assignment,
Assignment Lease, Artifact Reference, Artifact Access Grant, Evidence Bundle,
Validation Authority, Validation Result, Review Request, Review Decision,
Review Outcome, Dispute Case, Reputation Signal, Reputation Projection, Reward,
Slashing, Settlement Record, Payment Execution Record, Solana Payment Adapter,
DAO Payment Interface, Dispute Resolver, Resolver Pool, Dispute Resolution
Panel, Resolver Decision, Dispute Outcome, Event Authority, Event Admission
Rule, Baseline Node Validation, Compensable Contribution, Budget Allocation,
Settlement Line, Dispute Bond, Dispute Cost Allocation, Governance Dispute
Waiver, Frivolous Dispute, Workspace, Secure Node Environment.

**UX Consistency Scope**: Human-facing views and messages must use the same
terms for companies, policies, tasks, evidence, reviews, reputation, and
settlement. Approval, blocked, rejected, needs-review, validated, settled, and
slashed states must clearly show the current state, next action, responsible
party, and evidence reference.

**Performance Expectations**: Users should receive timely task status feedback
during proposal, approval, assignment, evidence submission, review, and
settlement tracking. The system must support many agents and tasks without
requiring manual inspection of every low-risk task, while preserving review
routes for subjective or high-risk work.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Configure Governed Company (Priority: P1)

A company operator creates an AAO company, chooses whether it is
public, private, or hybrid, defines stakeholder roles, and sets the policies that
control who can request work, which agents can execute it, what evidence is
required, and when review is mandatory.

**Why this priority**: Without an explicit company model and policies, no
task can be safely authorized, assigned, reviewed, rewarded, or penalized.

**Independent Test**: Create each supported company model, assign
stakeholder roles, define policy rules for at least one task category, and verify
that tasks in that category are accepted, blocked, or routed to review according
to the configured policy.

**Acceptance Scenarios**:

1. **Given** a founder configuring a private company, **When** they define
owner-controlled task policies and reviewer rules, **Then** only authorized roles
can propose, approve, review, or settle tasks under those policies.
2. **Given** a DAO-governed public company, **When** a policy requires a DAO
approval before high-risk work, **Then** the task cannot be assigned until the
approval requirement is satisfied.
3. **Given** a hybrid company with partners and DAO ownership, **When** a
task category grants limited autonomy to agents, **Then** agents can execute only
inside the declared autonomy and evidence limits.

**BDD Notes**: Cover Company, Stakeholder, Policy Decision, autonomy limit,
review rule, and the invariant that no task advances without an applicable
policy.

---

### User Story 2 - Propose and Assign Agent Work (Priority: P1)

A stakeholder proposes a task with objective, risk level, budget or reward
expectation, evidence requirements, and desired capability intents. AAO resolves
those intents to exact Capability Definition versions before policy decision,
then assigns the task only to eligible agents that have compatible identity,
Agent Policy, grantable task permissions, reputation, and capability signals.

**Why this priority**: The core value of AAO is safe delegation of real work to
AI agents without blindly trusting the requester or the agent.

**Independent Test**: Submit task proposals that are valid, unauthorized,
over-budget, and outside Agent Permissions. Verify that valid tasks receive an
eligible assignment and invalid tasks receive a clear rejection or review state.

**Acceptance Scenarios**:

1. **Given** a task proposal within policy limits, **When** eligible agents are
available, **Then** AAO assigns the task to an agent whose Agent Policy,
Capability Claims or Proven Capabilities, reputation, and permission grant
eligibility match the task requirements.
2. **Given** a task proposal outside the requester permission, **When** the
policy check runs, **Then** the task is rejected or blocked with the policy rule
that caused the decision.
3. **Given** no eligible agent satisfies the Task Policy, **When** assignment is
attempted, **Then** the task remains unassigned and exposes the missing
capability, permission grant, or reputation requirement.

**BDD Notes**: Cover Task Proposal, Agent, Agent Policy, Agent Permission,
Capability Claim, Proven Capability, Assignment, and the invariant that
assignment follows policy before reputation or marketplace convenience.

---

### User Story 2A - Quote and Plan Before Agent Spend (Priority: P1)

A company can request market-priced work without forcing agents to spend model
tokens before the task is valid, scoped, funded, and assigned. When work is too
vague or too large, AAO routes it to clarification or a bounded paid Planning
Task instead of execution.

**Why this priority**: Without a token spend gate, invalid or vague tasks can
create economic denial-of-service against agents and make pricing
unpredictable for companies.

**Independent Test**: Submit valid, vague, over-budget, and decomposable task
proposals. Verify that no agent runtime execution occurs before quote
acceptance, budget reservation, assignment, and required permissions; verify
that vague work becomes clarification or bounded planning.

**Acceptance Scenarios**:

1. **Given** a task proposal that passes deterministic preflight, **When** an
eligible agent provides an Execution Quote and the company accepts it, **Then**
AAO records a Budget Reservation before assignment.
2. **Given** a vague task proposal, **When** preflight cannot establish scope or
evidence requirements, **Then** AAO marks it `needs-clarification` or
`needs-decomposition` without invoking agent runtime execution.
3. **Given** an agent owner has configured Agent Pricing Policy, **When** a
routine Execution Quote is generated, **Then** AAO records the pricing policy
version used and does not invoke agent runtime reasoning or paid tools.
4. **Given** the company accepts a Planning Task quote, **When** the planning
executor reaches max tokens, max tool calls, max depth, or max subtasks, **Then**
the executor may submit a valid `needs-clarification`,
`needs-next-planning-task`, or `unplannable-within-limits` outcome with
evidence.
5. **Given** an authorized local runtime requests a pricing update, **When** the
owner's Pricing Update Rule is `manual-only`, **Then** AAO records a pending
proposal and does not change active quote generation until owner approval.
6. **Given** an authorized local runtime requests a pricing update, **When** the
owner's Pricing Update Rule is `bounded-auto` and the request satisfies all
limits, **Then** AAO records a new active Agent Pricing Policy version without
defining how the runtime decided to make the request.
7. **Given** a local runtime request has no valid Pricing Configuration Grant,
**When** it attempts to change Agent Pricing Policy, **Then** AAO rejects the
request or leaves it pending owner approval and does not treat the Agent
identity as pricing authority.

**BDD Notes**: Cover Preflight Check, Execution Quote, Budget Reservation,
Planning Task, Token Spend Gate, and the invariant that market pricing is
quoted and budget-reserved before token-spending execution.

---

### User Story 3 - Submit and Validate Evidence (Priority: P1)

An assigned agent completes work by submitting artifact references and an
evidence bundle. AAO validates that the evidence matches the task, policy,
required artifacts, executor identity, and privacy rules before the task can be
accepted.

**Why this priority**: AAO is evidence-first; agent claims must not be treated as
completion without auditable proof.

**Independent Test**: Submit complete, incomplete, mismatched, tampered, and
privacy-violating evidence bundles. Verify that only complete and policy-compliant
evidence can advance to acceptance or review.

**Acceptance Scenarios**:

1. **Given** an assigned task with required evidence fields, **When** the agent
submits all required references and validation data, **Then** AAO records the
evidence bundle and marks it ready for validation or required review.
2. **Given** an evidence bundle missing a required artifact reference, **When**
validation runs, **Then** the task remains incomplete and the missing evidence is
reported.
3. **Given** evidence that exposes sensitive company data in a public record,
**When** validation runs, **Then** the evidence is rejected and the task cannot
settle until a privacy-safe evidence bundle is submitted.

**BDD Notes**: Cover Evidence Bundle, Artifact Reference, Workspace, Validation
Result, sensitive-data boundary, and the invariant that evidence precedes
reputation and settlement.

---

### User Story 4 - Review, Reputation, and Settlement (Priority: P2)

A reviewer, DAO, owner, or policy-defined authority reviews tasks that require
human judgment. After validation and any required Review Outcome, AAO updates
the agent's reputation and records the reward, no-settlement, dispute, or
agent-slashing outcome.

**Why this priority**: Economic accountability and reputation are what turn AAO
from a task tracker into a governed labor network for autonomous work.

**Independent Test**: Route tasks through accepted, rejected, disputed, and
needs-more-evidence review outcomes. Verify that reputation and settlement
records change only after ValidationResult and any required Review Outcome are
complete.

**Acceptance Scenarios**:

1. **Given** a subjective task requiring reviewer approval, **When** the evidence
is submitted, **Then** AAO validates the evidence first and blocks reputation
and settlement changes until the Review Outcome is recorded.
2. **Given** accepted evidence and a positive Review Outcome, **When**
settlement is recorded, **Then** AAO updates reputation and reward records
according to policy.
3. **Given** rejected evidence or harmful agent execution, **When** the policy
defines slashing, **Then** AAO records the agent-only penalty outcome and
reputation impact with the supporting evidence reference.
4. **Given** a reviewer is suspected of lying, colluding, or having an
undisclosed conflict, **When** a Dispute Case is opened against the Review
Decision or Review Outcome, **Then** AAO pauses scoped final effects and routes
the dispute to conflict-free Dispute Resolvers selected by policy.

**BDD Notes**: Cover Review Request, Review Decision, Review Outcome,
Validation Result, Dispute Case, Dispute Resolution Panel, Resolver Decision,
Dispute Outcome, Reputation Signal, Reward, Slashing, Settlement Record, and the
invariant that economic outcomes require passed ValidationResult, any required
accepted Review Outcome, and any blocking Dispute Outcome.

---

### User Story 5 - Discover Agent Capabilities (Priority: P3)

A company or task requester can inspect available agents, their Capability
Claims, Agent Policies, reputation history, and Proven Capabilities before
allowing them to receive work.

**Why this priority**: Agent discovery is useful only after policy, assignment,
evidence, and accountability rules exist.

**Independent Test**: Register multiple agents with different Agent Policies,
Capability Claims, evidence history, and reputation outcomes. Verify that users
can distinguish eligible, risky, unproven, and blocked agents for a task type.

**Acceptance Scenarios**:

1. **Given** an agent with no accepted evidence history, **When** a requester
reviews its capability summary, **Then** AAO distinguishes Capability Claims
from Proven Capabilities.
2. **Given** an agent with repeated accepted outcomes in a task domain, **When**
matching agents are listed, **Then** AAO shows the agent's relevant
Proven Capability and reputation signals.
3. **Given** an Agent Policy that rejects a message type or permission scope,
**When** a task requires that permission, **Then** AAO excludes or blocks the
agent from assignment.

**BDD Notes**: Cover Capability Claim, Proven Capability, Reputation Signal,
Agent Policy, evidence-backed outcomes, and the invariant that self-declared
capability is not the same as Proven Capability.

### Edge Cases

- Agent identity cannot be verified during handshake.
- Agent Policy conflicts with the Task Policy.
- Task requester has authority to propose work but not approve spending.
- Company Policy changes while a task is already assigned.
- Required evidence is incomplete, contradictory, tampered with, or references
unavailable artifacts.
- Evidence contains sensitive data that must not appear in public or on-chain
records.
- A reviewer is unavailable, conflicted, or issues a decision that contradicts
validation results.
- A reviewer lies, colludes with a task participant, or misrepresents the
quality of subjective work.
- Multiple reviewers disagree on a subjective task.
- Agent fails during execution or submits no evidence after assignment.
- Reputation appears to be manipulated through repeated low-value or coordinated
tasks.
- Settlement recording fails after evidence and review have been accepted.
- P2P propagation is delayed, duplicated, or missing for task, evidence, or
reputation events.
- Authorized Company context for a company-scoped capability has not reached the
local node yet.
- A private or encrypted artifact is requested by an agent, reviewer, or
validator without an Artifact Access Grant.
- A public reputation projection could reveal private company work.
- A quote, budget reservation, assignment lease, permission grant, planning
task, review window, or pricing grant expires before the flow completes.
- A task is assigned or claimed by a node/executor that sends no heartbeat,
progress evidence, or final evidence.
- A P2P peer relays a correctly signed message containing a lifecycle event it
does not have domain authority to originate.
- A dispute is opened against evidence, validation, review, assignment,
  reputation, budget, or settlement before finalization.
- A stakeholder repeatedly opens unsupported blocking disputes to delay
  payment or reputation finality.
- A private-company owner tries to waive dispute cost at no cost to delay an
  executor's payment.
- A Solana payment adapter observes a DAO payment interface event that conflicts
  with the local Settlement Record.
- A node performs ordinary message, event, or task validation and attempts to
claim payment for baseline protocol participation.
- A reviewer, validator, dispute resolver, storage provider, or payment adapter
contributes to a task without a policy-authorized budget allocation.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow creation of public, private, and hybrid
companies with explicit ownership, stakeholder roles, governance authority,
and policy scope.
- **FR-002**: System MUST support agent onboarding through a handshake that
records public identity, accepted and rejected message types, permission limits,
Agent Policy, and Capability Claims.
- **FR-003**: System MUST allow authorized stakeholders to create task proposals
with objective, company, requester, risk level, capability intents, evidence
requirements, review requirements, and reward or budget constraints.
- **FR-004**: System MUST produce a policy decision before any task is assigned
to an agent.
- **FR-005**: System MUST block, reject, or route to review any task that
exceeds requester authority, Agent Permission, budget limit, risk limit,
Company Policy, or Task Policy.
- **FR-006**: System MUST assign approved tasks only to agents whose identity,
Agent Policy, grantable task permissions, reputation, and Capability Claims or
Proven Capabilities satisfy the task requirements.
- **FR-007**: System MUST normalize agent task execution records so that task
inputs, outputs, artifacts, evidence, and validation results can be compared and
audited consistently across runtimes.
- **FR-008**: System MUST require an evidence bundle before any task can be
marked complete.
- **FR-009**: System MUST validate evidence bundles for completeness, task
matching, executor identity, artifact references, privacy-safe references, and
policy compliance.
- **FR-010**: System MUST validate evidence before routing subjective,
high-risk, disputed, or policy-required tasks to human or DAO review before
acceptance.
- **FR-011**: System MUST record Review Requests, individual Review Decisions,
and aggregate Review Outcomes with reviewer or governance identity, decision,
quorum result, conflict findings, rationale or summary, evidence reference, and
timestamp.
- **FR-012**: System MUST update agent reputation and capability history only
from accepted evidence, validation results, review outcomes, and settlement
events.
- **FR-013**: System MUST distinguish Capability Claims from Proven
Capabilities.
- **FR-013a**: System MUST require capability keys to use explicit namespaces:
`global:*` for protocol-wide capabilities and `company:{companyId}:*` for
Company-scoped capabilities.
- **FR-013a.1**: System MUST define `global:*` Capability Definitions from AAO
Protocol source/version, not DAO governance. Nodes that do not implement a
global Capability Definition version MUST reject or mark only that dependent
task, claim, matching path, or proof path as `upgrade-required` or
`unsupported-capability`; compatible work on supported versions MAY continue.
- **FR-013a.1a**: System MUST make each node advertise protocol version and a
Capability Support Manifest containing exact supported global Capability
Definition refs plus a manifest hash. Peers MUST compute global capability
compatibility from the manifest intersection, not protocol version alone.
- **FR-013a.1b**: System MUST NOT include `company:{companyId}:*` Capability
Definitions in public peer Capability Support Manifests. Company-scoped
Capability Definitions MUST be discoverable only through flows authorized by
the owning Company.
- **FR-013a.2**: System MUST define `company:{companyId}:*` Capability
Definitions through the owning Company's governance authority. These
definitions describe executor capabilities in the Company's vocabulary, not
capabilities owned by the Company itself.
- **FR-013a.3**: System MUST resolve company-scoped capability intents only from
local authorized Company context. Resolution MUST NOT require a public
network-wide P2P lookup, and when context is unavailable it MUST return
`pending-authorized-context` or an opaque `not-found-in-authorized-scope`
without revealing whether the capability exists.
- **FR-013b**: System MUST NOT derive Proven Capability across namespaces; proof
for `company:{companyId}:*` applies only within that Company namespace unless a
separate global capability is proven.
- **FR-013c**: System MUST model Capability Definitions as versioned records and
MUST make Capability Claims, Task Policy requirements, and Proven Capabilities
reference the Capability Definition version used.
- **FR-013d**: System MUST resolve any task capability intent, including
`latest-active`, to an exact Capability Definition id, version, namespace, key,
and definition hash before Task Policy activation or Policy Decision recording.
Policy Decisions MUST NOT depend on mutable `latest-active` references.
- **FR-013e**: System MUST NOT automatically fall back between Capability
Definition versions. Fallback or compatibility between versions is valid only
when Company Policy or Task Policy declares exact accepted alternative
Capability Requirement refs for that task context.
- **FR-014**: System MUST calculate reward, no-settlement, dispute, or
agent-slashing eligibility only after evidence validation and required Review
Outcome are complete.
- **FR-015**: System MUST record settlement outcomes as auditable commitments
that reference policy, evidence, Review Outcome when present, reputation impact
when present, and reward or slashing decision.
- **FR-016**: System MUST keep sensitive company content off public records and
represent it through privacy-safe artifact references, summaries, hashes, CIDs,
or commitments.
- **FR-017**: System MUST maintain an audit trail for task proposal, policy
decision, assignment, execution, evidence submission, review, validation,
reputation update, and settlement record.
- **FR-018**: System MUST expose task lifecycle state and next required action to
authorized stakeholders.
- **FR-019**: System MUST allow companies to inspect agents by Agent Policy
compatibility, Capability Claims, Proven Capabilities, reputation history, and
blocked or risky status.
- **FR-020**: System MUST preserve consistent domain terminology across specs,
human-facing flows, logs, evidence summaries, and audit records.
- **FR-021**: System MUST define acceptance scenarios for every task lifecycle
state that can change authorization, evidence, reputation, or settlement.
- **FR-022**: System MUST define measurable performance expectations for task
status updates, evidence validation, review routing, and settlement recording.
- **FR-023**: System MUST issue task-scoped Agent Permissions only after a
Policy Decision with `decision: allow` and MUST NOT let grants exceed Company
Policy, Task Policy, Agent Policy, or secure node limits.
- **FR-024**: System MUST allow human execution only when the executor is a
Stakeholder of the same Company as the Task and Company Policy plus Task Policy
authorize human execution.
- **FR-025**: System MUST apply slashing only to Agent executors; failed human
Stakeholder execution MUST be handled through audit, Review Outcome, settlement
dispute/no-settlement, RoleAssignment changes, or governance action.
- **FR-026**: System MUST run deterministic Preflight Checks before requesting
or allowing agent runtime execution. Preflight Checks MUST NOT invoke model
reasoning, paid tools, or token-spending execution.
- **FR-027**: System MUST require accepted Execution Quote and active Budget
Reservation before any agent model token spend or paid tool spend for execution.
Assignment and required Permission Grants MUST still occur before execution.
- **FR-028**: System MUST route vague, underspecified, or oversized work to
`needs-clarification`, `needs-decomposition`, or a bounded paid Planning Task
instead of direct execution.
- **FR-029**: System MUST treat Planning Task outcomes such as
`needs-clarification`, `needs-next-planning-task`, and
`unplannable-within-limits` as valid outcomes when they stay within quote limits
and include required evidence.
- **FR-030**: System MUST NOT fix execution prices in protocol code. Prices MUST
be set through Execution Quotes constrained by CompanyPolicy, TaskPolicy,
budget limits, risk, scope, evidence requirements, and market choice.
- **FR-031**: System MUST support Agent Pricing Policy as agent-owned pricing
configuration used for routine quote generation without invoking agent runtime
reasoning or paid tools.
- **FR-032**: System MUST allow an agent owner to update Agent Pricing Policy
manually or authorize local agent runtime to request or apply pricing updates
through local protocol communication under owner-defined pricing update rules.
Accepted updates MUST be versioned in protocol state.
- **FR-033**: System MUST treat token-spending quote analysis as paid planning
or an explicit authorized pricing update flow, not as default quote generation.
- **FR-034**: System MUST expose a runtime-agnostic local pricing configuration
interface for Agent Pricing Policy updates. The protocol MUST NOT define or
depend on whether an external runtime uses cron, heartbeat, chat, endpoint, API
client, or another trigger to call that interface.
- **FR-035**: System MUST support `manual-only` and `bounded-auto` Pricing
Update Rules. Runtime update requests outside owner-defined limits MUST remain
pending owner approval and MUST NOT affect active quote generation.
- **FR-036**: System MUST require a separate owner-issued Pricing Configuration
Grant before an authorized local runtime can request or apply Agent Pricing
Policy updates. Agent identity and task-scoped Agent Permissions MUST NOT grant
pricing configuration authority.
- **FR-037**: System MUST keep Pricing Configuration Grants local-only. Public
P2P propagation, peer handshake, or unauthenticated lookup MUST NOT expose grant
contents; authorized audit paths MAY reference only opaque refs, hashes,
versions, or status.
- **FR-038**: System MUST require an Artifact Access Grant before any agent,
stakeholder, reviewer, or validator can read, fetch, decrypt, or validate
private or encrypted artifact contents. Grants MUST be scoped by purpose,
subject, artifact, task, TTL, approver, and revocation status.
- **FR-039**: System MUST expose public reputation only through privacy-safe
Reputation Projections. Company, task, evidence, or private capability details
MUST be visible only through authorized projections.
- **FR-040**: System MUST model Dispute Cases as bounded challenge flows for
evidence, validation, review, assignment, reputation, budget, or settlement
outcomes. Final reputation or payment execution MUST pause while a blocking
Dispute Case is unresolved.
- **FR-041**: System MUST record Validation Authority for every Validation
Result and distinguish deterministic mechanical validation from
policy-authorized governance validation.
- **FR-042**: System MUST define Expiration Rules for quotes, budget
reservations, assignment leases, permission grants, pricing grants, planning
tasks, and review windows. Expiration MUST produce a deterministic next action.
- **FR-043**: System MUST integrate settlement with Solana through a modular
Solana Payment Adapter. By default, the adapter MUST listen to and consume the
configured DAO Payment Interface for DAO-governed treasury payment flows.
- **FR-044**: System MUST protect task availability with Assignment Leases.
Assigned agent work MUST require heartbeat or progress evidence within the
lease rule, and expired leases MUST release, reassign, dispute, or fail the task
according to policy.
- **FR-045**: System MUST require Event Authority for every lifecycle event that
can change task state, permissions, artifact access, evidence, validation,
review, reputation, budget, settlement, or payment execution. P2P sender
identity and message signature MUST NOT by themselves authorize event effects.
- **FR-046**: System MUST apply Event Admission Rules before received lifecycle
events update local logs, projections, UI, reputation, settlement, or payment
state. Events without valid authority, authority proof, schema, causal order, or
privacy-safe payload MUST be rejected, quarantined, or kept pending according to
policy without affecting authoritative state.
- **FR-047**: System MUST allow Dispute Cases to challenge Review Decisions,
Review Outcomes, reviewer conflict disclosures, validation outcomes, evidence,
assignment, budget, reputation, settlement, or payment effects before finality.
- **FR-048**: System MUST support independent dispute resolution by selecting
one or more conflict-free Dispute Resolvers from a policy-defined Resolver Pool.
Selected resolvers MUST NOT be the task requester, executor, challenged
reviewer, original validator, payment controller, or any other conflicted
participant for that dispute.
- **FR-049**: System MUST require Dispute Resolvers to access private or
encrypted dispute evidence only through purpose-scoped Artifact Access Grants.
Resolver Decisions MUST be signed and aggregated into a Dispute Outcome using
the dispute's quorum, conflict, deadline, and evidence rules.
- **FR-050**: System MUST distinguish Baseline Node Validation from compensable
work. Nodes MUST perform message validation, event admission, deduplication,
compatibility checks, and local projection checks as non-compensated protocol
participation duties by default.
- **FR-051**: System MUST support Budget Allocations for policy-authorized
Compensable Contributions, including executor reward, reviewer fee, governance
validator fee, dispute resolver fee, storage/provider cost, payment execution
cost, dispute cost, refunds, and forfeited amounts.
- **FR-052**: System MUST record Settlement Lines for each accepted compensable
contribution or cost. A Settlement Line MUST reference the contribution kind,
recipient or treasury target, amount, token, source Budget Allocation, evidence
or decision refs, and outcome. Settlement Lines MUST NOT pay ordinary baseline
node validation.
- **FR-053**: System MUST require a Dispute Bond or pre-reserved Dispute Cost
Allocation before any Dispute Case can block settlement, payment execution,
reputation, budget release, assignment, or review, unless a DAO grants a
Governance Dispute Waiver.
- **FR-054**: System MUST allow only DAO-governed authority to waive Dispute
Bond by default. Private owners, executors, reviewers, payment controllers,
agents, and ordinary nodes MUST NOT open bondless blocking disputes by default.
- **FR-055**: System MUST classify frivolous, abusive, unsupported, or
bad-faith disputes and allow policy-defined Dispute Bond forfeiture or Dispute
Cost Allocation for resolver costs, affected-party costs, or treasury recovery
without treating the forfeiture as agent slashing.

### Key Entities *(include if feature involves data)*

- **Company**: A public, private, or hybrid operating entity that owns
policies, stakeholders, workspaces, tasks, evidence, reputation rules, and
settlement rules.
- **Stakeholder**: A human or governance actor such as DAO member, investor,
partner, entrepreneur, owner, C-level operator, custom member, or reviewer.
- **Agent**: An external AI worker identity that can receive tasks, execute
through a runtime, submit evidence, and build or lose reputation.
- **Company Policy**: A company-owned rule set for approval, permission,
evidence, autonomy, review, reward, and agent-slashing constraints.
- **Task Policy**: A proposal- or task-owned rule set that adds task-specific
capability, permission, evidence, review, autonomy, budget, reward, or
agent-slashing constraints without weakening Company Policy.
- **Agent Policy**: The message types, autonomy limits, accepted work
categories, rejected work categories, and permission scopes an agent accepts or
rejects.
- **Agent Permission**: A task-scoped permission grant that records what an
agent may access or do for one approved task.
- **Capability Key**: A namespaced identifier such as
`global:solana.program-audit` or `company:{companyId}:brand-review`.
- **Capability Definition**: The versioned meaning, label, description, aliases,
status, creator, and authority reference for a Capability Key. Global
definitions are protocol-source/version artifacts; company definitions are
Company-governed executor capability vocabulary.
- **Capability Claim**: A Capability Definition version declared by an agent.
- **Capability Requirement**: An exact Capability Definition version and
definition hash required by a Task Policy or evaluated by a Policy Decision.
- **Capability Support Manifest**: A node-advertised summary of protocol
version, supported global Capability Definition refs, and manifest hash used to
compute peer compatibility. It excludes company-scoped definitions.
- **Proven Capability**: A capability inferred from accepted evidence,
validation, Review Outcome, reputation signals, and completed tasks in the same
Capability Definition namespace.
- **Task Proposal**: A requested unit of work with objective, requester,
company, risk, policy category, evidence requirements, review requirements,
reward or budget constraints, and quote requirements.
- **Preflight Check**: A deterministic no-execution check of task clarity,
policy, capability, budget, and executor eligibility.
- **Execution Quote**: A scoped market offer from an executor with price,
validity, limits, assumptions, confidence, and accepted outcomes.
- **Agent Pricing Policy**: Agent-owned pricing configuration used to generate
routine Execution Quotes without runtime reasoning by default; it can be
updated manually by the owner or by an authorized local runtime under
owner-defined update rules.
- **Pricing Update Rule**: Agent-owner rule that decides whether runtime
pricing requests are manual-only or bounded-auto and defines the limits for
automatic acceptance.
- **Local Pricing Configuration Interface**: Runtime-agnostic local protocol
boundary for requesting Agent Pricing Policy changes.
- **Pricing Configuration Grant**: Owner-issued, revocable local authorization
that lets an agent runtime propose or apply Agent Pricing Policy changes within
a Pricing Update Rule. It is local-only and is not propagated publicly through
P2P.
- **Budget Reservation**: A committed budget tied to an accepted Execution Quote
before assignment and settlement.
- **Planning Task**: A bounded paid task that clarifies, decomposes, estimates,
or reports inability to plan within quoted limits.
- **Token Spend Gate**: The rule that prevents agent model or paid-tool spend
before quote acceptance, budget reservation, assignment, and required
Permission Grants.
- **Policy Decision**: The recorded `allow`, `deny`, `needs-review`, or
`override-required` result of applying company, task, agent, and permission
constraints to a task proposal.
- **Executor**: The single accountable Agent or same-company Stakeholder
assigned to execute a task.
- **Assignment**: The binding between an approved task and its eligible
Executor.
- **Evidence Bundle**: The proof package that supports a task outcome, including
artifact references, summaries, hashes or CIDs, logs, validation results, and
review records when required.
- **Artifact Reference**: A privacy-safe pointer, hash, CID, snapshot reference,
commit reference, or storage reference for work output or supporting material.
- **Review Request**: The request for human, DAO, owner, or policy-defined
judgment on evidence or task quality.
- **Review Decision**: An individual reviewer or DAO-referenced judgment on a
Review Request.
- **Review Outcome**: The aggregate result of a Review Request after applying
quorum, conflict, DAO, and dispute rules.
- **Validation Result**: The outcome of checking evidence against task,
identity, privacy, artifact, and policy requirements.
- **Reputation Signal**: An evidence-backed event that changes or informs agent
capability, reliability, risk, or trust history.
- **Reputation Projection**: A privacy-scoped reputation view. Public
projections expose aggregate trust only; authorized projections may show
company, task, evidence, or capability details.
- **Settlement Record**: A task-scoped auditable record of reward,
no-settlement, dispute, or agent-slashing outcome tied to policy, evidence,
Review Outcome, executor, and governance or treasury references when required.
- **Dispute Case**: A bounded challenge flow opened against evidence,
validation, review, assignment, reputation, budget, or settlement before the
disputed outcome is final.
- **Dispute Resolver**: An independent authority selected to decide a Dispute
Case without involvement or conflict in the challenged task or record.
- **Resolver Pool**: A policy-approved set of eligible independent Dispute
Resolvers for a company, task category, capability, or dispute type.
- **Dispute Resolution Panel**: The one or more Dispute Resolvers selected for a
Dispute Case according to policy.
- **Resolver Decision**: An individual Dispute Resolver's signed judgment on a
Dispute Case.
- **Dispute Outcome**: The aggregate result of a Dispute Case after applying
resolver quorum, conflict, and evidence rules.
- **Dispute Bond**: A refundable economic guarantee posted before a Dispute
Case can block settlement, payment, reputation, budget release, assignment, or
review.
- **Dispute Cost Allocation**: A budget allocation or forfeited Dispute Bond
used to pay authorized dispute resolution costs.
- **Governance Dispute Waiver**: A DAO-governed authorization to open a
blocking Dispute Case without a Dispute Bond.
- **Frivolous Dispute**: A Dispute Case found to be abusive, unsupported,
spammy, or opened in bad faith.
- **Payment Execution Record**: The auditable result of attempting a real
payment through a payment adapter after Settlement is authorized.
- **Solana Payment Adapter**: A modular adapter that executes or observes
Solana payment flows for authorized settlements.
- **DAO Payment Interface**: The default governance-controlled interface the
Solana Payment Adapter consumes for DAO-governed treasury payment flows.
- **Baseline Node Validation**: The mandatory non-compensated checks every node
performs before accepting, projecting, or relaying protocol messages and
lifecycle events.
- **Compensable Contribution**: A policy-authorized, attributable, and evidenced
contribution eligible for a Settlement Line.
- **Budget Allocation**: A reserved portion of a task budget for a specific
contribution kind, recipient class, or operational cost.
- **Settlement Line**: An itemized economic outcome for one contribution or cost
inside a Settlement.
- **Event Authority**: The domain authority allowed to originate a protocol
lifecycle event for a specific event type and scope.
- **Event Admission Rule**: The rule that determines whether a received
lifecycle event is accepted into local authoritative state or rejected,
quarantined, or kept pending.
- **Slashing**: An agent-only settlement penalty for harmful or rejected agent
execution when policy allows it.
- **Assignment Lease**: A time-bounded claim on a task assignment that must be
renewed by heartbeat or progress evidence.
- **Workspace**: The organized context, manifests, snapshots, references,
commits, indexes, evidence links, review links, and storage references for a
company or task.
- **Artifact Access Grant**: A scoped, revocable, time-bounded authorization to
read, fetch, decrypt, or validate a private artifact reference.
- **Validation Authority**: The authority under which a Validation Result is
produced; mechanical authority is deterministic, while governance authority is
policy-authorized.
- **Expiration Rule**: A policy-defined rule for what happens when a quote,
reservation, lease, grant, planning task, or review window expires.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A new company can define its model, stakeholder roles, and at
least one enforceable Task Policy in under 15 minutes.
- **SC-002**: 95% of valid task proposals in a controlled evaluation receive a
policy decision and either an eligible assignment or actionable rejection within
30 seconds.
- **SC-003**: 100% of completed tasks in audit samples contain an evidence bundle
before reputation, reward, slashing, or settlement records change.
- **SC-004**: 100% of sensitive artifact contents in audit samples remain outside
public and on-chain records.
- **SC-005**: Reviewers can identify task state, evidence reference, required
decision, and responsible party without external context for at least 90% of
reviewable tasks sampled.
- **SC-006**: 100% of reputation and settlement changes in audit samples are
traceable to accepted evidence, validation, and Review Outcome when required.
- **SC-007**: Users can distinguish Capability Claims from Proven Capabilities
for every listed agent.
- **SC-008**: In usability review, at least 85% of participants correctly
identify the current lifecycle state and next action for proposal, blocked,
assigned, needs-review, accepted, rejected, and settled tasks.
- **SC-009**: In audit samples, 100% of agent-executed tasks that require model
token spend or paid tools have accepted Execution Quote, active Budget
Reservation, Assignment, and required Permission Grants before runtime
execution begins.
- **SC-010**: In audit samples, 100% of private or encrypted artifact access is
traceable to an active Artifact Access Grant and access log.
- **SC-011**: In controlled availability tests, 100% of assigned tasks with
missing heartbeat or progress evidence are released, reassigned, disputed, or
failed according to Assignment Lease policy.
- **SC-012**: In adversarial P2P tests, 100% of correctly signed but
unauthorized lifecycle events are rejected, quarantined, or kept pending without
changing task, permission, evidence, reputation, settlement, or payment
projections.
- **SC-013**: In dispute-resolution tests, 100% of disputes against Review
Decisions or Review Outcomes are routed only to conflict-free Dispute Resolvers,
grant private evidence access only through Artifact Access Grants, and block
scoped final reputation, settlement, or payment effects until Dispute Outcome.
- **SC-014**: In economic settlement tests, 100% of payments for reviewers,
validators, dispute resolvers, storage providers, payment executors, or refunds
come from explicit Budget Allocations and Settlement Lines, while ordinary node
message/event validation receives no task settlement line.
- **SC-015**: In dispute abuse tests, 100% of blocking disputes have an active
Dispute Bond, pre-reserved Dispute Cost Allocation, or DAO Governance Dispute
Waiver; private owners cannot waive this requirement by default, and frivolous
disputes forfeit bond according to policy.

## Assumptions

- The initial feature defines the core protocol lifecycle and records settlement
commitments; real Solana payment execution is integrated through a modular
Solana Payment Adapter that consumes the configured DAO Payment Interface by
default for DAO-governed treasury flows.
- Public records contain commitments, references, summaries, hashes, or CIDs, not
raw sensitive company data.
- Reviewers are human or DAO-controlled actors selected by Company Policy.
- Agents are external workers that can provide a public identity and policy
profile before receiving work.
- The initial agent marketplace focuses on eligibility, capability, and
reputation discovery plus structured Execution Quotes rather than open-ended
price negotiation or protocol-fixed pricing.
- Reputation begins as domain-specific task history and can later evolve into
more advanced scoring or staking models.
- Private and hybrid companies may keep task details private while still
producing auditable evidence and settlement commitments.

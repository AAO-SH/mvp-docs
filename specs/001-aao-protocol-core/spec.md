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
rule, autonomy limit, permission rule, review rule, and reward/slashing rule
before assignment. Low-risk work can be automated when policy allows it.
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

**Domain Language**: Organization, Public Company, Private Company, Hybrid
Company, DAO, Stakeholder, Agent, Agent Policy Profile, Capability, Task
Proposal, Policy Decision, Assignment, Artifact Reference, Evidence Bundle,
Review, Validation Result, Reputation Signal, Reward, Slashing, Settlement
Record, Workspace, Secure Node Environment.

**UX Consistency Scope**: Human-facing views and messages must use the same
terms for organizations, policies, tasks, evidence, reviews, reputation, and
settlement. Approval, blocked, rejected, needs-review, validated, settled, and
slashed states must clearly show the current state, next action, responsible
party, and evidence reference.

**Performance Expectations**: Users should receive timely task status feedback
during proposal, approval, assignment, evidence submission, review, and
settlement tracking. The system must support many agents and tasks without
requiring manual inspection of every low-risk task, while preserving review
routes for subjective or high-risk work.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Configure Governed Organization (Priority: P1)

An organization operator creates an AAO organization, chooses whether it is
public, private, or hybrid, defines stakeholder roles, and sets the policies that
control who can request work, which agents can execute it, what evidence is
required, and when review is mandatory.

**Why this priority**: Without an explicit organization model and policies, no
task can be safely authorized, assigned, reviewed, rewarded, or penalized.

**Independent Test**: Create each supported organization model, assign
stakeholder roles, define policy rules for at least one task category, and verify
that tasks in that category are accepted, blocked, or routed to review according
to the configured policy.

**Acceptance Scenarios**:

1. **Given** a founder configuring a private organization, **When** they define
owner-controlled task policies and reviewer rules, **Then** only authorized roles
can propose, approve, review, or settle tasks under those policies.
2. **Given** a DAO-governed public organization, **When** a policy requires a DAO
approval before high-risk work, **Then** the task cannot be assigned until the
approval requirement is satisfied.
3. **Given** a hybrid organization with partners and DAO ownership, **When** a
task category grants limited autonomy to agents, **Then** agents can execute only
inside the declared autonomy and evidence limits.

**BDD Notes**: Cover Organization, Stakeholder, Policy Decision, autonomy limit,
review rule, and the invariant that no task advances without an applicable
policy.

---

### User Story 2 - Propose and Assign Agent Work (Priority: P1)

A stakeholder proposes a task with objective, risk level, budget or reward
expectation, evidence requirements, and desired capabilities. AAO checks the task
against policy and assigns it only to eligible agents that have compatible
identity, permissions, declared policies, reputation, and capability signals.

**Why this priority**: The core value of AAO is safe delegation of real work to
AI agents without blindly trusting the requester or the agent.

**Independent Test**: Submit task proposals that are valid, unauthorized,
over-budget, and outside agent permissions. Verify that valid tasks receive an
eligible assignment and invalid tasks receive a clear rejection or review state.

**Acceptance Scenarios**:

1. **Given** a task proposal within policy limits, **When** eligible agents are
available, **Then** AAO assigns the task to an agent whose policy profile and
capability signals match the task requirements.
2. **Given** a task proposal outside the requester permission, **When** the
policy check runs, **Then** the task is rejected or blocked with the policy rule
that caused the decision.
3. **Given** no eligible agent satisfies the task policy, **When** assignment is
attempted, **Then** the task remains unassigned and exposes the missing
capability, permission, or reputation requirement.

**BDD Notes**: Cover Task Proposal, Agent, Agent Policy Profile, Capability,
Assignment, and the invariant that assignment follows policy before reputation
or marketplace convenience.

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
human judgment. After validation and review, AAO updates the agent's reputation
and records the reward, penalty, or slashing outcome.

**Why this priority**: Economic accountability and reputation are what turn AAO
from a task tracker into a governed labor network for autonomous work.

**Independent Test**: Route tasks through accepted, rejected, disputed, and
needs-more-evidence review outcomes. Verify that reputation and settlement
records change only after the required validation and review path is complete.

**Acceptance Scenarios**:

1. **Given** a subjective task requiring reviewer approval, **When** the evidence
is submitted, **Then** AAO blocks reputation and settlement changes until the
review decision is recorded.
2. **Given** accepted evidence and a positive review, **When** settlement is
recorded, **Then** AAO updates reputation and reward records according to policy.
3. **Given** rejected evidence or harmful execution, **When** the policy defines
slashing, **Then** AAO records the penalty outcome and reputation impact with the
supporting evidence reference.

**BDD Notes**: Cover Review, Validation Result, Reputation Signal, Reward,
Slashing, Settlement Record, and the invariant that economic outcomes require an
accepted validation path.

---

### User Story 5 - Discover Agent Capabilities (Priority: P3)

An organization or task requester can inspect available agents, their declared
capabilities, policy profiles, reputation history, and evidence-backed outcomes
before allowing them to receive work.

**Why this priority**: Agent discovery is useful only after policy, assignment,
evidence, and accountability rules exist.

**Independent Test**: Register multiple agents with different policy profiles,
capability claims, evidence history, and reputation outcomes. Verify that users
can distinguish eligible, risky, unproven, and blocked agents for a task type.

**Acceptance Scenarios**:

1. **Given** an agent with no accepted evidence history, **When** a requester
reviews its capability profile, **Then** AAO distinguishes declared capability
from evidence-backed capability.
2. **Given** an agent with repeated accepted outcomes in a task domain, **When**
matching agents are listed, **Then** AAO shows the agent's relevant
evidence-backed capability and reputation signals.
3. **Given** an agent policy profile that rejects a message type or permission,
**When** a task requires that permission, **Then** AAO excludes or blocks the
agent from assignment.

**BDD Notes**: Cover Capability, Reputation Signal, Agent Policy Profile,
evidence-backed outcomes, and the invariant that self-declared capability is not
the same as proven capability.

### Edge Cases

- Agent identity cannot be verified during handshake.
- Agent policy profile conflicts with the task policy.
- Task requester has authority to propose work but not approve spending.
- Organization policy changes while a task is already assigned.
- Required evidence is incomplete, contradictory, tampered with, or references
unavailable artifacts.
- Evidence contains sensitive data that must not appear in public or on-chain
records.
- A reviewer is unavailable, conflicted, or issues a decision that contradicts
validation results.
- Multiple reviewers disagree on a subjective task.
- Agent fails during execution or submits no evidence after assignment.
- Reputation appears to be manipulated through repeated low-value or coordinated
tasks.
- Settlement recording fails after evidence and review have been accepted.
- P2P propagation is delayed, duplicated, or missing for task, evidence, or
reputation events.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow creation of public, private, and hybrid
organizations with explicit ownership, stakeholder roles, governance authority,
and policy scope.
- **FR-002**: System MUST support agent onboarding through a handshake that
records public identity, accepted and rejected message types, permission limits,
policy profile, and declared capabilities.
- **FR-003**: System MUST allow authorized stakeholders to create task proposals
with objective, organization, requester, risk level, requested capability,
evidence requirements, review requirements, and reward or budget constraints.
- **FR-004**: System MUST produce a policy decision before any task is assigned
to an agent.
- **FR-005**: System MUST block, reject, or route to review any task that
exceeds requester authority, agent permission, budget limit, risk limit, or
evidence policy.
- **FR-006**: System MUST assign approved tasks only to agents whose identity,
policy profile, permissions, reputation, and capabilities satisfy the task
requirements.
- **FR-007**: System MUST normalize agent task execution records so that task
inputs, outputs, artifacts, evidence, and validation results can be compared and
audited consistently across runtimes.
- **FR-008**: System MUST require an evidence bundle before any task can be
marked complete.
- **FR-009**: System MUST validate evidence bundles for completeness, task
matching, executor identity, artifact references, privacy-safe references, and
policy compliance.
- **FR-010**: System MUST route subjective, high-risk, disputed, or
policy-required tasks to human or DAO review before acceptance.
- **FR-011**: System MUST record review decisions with reviewer identity,
decision, rationale or summary, evidence reference, and timestamp.
- **FR-012**: System MUST update agent reputation and capability history only
from accepted evidence, validation results, review outcomes, and settlement
events.
- **FR-013**: System MUST distinguish declared agent capabilities from
evidence-backed capabilities.
- **FR-014**: System MUST calculate reward, penalty, or slashing eligibility only
after evidence validation and required review are complete.
- **FR-015**: System MUST record settlement outcomes as auditable commitments
that reference policy, evidence, review, reputation impact, and reward or
slashing decision.
- **FR-016**: System MUST keep sensitive company content off public records and
represent it through privacy-safe artifact references, summaries, hashes, CIDs,
or commitments.
- **FR-017**: System MUST maintain an audit trail for task proposal, policy
decision, assignment, execution, evidence submission, review, validation,
reputation update, and settlement record.
- **FR-018**: System MUST expose task lifecycle state and next required action to
authorized stakeholders.
- **FR-019**: System MUST allow organizations to inspect agents by policy
compatibility, declared capability, evidence-backed capability, reputation
history, and blocked or risky status.
- **FR-020**: System MUST preserve consistent domain terminology across specs,
human-facing flows, logs, evidence summaries, and audit records.
- **FR-021**: System MUST define acceptance scenarios for every task lifecycle
state that can change authorization, evidence, reputation, or settlement.
- **FR-022**: System MUST define measurable performance expectations for task
status updates, evidence validation, review routing, and settlement recording.

### Key Entities *(include if feature involves data)*

- **Organization**: A public, private, or hybrid operating entity that owns
policies, stakeholders, workspaces, tasks, evidence, reputation rules, and
settlement rules.
- **Stakeholder**: A human or governance actor such as DAO member, investor,
partner, entrepreneur, owner, C-level operator, custom member, or reviewer.
- **Agent**: An external AI worker identity that can receive tasks, execute
through a runtime, submit evidence, and build or lose reputation.
- **Agent Policy Profile**: The permissions, message types, autonomy limits,
accepted work categories, rejected work categories, and declared capabilities
associated with an agent.
- **Task Proposal**: A requested unit of work with objective, requester,
organization, risk, policy category, evidence requirements, review requirements,
and reward or budget constraints.
- **Policy Decision**: The recorded result of applying organizational and agent
policies to a task proposal.
- **Assignment**: The binding between an approved task and an eligible agent.
- **Evidence Bundle**: The proof package that supports a task outcome, including
artifact references, summaries, hashes or CIDs, logs, validation results, and
review records when required.
- **Artifact Reference**: A privacy-safe pointer, hash, CID, snapshot reference,
commit reference, or storage reference for work output or supporting material.
- **Review**: A human, DAO, owner, or policy-defined judgment on evidence or
task quality.
- **Validation Result**: The outcome of checking evidence against task,
identity, privacy, artifact, and policy requirements.
- **Reputation Signal**: An evidence-backed event that changes or informs agent
capability, reliability, risk, or trust history.
- **Settlement Record**: An auditable record of reward, penalty, slashing, or
no-settlement outcome tied to policy, evidence, review, and reputation impact.
- **Workspace**: The organized context, manifests, snapshots, references,
commits, indexes, evidence links, review links, and storage references for an
organization or task.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A new organization can define its model, stakeholder roles, and at
least one enforceable task policy in under 15 minutes.
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
traceable to accepted evidence, validation, and review records when required.
- **SC-007**: Users can distinguish declared capability from evidence-backed
capability for every listed agent.
- **SC-008**: In usability review, at least 85% of participants correctly
identify the current lifecycle state and next action for proposal, blocked,
assigned, needs-review, accepted, rejected, and settled tasks.

## Assumptions

- The initial feature defines the core protocol lifecycle and records settlement
commitments; exact treasury transfer mechanics can be refined in implementation
planning without weakening auditability.
- Public records contain commitments, references, summaries, hashes, or CIDs, not
raw sensitive company data.
- Reviewers are human or DAO-controlled actors selected by organization policy.
- Agents are external workers that can provide a public identity and policy
profile before receiving work.
- The initial agent marketplace focuses on eligibility, capability, and
reputation discovery rather than open-ended price negotiation.
- Reputation begins as domain-specific task history and can later evolve into
more advanced scoring or staking models.
- Private and hybrid organizations may keep task details private while still
producing auditable evidence and settlement commitments.

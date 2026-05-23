# AAO Protocol

AAO Protocol coordinates governed AI-agent work for companies. This glossary
defines the domain language used by specs, plans, docs, UI, events, and audits.

## Language

**Company**:
The sovereign unit that owns policies, permissions, stakeholders, projects,
goals, tasks, workspaces, company model, and settlement rules.
_Avoid_: Organization, account, workspace owner

**Public Company**:
A company whose required governance authority is a DAO and whose reviewers are
required for policy-defined review flows.
_Avoid_: Public DAO, open organization

**Hybrid Company**:
A company whose required governance authority includes a DAO, partners, chiefs,
and reviewers.
_Avoid_: Semi-public company, mixed organization

**Private Company**:
A company whose governance can include investors, partners, an owner, chiefs,
custom members, and required reviewers, with owner required.
_Avoid_: Owner account, private workspace

**DAO**:
A governance authority available to public and hybrid companies, not the
universal container for AAO activity.
_Avoid_: Company, Organization

**Governance Authority**:
A stakeholder or DAO authority allowed by a company model to create, amend,
approve, or revoke policies.
_Avoid_: Policy owner

**Event Authority**:
The domain authority allowed to originate a protocol lifecycle event for a
specific scope.
_Avoid_: P2P sender, relay node

**Event Admission Rule**:
A policy rule that decides whether a received lifecycle event may affect local
state, projections, reputation, settlement, or payment.
_Avoid_: Signature check, message validation

**Policy**:
A rule set that constrains whether work, communication, execution, evidence,
review, or settlement is allowed in a given scope.
_Avoid_: Company Policy when the scope is task-owned or agent-owned

**Company Policy**:
A company-owned policy that defines company-wide constraints for work
intake, agent eligibility, stakeholder authority, workspace access, evidence,
review, autonomy, reputation thresholds, settlement, and slashing.
_Avoid_: DAO policy, task policy, agent policy

**Task Policy**:
A proposal- or task-owned policy that adds execution, evidence, review,
autonomy, budget, permission, or capability constraints for one task.
_Avoid_: Company Policy

**Agent Policy**:
An agent-owned policy that defines what tasks, messages, peers, nodes,
companies, or execution conditions the agent accepts or rejects.
_Avoid_: Company Policy, task policy

**Agent Permission**:
An auditable permission grant that limits what an agent may access or do for a
specific protocol task.
_Avoid_: Agent preference, task policy, capability claim

**Capability Claim**:
A versioned Capability Definition an agent declares about itself during
handshake or profile update.
_Avoid_: Proven Capability, free-text skill

**Capability Key**:
A namespaced identifier for a capability that can be requested, claimed, and
proven.
_Avoid_: Free-text skill, unscoped tag

**Capability Definition**:
The versioned meaning, label, description, aliases, and authority record for a
Capability Key. Global definitions come from protocol source/version;
company-scoped definitions come from Company governance.
_Avoid_: Capability Claim, Proven Capability

**Capability Intent**:
A requester-facing capability input that may name an exact Capability
Definition version or ask for the latest active definition at proposal time.
_Avoid_: Task Policy requirement, free-text skill

**Capability Requirement**:
A policy-resolved requirement for an exact Capability Definition version and
definition hash.
_Avoid_: Capability Intent, mutable latest-active reference

**Capability Support Manifest**:
A node-advertised compatibility summary containing protocol version,
supported global Capability Definition refs, and a manifest hash. It does not
advertise Company Capability Keys or company-scoped definitions.
_Avoid_: Protocol version alone, capability claim

**Global Capability Key**:
A protocol-wide capability key for reusable skills across companies. Nodes
advertise which global Capability Definition versions their protocol
source/version supports.
_Avoid_: Company-specific capability

**Company Capability Key**:
A company-scoped key for an executor capability that only has meaning inside a
Company's local context, brand, compliance rules, workflow, or private
operational knowledge. It does not mean the Company itself has that capability.
_Avoid_: Global Capability Key, Company ability

**Proven Capability**:
A versioned Capability Definition inferred from accepted evidence, validated
work history, Review Outcomes, reputation signals, and completed tasks.
_Avoid_: Capability Claim

**Reputation Signal**:
An evidence-backed event generated from task execution, validation, review, or
settlement that affects an agent's reputation.
_Avoid_: Company reputation, project reputation

**Reputation Projection**:
A privacy-scoped view of agent reputation derived from Reputation Signals.
Public projections expose aggregate trust only; authorized projections may show
company, task, evidence, or capability detail.
_Avoid_: Raw reputation log, public task history

**Agent Reputation**:
The trust projection of an agent derived from reputation signals.
_Avoid_: Stakeholder reputation, company reputation

**Global Agent Reputation**:
The ecosystem-wide trust projection of an agent across all accepted reputation
signals.
_Avoid_: Company score

**Capability Reputation**:
The trust projection of an agent within a specific domain or capability.
_Avoid_: Global Agent Reputation

**Reputation Gate**:
A policy condition that requires minimum global reputation, capability
reputation, or proven capability before an agent may receive or execute work.
_Avoid_: Universal agent block

**Policy Decision**:
The concrete allow, deny, needs-review, or override-required result produced by
evaluating all applicable company, task, agent, and permission constraints.
_Avoid_: Single-policy approval, task state

**Policy Version**:
An immutable revision of a policy used to explain which rules were evaluated
for a specific decision.
_Avoid_: Mutable policy state

**Governance Override**:
An audited exception granted by an authorized governance authority when a
**Company Policy** or **Task Policy** explicitly allows override.
_Avoid_: Agent override, permission bypass

**Stakeholder**:
A human or economic participant attached to a company with policy-defined
authority, interest, review duty, or execution responsibility.
_Avoid_: User, account, actor

**Reviewer**:
A required stakeholder role for all company models that evaluates evidence or
subjective outcomes when policy requires review.
_Avoid_: Auditor, validator

**Chief**:
An executive stakeholder role in private or hybrid companies with permissions
defined by **Company Policy**.
_Avoid_: C-level when the docs need the canonical role name

**Member**:
A private-company stakeholder with a custom role and permissions defined by
**Company Policy**.
_Avoid_: Generic user

**Settlement**:
The ecosystem-wide token-based economic outcome after accepted evidence,
required review, and reputation impact.
_Avoid_: Payment, payout

**Solana Payment Adapter**:
A modular settlement adapter that turns an accepted Settlement into Solana
payment instructions or payment status using a configured payment interface.
_Avoid_: Settlement, DAO governance

**DAO Payment Interface**:
The default governance-controlled payment interface that AAO listens to and
consumes for DAO-governed treasury payment flows.
_Avoid_: DAO policy, Solana payment adapter

**Payment Execution Record**:
The auditable result of attempting a real payment through a payment adapter
after Settlement is authorized.
_Avoid_: Settlement Record, Budget Reservation

**Baseline Node Validation**:
The mandatory non-compensated checks every node performs before accepting,
projecting, or relaying protocol messages and lifecycle events.
_Avoid_: Paid validation service, reviewer work

**Compensable Contribution**:
A policy-authorized, attributable, and evidenced contribution eligible for a
Settlement Line.
_Avoid_: Participation, involvement

**Budget Allocation**:
A reserved portion of a task budget for a specific contribution kind, recipient
class, or operational cost.
_Avoid_: Settlement, payment

**Settlement Line**:
An itemized economic outcome for one contribution or cost inside a Settlement.
_Avoid_: Settlement Record, Payment Execution Record

**Dispute Bond**:
A refundable economic guarantee posted before a Dispute Case can block
settlement, payment, reputation, budget release, assignment, or review.
_Avoid_: Slashing, fee, punishment

**Dispute Cost Allocation**:
A budget allocation or forfeited Dispute Bond used to pay authorized dispute
resolution costs.
_Avoid_: Resolver reward, settlement reward

**Governance Dispute Waiver**:
A DAO-governed authorization to open a blocking Dispute Case without a Dispute
Bond when policy treats the dispute as a governance-protected challenge.
_Avoid_: Owner waiver, free dispute

**Frivolous Dispute**:
A Dispute Case found to be abusive, unsupported, spammy, or opened in bad
faith.
_Avoid_: Lost dispute, rejected review

**Slashing**:
An agent-only settlement penalty applied when policy allows it after accepted
evidence and required review.
_Avoid_: Stakeholder penalty, governance sanction

**Project**:
An operational grouping of work inside a company.
_Avoid_: Goal, task group

**Goal**:
A measurable desired outcome inside a project.
_Avoid_: Task, milestone when it implies execution

**Task**:
The executable unit of work that can be assigned, evidenced, reviewed, and
settled.
_Avoid_: Project, goal, job

**Preflight Check**:
A deterministic eligibility check that evaluates task clarity, policy,
capability, budget, and executor fit before paid execution.
_Avoid_: Agent reasoning, execution attempt

**Execution Quote**:
An executor offer that binds price, scope, assumptions, limits, validity, and
acceptable outcomes for a task or planning task.
_Avoid_: Fixed protocol price, informal estimate

**Agent Pricing Policy**:
An agent-owned pricing configuration used to generate quotes without runtime
reasoning by default. It may be updated manually by the agent owner or by an
authorized local runtime under owner-defined update rules.
_Avoid_: Protocol price, company price

**Pricing Update Rule**:
An owner-defined rule that controls whether local runtime pricing updates are
manual-only or bounded-auto, and what limits must be satisfied before a new
Agent Pricing Policy version becomes active.
_Avoid_: Runtime behavior, company pricing rule

**Local Pricing Configuration Interface**:
The protocol boundary through which an agent owner or authorized local runtime
requests Agent Pricing Policy changes. It does not define how the runtime
decides to call it.
_Avoid_: Agent runtime, task execution interface

**Pricing Configuration Grant**:
A local, owner-issued authorization that lets an agent runtime request or apply
Agent Pricing Policy changes within a specific Pricing Update Rule. It is not
propagated publicly through P2P.
_Avoid_: Agent identity, Agent Permission, Company permission

**Budget Reservation**:
A committed budget held for an accepted execution quote before assignment and
settlement.
_Avoid_: Settlement, reward, payment

**Expiration Rule**:
A policy-defined rule that says what happens when a quote, reservation,
assignment, permission, grant, planning task, or review window expires.
_Avoid_: Timeout flag, cleanup job

**Planning Task**:
A bounded paid task whose outcome is clarification, decomposition, estimates, or
an accepted inability to plan within limits.
_Avoid_: Free planning, execution task

**Token Spend Gate**:
The economic boundary that prevents agent model or tool spend before quote
acceptance, budget reservation, assignment, and required permissions.
_Avoid_: Best-effort cost control

**Executor**:
The single accountable agent or authorized human stakeholder assigned to execute
a task.
_Avoid_: Reviewer, contributor

**Assignment Lease**:
A time-bounded claim on a task assignment that must be renewed through heartbeat
or progress evidence to keep the executor from holding availability without
delivery.
_Avoid_: Assignment, task ownership

**Workspace**:
The working context and material used by projects, goals, and tasks.
_Avoid_: Company, repository

**Executor Interface**:
The interaction boundary through which an executor receives work and submits
task outcome material.
_Avoid_: Agent Adapter when referring to humans too

**Agent Adapter**:
The executor interface for AI agents and external runtimes.
_Avoid_: Human Interface

**Human Interface**:
The executor, review, and governance interface for human stakeholders.
_Avoid_: Agent Adapter

**Evidence Bundle**:
The required proof package for a task outcome, regardless of executor type.
_Avoid_: Agent proof, optional attachment

**Artifact Access Grant**:
A scoped, revocable authorization to read, fetch, decrypt, or validate a
private artifact reference for a specific purpose.
_Avoid_: Agent Permission, public artifact link

**Validation**:
The mandatory check that determines whether a task evidence bundle satisfies
policy, identity, artifact, privacy, and completeness requirements.
_Avoid_: Review

**Validation Authority**:
The authority under which a validation result is produced. Mechanical
validators run deterministic checks; governance validators use company, owner,
reviewer, or DAO authority.
_Avoid_: Reviewer, runtime

**Review**:
The conditional human or DAO judgment required by policy or by a validation
result that needs judgment.
_Avoid_: Validation

**Review Request**:
The policy-triggered request for human or DAO judgment on a task evidence
bundle.
_Avoid_: Review Decision, Review Outcome

**Review Decision**:
An individual reviewer or DAO-referenced judgment submitted for a review
request.
_Avoid_: Review Outcome

**Review Outcome**:
The aggregate result of a review request after applying quorum, conflict, and
decision rules.
_Avoid_: Review Decision

**Dispute Case**:
A bounded challenge flow opened against evidence, validation, review,
assignment, reputation, budget, or settlement before the disputed outcome is
final.
_Avoid_: Review, failed task

**Dispute Resolver**:
An independent authority selected to decide a Dispute Case without involvement
or conflict in the challenged task or record.
_Avoid_: Reviewer, executor, payer

**Resolver Pool**:
A policy-approved set of eligible independent Dispute Resolvers for a company,
task category, capability, or dispute type.
_Avoid_: Reviewer list, public crowd

**Dispute Resolution Panel**:
The one or more Dispute Resolvers selected for a Dispute Case according to
policy.
_Avoid_: Review panel, DAO vote by default

**Resolver Decision**:
An individual Dispute Resolver's signed judgment on a Dispute Case.
_Avoid_: Review Decision

**Dispute Outcome**:
The aggregate result of a Dispute Case after applying resolver quorum, conflict,
and evidence rules.
_Avoid_: Review Outcome

## Relationships

- A **Company** has exactly one company model: **Public Company**,
  **Hybrid Company**, or **Private Company**.
- A **Public Company** requires a **DAO** and **Reviewer** role.
- A **Hybrid Company** requires a **DAO**, one or more **Partners**, one or more
  **Chiefs**, and **Reviewer** role.
- A **Private Company** requires an **Owner** and **Reviewer** role, and may
  include **Investors**, **Partners**, **Chiefs**, and **Members**.
- A **DAO** governs public and hybrid companies but is not itself the root
  container of protocol activity.
- A **Company** owns many **Company Policies**.
- A **Governance Authority** may create, amend, approve, or revoke **Company
  Policies** according to the **Company** model.
- A **Task** may have a **Task Policy** in addition to applicable **Company
  Policies**.
- A **Task Policy** defines permission requirements and limits but does not
  itself grant access.
- A **Task Policy** cannot weaken applicable **Company Policies**.
- An **Agent** owns its **Agent Policy**.
- An **Agent** receives task-scoped **Agent Permissions** only through a
  **Policy Decision** and **Assignment**.
- An **Agent** may declare **Capability Claims**.
- A **Capability Claim** references a **Capability Definition** version so
  policies and discovery can compare agents consistently.
- A **Capability Key** is either a **Global Capability Key** such as
  `global:frontend.react` or a **Company Capability Key** such as
  `company:{companyId}:brand-review`.
- **Global Capability Keys** and their **Capability Definitions** are defined by
  protocol source/version, not by DAO governance.
- Nodes may continue participating in the network with the global
  **Capability Definition** versions they support. Unsupported global
  definition versions only block tasks, claims, matching, or proof that depend
  on that specific unsupported version.
- A node announces a **Capability Support Manifest** so peers can discover the
  exact intersection of supported global **Capability Definition** versions,
  instead of relying only on protocol version.
- A **Capability Support Manifest** does not announce **Company Capability
  Keys** or company-scoped definitions; those are discovered only through
  company-authorized flows.
- Company-scoped capability resolution uses authorized Company context already
  available to the node. It does not depend on a synchronous public P2P query to
  the whole network.
- Updating global **Capability Definitions** is a protocol compatibility change:
  users update their AAO Protocol version or source checkout to support newer
  shared capability language, while older compatible flows may continue.
- **Company Capability Keys** and their **Capability Definitions** are governed
  by the owning **Company**.
- A **Company Capability Key** names an executor capability in the Company's
  local vocabulary. It is claimed or proven by an **Agent** or executor, not by
  the **Company** itself.
- A **Capability Key** has versioned **Capability Definitions** that describe
  what the key means at decision time.
- **Capability Claims**, **Task Policies**, and **Proven Capabilities**
  reference a **Capability Definition** version, not only a key string.
- A **Task Proposal** may contain **Capability Intents** for ergonomics, but a
  **Task Policy** stores **Capability Requirements** resolved to exact
  **Capability Definition** versions.
- A **Policy Decision** records the resolved **Capability Requirements** it
  evaluated so later **Capability Definition** changes do not rewrite old work.
- The protocol may derive **Proven Capabilities** from evidence, validation,
  reviews, reputation signals, and task history.
- A **Proven Capability** is derived only inside the same **Capability Key**
  namespace as the accepted evidence that proved it.
- **Reputation Signal** always applies to an **Agent**.
- **Reputation Projection** is the privacy boundary around reputation: public
  projections expose aggregate trust, while authorized projections may expose
  company, task, evidence, or capability detail.
- **Agent Reputation** may be projected as **Global Agent Reputation** or
  **Capability Reputation**.
- **Capability Reputation** scopes trust to a domain or capability instead of
  treating all agent work as equivalent.
- An **Agent** with little or no history may execute work only when applicable
  policies allow **Capability Claims** and have no or low **Reputation Gate**.
- Higher-risk tasks should use a **Reputation Gate** that requires minimum
  **Capability Reputation** or **Proven Capability**.
- **Company Policy** and **Task Policy** may evaluate both **Capability Claims**
  and **Proven Capabilities**.
- Higher-risk tasks should require **Proven Capabilities** rather than relying
  only on **Capability Claims**.
- **Agent Permissions** protect the agent and the company from unauthorized
  execution, excess access, and prompt-injection attempts that conflict with
  approved work boundaries.
- A **Policy Decision** uses the most restrictive intersection of applicable
  **Company Policy**, **Task Policy**, **Agent Policy**, and **Agent
  Permissions**.
- A **Policy Decision** records the applicable **Policy Versions** used during
  evaluation.
- A **Policy Decision** is not a task state; it maps to task states such as
  approved, rejected, blocked, or needs-review.
- Every state-changing lifecycle event requires an **Event Authority** for its
  event type and scope.
- A P2P signature proves who sent or relayed a message, but **Event Admission
  Rule** decides whether the embedded event is authorized to affect local state.
- A node may relay an event without being the **Event Authority** that
  originated it.
- Events that fail **Event Admission Rule** are rejected or quarantined and do
  not update task state, permissions, evidence, reputation, settlement, or
  payment.
- A **Task** in progress keeps the **Policy Decision** that governed its
  approval or assignment.
- Later policy changes affect new **Policy Decisions**, not tasks already in
  progress.
- A task already in progress can be paused, migrated, or re-evaluated only by an
  explicit governance action allowed by policy.
- Any denial from an applicable policy or permission boundary blocks execution
  unless the result explicitly allows review or governance override.
- A **Governance Override** can apply only to **Company Policy** or **Task
  Policy** constraints that explicitly allow override.
- A **Governance Override** cannot force an **Agent** to accept work rejected by
  its **Agent Policy**.
- A **Governance Override** cannot expand or bypass **Agent Permissions**.
- If **Agent Policy** denies work or **Agent Permissions** block execution, the
  **Policy Decision** is denied rather than override-required.
- **Settlement** applies to the whole AAO ecosystem through tokens.
- **Settlement** records the authorized economic outcome; **Payment Execution
  Record** records the result of attempting the real payment.
- A **Settlement** may contain multiple **Settlement Lines** for executor
  reward, reviewer fee, governance validator fee, dispute resolver fee, storage
  cost, payment execution cost, dispute cost, refund, or forfeited amount.
- A **Budget Reservation** may contain **Budget Allocations** that reserve funds
  for policy-required **Compensable Contributions** before assignment or review.
- A **Compensable Contribution** must be authorized by policy, attributable to a
  participant or provider, and backed by evidence, decision, outcome, or service
  record.
- **Baseline Node Validation** is not a **Compensable Contribution**; nodes
  perform message validation, event admission, deduplication, compatibility
  checks, and local projection checks as a duty of protocol participation.
- Specialized validation, governance review, dispute resolution, storage, or
  payment execution can become compensable only when policy and budget
  allocation explicitly authorize it.
- A **Solana Payment Adapter** executes or observes payment through a configured
  payment interface after Settlement is authorized.
- AAO's default Solana payment path consumes the **DAO Payment Interface** for
  DAO-governed treasury payment flows.
- A **Company** owns many **Projects**.
- A **Project** contains many **Goals**.
- A **Goal** is achieved through many **Tasks**.
- A **Task** has exactly one **Executor**.
- An **Executor** can be an **Agent** or an authorized human **Stakeholder**.
- A human **Executor** must be a **Stakeholder** of the same **Company** as the
  **Task**.
- An **Assignment** is protected by an **Assignment Lease** so an executor cannot
  hold work indefinitely without heartbeat or progress evidence.
- If an **Assignment Lease** expires, policy can release, reassign, dispute, or
  fail the task and may update reputation for agent executors.
- Work requiring multiple executors is split into multiple **Tasks** under the
  same **Goal**.
- A **Task Proposal** receives a **Preflight Check** before any **Agent** spends
  execution tokens or tool budget.
- An **Execution Quote** must be accepted before a **Budget Reservation** is
  created.
- An **Execution Quote** is generated from **Agent Pricing Policy** by default,
  without invoking agent runtime reasoning.
- The owner of an **Agent** may update **Agent Pricing Policy** manually, or may
  authorize the local agent runtime to request or apply pricing updates through
  local protocol communication.
- **Agent Pricing Policy** changes enter through a **Local Pricing Configuration
  Interface**, regardless of whether the caller is a UI, script, scheduled job,
  heartbeat, chat flow, local API, or runtime-specific process.
- A local runtime can change **Agent Pricing Policy** only within the
  **Pricing Update Rule** set by the agent owner; accepted changes create new
  policy versions.
- A **Pricing Update Rule** can be `manual-only`, where local runtime requests
  wait for owner approval, or `bounded-auto`, where matching requests are
  applied automatically inside owner-defined limits.
- A local runtime needs a **Pricing Configuration Grant** before it can request
  or apply **Agent Pricing Policy** changes.
- A **Pricing Configuration Grant** is issued by the agent owner, is revocable,
  and is separate from the **Agent** identity and task-scoped **Agent
  Permissions**.
- A **Pricing Configuration Grant** is local-only. The network may see opaque
  refs, hashes, versions, or status needed for authorized audit, but not the
  grant contents or private automation authority itself.
- Runtime pricing requests outside **Pricing Update Rule** limits become
  pending owner approval and do not affect active quote generation.
- A **Budget Reservation** must exist before agent assignment and token-spending
  execution.
- **Expiration Rules** define what happens when quotes, budget reservations,
  assignments, permission grants, pricing grants, planning tasks, or review
  windows expire.
- A vague or oversized **Task Proposal** becomes a **Planning Task** or returns
  clarification needs instead of being pushed directly to execution.
- A **Planning Task** can produce child **Task Proposals**, clarification needs,
  or an accepted unplannable result within its quoted limits.
- The **Token Spend Gate** protects both **Company** and **Agent** by ensuring
  market-priced work starts only after scope and budget are explicit.
- A **Reviewer** evaluates evidence or subjective outcome and is not the
  **Executor** of the reviewed task.
- An **Executor** uses an **Executor Interface** to interact with the protocol.
- An **Agent** executor uses an **Agent Adapter**.
- A human **Stakeholder** executor uses a **Human Interface**.
- Every **Task** requires an **Evidence Bundle**, whether the **Executor** is an
  **Agent** or a human **Stakeholder**.
- Private or encrypted **Artifact References** require an **Artifact Access
  Grant** before an agent, stakeholder, reviewer, or validator can access their
  contents.
- An **Artifact Access Grant** is purpose-scoped, revocable, time-bounded, and
  auditable.
- Every **Evidence Bundle** requires **Validation**.
- Every **Validation Result** is produced under a **Validation Authority**.
- Mechanical validators check deterministic evidence rules; governance
  validators require company, owner, reviewer, or DAO authority.
- **Review** is required only when **Policy** requires it or **Validation**
  returns `needs-review`.
- **Validation** occurs before **Review Request** so mechanical evidence,
  identity, artifact, privacy, and policy checks are completed first.
- A **Review Request** belongs to one **Task** and one **Evidence Bundle**.
- A **Review Request** may receive multiple **Review Decisions**.
- A **Review Outcome** aggregates **Review Decisions** by the policy-defined
  quorum, DAO reference, conflict, and dispute rules.
- A **Dispute Case** pauses final settlement or reputation changes for disputed
  outcomes until the dispute authority resolves it.
- A blocking **Dispute Case** requires a **Dispute Bond** or pre-reserved
  **Dispute Cost Allocation** unless a DAO grants a **Governance Dispute
  Waiver**.
- A private-company owner cannot open a bondless blocking dispute by default;
  owner urgency can pause risk by policy, but dispute merits still require the
  normal bond, cost allocation, or DAO waiver path.
- A **Dispute Case** may challenge a **Review Decision** or **Review Outcome**
  when a reviewer is suspected of lying, colluding, being conflicted, or
  misapplying policy.
- **Company Policy** or **Task Policy** defines the applicable **Resolver
  Pool**, resolver selection rule, quorum, conflict policy, and evidence access
  rule for disputes.
- A **Dispute Resolver** must not be the task requester, executor, challenged
  reviewer, original validator, payment controller, or any other conflicted
  participant for that **Dispute Case**.
- A **Dispute Resolution Panel** receives only the grant-gated evidence access
  required to resolve the dispute.
- A **Resolver Decision** does not by itself finalize a dispute; the **Dispute
  Outcome** applies quorum and conflict rules.
- A **Frivolous Dispute** may forfeit its **Dispute Bond** into **Dispute Cost
  Allocation** or **Settlement Lines** for resolver costs, affected-party
  costs, or treasury recovery. This is not **Slashing**.
- Only a **Task** can directly produce an **Evidence Bundle**, **Review
  Request**, **Review Outcome**, Reputation Signal, or Settlement.
- **Settlement** belongs to the **Task** outcome; DAO governance may authorize or
  reference it for public or hybrid companies.
- **Slashing** applies only to an **Agent** executor in the core protocol.
- Human **Stakeholder** execution failures produce audit and governance actions,
  not slashing or **Agent Reputation** changes.
- A **Workspace** provides context and artifacts for **Projects**, **Goals**,
  and **Tasks**.

## Example dialogue

> **Dev:** "Should a DAO own the task policy directly?"
> **Domain expert:** "No. The **Company** owns **Company Policy**. In a public
> or hybrid company, the **DAO** is the governance authority that can approve or
> change it."
>
> **Dev:** "Can a chief own a separate policy?"
> **Domain expert:** "No. The **Company** owns **Company Policies**. A
> **Chief** may be a **Governance Authority** allowed to change them."
>
> **Dev:** "Can we settle a goal?"
> **Domain expert:** "No. A **Goal** is an outcome. A **Task** is the executable
> unit that produces evidence and can be settled."
>
> **Dev:** "Can two agents execute the same task?"
> **Domain expert:** "No. Split the work into two **Tasks**. Each **Task** has
> exactly one **Executor** so evidence, reputation, and settlement stay clean."
>
> **Dev:** "Should a human executor go through the Agent Adapter?"
> **Domain expert:** "No. **Agent Adapter** is for AI agents. Humans use the
> **Human Interface**. Both are kinds of **Executor Interface**."
>
> **Dev:** "Do we store both executor kind and executor interface?"
> **Domain expert:** "No. The interface is derived: **Agent** uses **Agent
> Adapter**, and human **Stakeholder** uses **Human Interface**."
>
> **Dev:** "Can a human-executed task skip evidence?"
> **Domain expert:** "No. Every **Task** needs an **Evidence Bundle**. Human
> evidence may be a signed decision, note, upload, or approval record."
>
> **Dev:** "Can an agent or reviewer open private evidence just because it is
> assigned to a task?"
> **Domain expert:** "No. Private or encrypted **Artifact References** require
> an **Artifact Access Grant** with purpose, TTL, audit, and revocation."
>
> **Dev:** "Does every task need a human reviewer?"
> **Domain expert:** "No. Every evidence bundle needs **Validation**. **Review**
> happens only when **Policy** requires it or validation needs judgment."
>
> **Dev:** "Who is trusted to validate evidence?"
> **Domain expert:** "A **Validation Result** must identify its **Validation
> Authority**. Mechanical validation is deterministic; governance validation
> requires policy-authorized company, owner, reviewer, or DAO authority."
>
> **Dev:** "If three reviewers disagree, which review decision controls?"
> **Domain expert:** "No single **Review Decision** controls. The
> **Review Outcome** applies the review request's quorum, conflict, and dispute
> rules."
>
> **Dev:** "What if the reviewer lies?"
> **Domain expert:** "Open a **Dispute Case** against the **Review Decision** or
> **Review Outcome**. A conflict-free **Dispute Resolution Panel** from the
> policy's **Resolver Pool** receives scoped evidence access and produces a
> **Dispute Outcome**."
>
> **Dev:** "Can the same Policy decide company preferences, task constraints,
> and agent self-protection?"
> **Domain expert:** "No. Use **Company Policy** for company-wide constraints,
> **Task Policy** for task-specific criteria, **Agent Policy** for what the agent
> accepts, and **Agent Permissions** for enforceable execution boundaries."
>
> **Dev:** "Should the **Task Policy** contain the agent's permissions?"
> **Domain expert:** "No. **Task Policy** declares permission requirements and
> limits; **Agent Permissions** are concrete grants issued for one task after a
> **Policy Decision**."
>
> **Dev:** "If **Company Policy** allows an agent but **Task Policy** rejects it,
> can the task still be assigned?"
> **Domain expert:** "No. The **Policy Decision** uses the most restrictive
> intersection of all applicable policies and permissions."
>
> **Dev:** "Can governance override an agent's refusal or permission boundary?"
> **Domain expert:** "No. **Governance Override** can only apply to overrideable
> **Company Policy** or **Task Policy** constraints. **Agent Policy** and
> **Agent Permissions** remain hard boundaries."
>
> **Dev:** "If a Company Policy changes after a task starts, does the task change
> automatically?"
> **Domain expert:** "No. The **Task** keeps the **Policy Decision** and
> **Policy Versions** used when it was approved or assigned. New policy versions
> affect new decisions unless governance explicitly pauses, migrates, or
> re-evaluates the task."
>
> **Dev:** "If an agent says it can audit Solana programs, is that enough?"
> **Domain expert:** "No. That is a **Capability Claim**. For higher-risk work,
> policy should require **Proven Capability** derived from accepted evidence,
> reviews, reputation signals, and completed task history."
>
> **Dev:** "Can a policy just require the string `global:solana.program-audit`?"
> **Domain expert:** "It should require a **Capability Definition** version for
> that key, so audits can reconstruct exactly what the capability meant."
>
> **Dev:** "Can a requester just choose the latest active definition?"
> **Domain expert:** "Yes as a **Capability Intent**, but before the **Task
> Policy** or **Policy Decision** is recorded, AAO resolves it to an exact
> **Capability Requirement** with definition id, version, namespace, key, and
> hash."
>
> **Dev:** "Who controls `global:*` capability definitions?"
> **Domain expert:** "The protocol source/version does. This is not DAO
> governance. A node accepts a global definition only if its local protocol
> version implements it; otherwise it rejects that specific capability
> requirement or marks it upgrade-required, while continuing to participate in
> compatible flows."
>
> **Dev:** "Is protocol version enough to know which global capabilities a node
> supports?"
> **Domain expert:** "No. The node announces protocol version plus a
> **Capability Support Manifest** containing exact supported global definition
> refs and a manifest hash, so peers can compute compatibility precisely."
>
> **Dev:** "Should the manifest announce `company:{companyId}:*` too?"
> **Domain expert:** "No. Company-scoped capability language may reveal private
> operational context. It is discovered only through flows authorized by that
> Company."
>
> **Dev:** "Does **Company Capability Key** mean the Company has a capability?"
> **Domain expert:** "No. The executor has or proves the capability. The
> Company owns the vocabulary and rules that define what that capability means
> inside its context."
>
> **Dev:** "Does resolving `company:{companyId}:*` require waiting for the whole
> P2P network?"
> **Domain expert:** "No. Resolution uses local authorized Company context. If
> the node does not have that authorized context yet, the result is pending or
> opaque, not a public network-wide lookup."
>
> **Dev:** "If a P2P peer sends a signed `task.assigned` event, do we accept it
> as true?"
> **Domain expert:** "No. The message signature authenticates the peer. The
> event still needs **Event Authority** and must pass the local **Event
> Admission Rule** before it can change task state."
>
> **Dev:** "If a task requires `global:solana.program-audit@v2`, can an agent
> that only supports `@v1` run it?"
> **Domain expert:** "Not by automatic fallback. The task blocks unless
> **Task Policy** or **Company Policy** explicitly lists `@v1` as an accepted
> alternative for that risk level."
>
> **Dev:** "Can an agent spend tokens trying to understand a task before it is
> assigned?"
> **Domain expert:** "No. **Preflight Check** and quote matching are cheap and
> deterministic. Token-spending work starts only after **Execution Quote**
> acceptance, **Budget Reservation**, assignment, and required permissions."
>
> **Dev:** "Can generating a quote spend agent tokens?"
> **Domain expert:** "Not by default. A normal **Execution Quote** comes from
> **Agent Pricing Policy**. If pricing needs runtime reasoning, that becomes a
> bounded **Planning Task** or an explicit local pricing update flow."
>
> **Dev:** "Who can change an agent's pricing?"
> **Domain expert:** "The agent owner can update **Agent Pricing Policy**
> manually, or authorize the local agent runtime to request or apply updates
> under owner-defined rules. Accepted updates are recorded by the protocol."
>
> **Dev:** "Does AAO define the cron job, heartbeat, chat flow, or endpoint that
> adjusts pricing?"
> **Domain expert:** "No. AAO defines the **Local Pricing Configuration
> Interface** and validates the **Pricing Update Rule**. The runtime mechanism
> stays outside the domain model."
>
> **Dev:** "Can the runtime update pricing just because it is the agent?"
> **Domain expert:** "No. Runtime pricing updates require a separate **Pricing
> Configuration Grant** from the agent owner. The **Agent** identity executes
> tasks; the owner governs economic configuration."
>
> **Dev:** "Should Pricing Configuration Grants be propagated through P2P?"
> **Domain expert:** "No. They are local-only authorization records. P2P may
> carry privacy-safe refs or hashes for audit, but not the grant contents."
>
> **Dev:** "Who pays when the task is too vague and needs decomposition?"
> **Domain expert:** "That becomes a bounded **Planning Task** with its own
> **Execution Quote**. If it cannot be planned within the limits, that is an
> acceptable outcome rather than an infinite unpaid loop."
>
> **Dev:** "If an agent proves `company:acme:brand-review`, can another company
> trust that as its own brand review capability?"
> **Domain expert:** "No. Company-scoped **Capability Keys** do not transfer
> across companies. The agent would need proof in that other company's namespace
> or a relevant **Global Capability Key**."
>
> **Dev:** "Can a company or project have reputation?"
> **Domain expert:** "No. Reputation is always about an **Agent**. It can be
> projected as **Global Agent Reputation** or **Capability Reputation**."
>
> **Dev:** "Can public reputation reveal private client work?"
> **Domain expert:** "No. Public **Reputation Projections** expose aggregate
> trust only. Company, task, evidence, or capability details require authorized
> projections."
>
> **Dev:** "If an agent accepts a task and disappears, does it keep the task?"
> **Domain expert:** "No. The **Assignment Lease** expires without heartbeat or
> progress evidence, allowing release, reassignment, dispute, or failure by
> policy."
>
> **Dev:** "If a human stakeholder executes badly, do we slash them?"
> **Domain expert:** "No. Human stakeholder failure is handled through audit and
> governance action. **Slashing** is agent-only in the core protocol."
>
> **Dev:** "Does Settlement mean the Solana payment already happened?"
> **Domain expert:** "No. **Settlement** records the authorized economic
> outcome. **Payment Execution Record** records the actual payment attempt
> through a **Solana Payment Adapter**, which by default consumes the **DAO
> Payment Interface** for DAO treasury flows."
>
> **Dev:** "If many nodes validate a task or message, does every node get paid?"
> **Domain expert:** "No. **Baseline Node Validation** is a network duty.
> Payment requires a policy-authorized **Compensable Contribution** represented
> by **Budget Allocation** and finalized as a **Settlement Line**."
>
> **Dev:** "Can someone open a free dispute just to block payment?"
> **Domain expert:** "No. A blocking **Dispute Case** needs a **Dispute Bond**
> or pre-reserved **Dispute Cost Allocation**, unless a DAO grants a
> **Governance Dispute Waiver**."
>
> **Dev:** "Can a private-company owner waive the bond because they own the
> company?"
> **Domain expert:** "No, not by default. A private owner may have emergency
> pause authority if policy grants it, but deciding the dispute still needs the
> normal bond, cost allocation, or DAO waiver path."
>
> **Dev:** "Can a new agent with no history execute anything?"
> **Domain expert:** "Yes, but only when policy allows **Capability Claims** and
> has no or low **Reputation Gate**. Higher-risk work should require
> **Capability Reputation** or **Proven Capability**."

## Flagged ambiguities

- "Organization" was used as the root domain term in earlier specs. Resolved:
  the canonical root term is **Company**.
- "Policy" was too broad when defined only as company-owned. Resolved:
  **Policy** is the umbrella term; **Company Policy**, **Task Policy**, and
  **Agent Policy** are separate scopes, and **Agent Permission** is the
  enforceable execution boundary.
- A generic `Policy` entity could hide ownership and scope. Resolved:
  implementation artifacts model **Company Policy**, **Task Policy**, and
  **Agent Policy** explicitly.
- "Agent Policy Profile" mixed agent preferences, permission grants, and
  capability declarations. Resolved: use **Agent Policy** for agent-owned
  acceptance rules, **Agent Permission** for task-scoped grants, and
  **Capability Claim** for declared **Capability Definition** versions.
- Policy conflicts could be treated as precedence rules. Resolved: a **Policy
  Decision** uses the most restrictive intersection; any denial blocks execution
  unless review or governance override is explicitly allowed.
- Policy decision results could be confused with task lifecycle states.
  Resolved: **Policy Decision** uses allow, deny, needs-review, or
  override-required; task projections use states such as approved, blocked,
  rejected, and needs-review.
- Governance override could be interpreted as a universal bypass. Resolved:
  **Governance Override** cannot force an agent to accept work and cannot bypass
  **Agent Permissions**.
- Policy updates could silently alter in-progress work. Resolved: a **Policy
  Decision** records the evaluated **Policy Versions**; later changes affect new
  decisions unless governance explicitly pauses, migrates, or re-evaluates the
  task.
- Agent skill could be treated as self-declared truth. Resolved:
  **Capability Claim** is an agent-declared **Capability Definition** version;
  **Proven Capability** is inferred from evidence, validation, reviews,
  reputation signals, and task history.
- Capability keys could be free-text or globally ambiguous. Resolved:
  **Capability Key** uses explicit namespaces: `global:*` for protocol-wide
  skills and `company:{companyId}:*` for company-local capabilities.
- Global capability definitions could be confused with DAO-governed settings.
  Resolved: `global:*` definitions are protocol-source compatibility artifacts;
  nodes advertise supported definition versions and continue operating on
  compatible work.
- Protocol version could be treated as enough for global capability
  compatibility. Resolved: nodes also advertise a **Capability Support
  Manifest** with exact supported global Capability Definition refs and a
  manifest hash.
- Company-scoped capabilities could leak through public peer compatibility
  handshakes. Resolved: **Capability Support Manifest** advertises only
  `global:*`; `company:{companyId}:*` is discovered through authorized Company
  flows.
- Company Capability Key could sound like a capability owned by the Company.
  Resolved: it is a Company-local vocabulary key for executor capabilities.
- Company-scoped capability resolution could be mistaken for a public
  network-wide lookup. Resolved: nodes resolve it from local authorized Company
  context and return pending or opaque results when that context is unavailable.
- Signed P2P messages could be confused with authorized lifecycle events.
  Resolved: P2P signatures authenticate transport; **Event Authority** and
  **Event Admission Rule** decide whether a lifecycle event can affect local
  state.
- Capability fallback could be automatic across versions. Resolved: unsupported
  required versions block matching unless **Company Policy** or **Task Policy**
  explicitly declares exact accepted alternatives.
- Agent execution tokens could be spent before work is valid or funded.
  Resolved: **Token Spend Gate** requires accepted **Execution Quote**,
  **Budget Reservation**, assignment, and permissions before token-spending
  execution.
- Planning could become unpaid or infinite. Resolved: decomposition is a
  bounded paid **Planning Task** with quote limits and accepted stop outcomes.
- Pricing could be fixed by protocol or be too unpredictable. Resolved: the
  protocol defines quote and reservation contracts; market participants set
  prices through **Agent Pricing Policy** and **Execution Quotes** under Company
  budget constraints.
- Quote generation could itself burn tokens. Resolved: normal quotes are
  generated from **Agent Pricing Policy** without runtime reasoning; token-heavy
  pricing analysis becomes bounded paid planning or an explicit local pricing
  update flow.
- Agent runtime pricing updates could be mistaken for company-controlled
  pricing. Resolved: **Agent Pricing Policy** remains agent-owned; the owner can
  allow local runtime updates only under owner-defined rules.
- Runtime pricing automation could leak into the protocol model. Resolved: AAO
  exposes a **Local Pricing Configuration Interface** and validates
  **Pricing Update Rule** limits, but does not define cron jobs, heartbeats,
  chat flows, or runtime-specific behavior.
- Agent identity could be mistaken for pricing authority. Resolved: local
  runtime pricing updates require a separate **Pricing Configuration Grant**
  from the agent owner; task-scoped **Agent Permissions** do not authorize
  pricing configuration changes.
- Pricing Configuration Grants could leak local automation authority through the
  network. Resolved: grants are local-only; P2P carries only privacy-safe refs,
  hashes, versions, or status when authorized audit requires them.
- Capability meaning could drift over time. Resolved: **Capability Definition**
  versions define the auditable meaning required, claimed, or proven at the time
  of a policy decision.
- A task could accidentally depend on a mutable "latest active" capability.
  Resolved: **Task Proposals** may accept **Capability Intents**, but **Task
  Policies** and **Policy Decisions** store exact **Capability Requirements**.
- Reputation could be modeled for companies, projects, stakeholders, or agents.
  Resolved: reputation is always about an **Agent**, with global and
  domain/capability projections.
- Reputation details could leak private company work. Resolved: public
  **Reputation Projections** expose aggregate trust only; company, task,
  evidence, or capability details require authorized projections.
- New agents could be blocked from all work or trusted too quickly. Resolved:
  they may execute work only when policy accepts claims and has no or low
  **Reputation Gate**; higher-risk work requires capability reputation or proven
  capability.
- "Settlement" was used like payment. Resolved: **Settlement** means the
  token-based economic outcome for the whole ecosystem, including reward,
  slashing, dispute, or no-settlement.
- Settlement commitment could be confused with real Solana payment execution.
  Resolved: **Settlement** authorizes the economic outcome; **Payment Execution
  Record** records actual payment through a **Solana Payment Adapter**, using
  the **DAO Payment Interface** by default for DAO-governed treasury flows.
- Every participant in a task flow could be assumed to receive payment.
  Resolved: only policy-authorized **Compensable Contributions** receive
  **Settlement Lines**; **Baseline Node Validation** is required network
  participation and is not paid by task settlement.
- Slashing could be applied to human stakeholders. Resolved: in the core
  protocol, **Slashing** is agent-only; human stakeholder failures produce audit
  and governance actions instead.
- "Project", "Goal", and "Task" were loosely grouped as work. Resolved:
  **Project** groups work, **Goal** defines measurable outcome, and **Task**
  executes.
- "Executor" and "Reviewer" could be confused as task participants. Resolved:
  a **Task** has one **Executor**; a **Reviewer** evaluates but does not execute
  that task.
- Human executors could be modeled as arbitrary users. Resolved: a human
  **Executor** must be a **Stakeholder** of the same **Company** as the
  **Task**.
- "Agent Adapter" was too narrow for human executors. Resolved:
  **Executor Interface** is the umbrella term; **Agent Adapter** and **Human
  Interface** are specific forms.
- Executor interface could be stored redundantly. Resolved: it is derived from
  executor type, with **Agent** using **Agent Adapter** and human
  **Stakeholder** using **Human Interface**.
- "Evidence Bundle" could sound agent-only. Resolved: every **Task** requires an
  **Evidence Bundle**, including tasks executed by humans.
- Private artifacts could be treated as normal evidence links. Resolved:
  private or encrypted **Artifact References** require a scoped, revocable,
  time-bounded **Artifact Access Grant**.
- "Validation" and "Review" were easy to collapse. Resolved: **Validation** is
  mandatory for every evidence bundle, occurs before review, and **Review** is
  conditional.
- Validation authority could be implicit. Resolved: every **Validation Result**
  records **Validation Authority**, separating deterministic mechanical checks
  from policy-authorized governance validation.
- Review could be modeled as one decision row. Resolved: **Review Request**
  starts the review flow, **Review Decision** records individual reviewer or DAO
  inputs, and **Review Outcome** records the aggregate result.
- Disputes could be folded into review or settlement status. Resolved:
  **Dispute Case** is a bounded challenge flow that pauses final disputed
  outcomes until resolved.
- A lying or conflicted reviewer could be treated as a trusted final authority.
  Resolved: **Review Decisions** and **Review Outcomes** can be challenged by a
  **Dispute Case** resolved by an independent **Dispute Resolution Panel**.
- Free blocking disputes could become economic denial-of-service. Resolved:
  blocking **Dispute Cases** require **Dispute Bond** or pre-reserved
  **Dispute Cost Allocation** unless a DAO grants a **Governance Dispute
  Waiver**.
- Private owners could abuse bondless disputes to delay executor payment.
  Resolved: a private-company owner does not get default bondless dispute
  authority; emergency pause and dispute merits are separate policy paths.
- Expiring quotes, reservations, grants, assignments, permissions, planning
  tasks, or reviews could leave ambiguous state. Resolved: **Expiration Rule**
  states the allowed next action when an expirable object times out.
- An executor could claim work and never deliver, blocking availability.
  Resolved: **Assignment Lease** requires heartbeat or progress evidence and can
  release, reassign, dispute, or fail the task on expiry.

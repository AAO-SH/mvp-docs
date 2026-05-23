# Data Model: AAO Protocol Core

## Bounded Contexts

- **Company Registry**: Company, Stakeholder, RoleAssignment.
- **Agent Registry**: Agent, AgentPolicy, AgentPricingPolicy,
  PricingConfigurationGrant, CapabilityNamespace, CapabilityDefinition,
  CapabilityClaim, ProvenCapability.
- **Policy & Governance**: CompanyPolicy, TaskPolicy, PolicyDecision,
  GovernanceReference, ExpirationRule, EventAuthority, EventAdmissionRule.
- **Task Execution Graph**: TaskProposal, Task, Assignment, AssignmentLease,
  TaskDependency.
- **Market & Budgeting**: PreflightCheck, ExecutionQuote, BudgetReservation,
  BudgetAllocation, PlanningTask.
- **Evidence & Validation**: EvidenceBundle, ArtifactReference,
  ArtifactAccessGrant, ValidationAuthority, ValidationResult.
- **Review**: ReviewRequest, ReviewDecision, ReviewOutcome.
- **Dispute**: DisputeCase, ResolverPool, DisputeResolver,
  DisputeResolutionPanel, ResolverDecision, DisputeOutcome, DisputeBond.
- **Reputation**: ReputationSignal, ReputationProjection, CapabilityScore.
- **Settlement**: SettlementRecord, SettlementLine, SettlementCommitment,
  SolanaPaymentAdapter, DAOPaymentInterface, PaymentExecutionRecord.
- **Workspace & Storage**: Workspace, WorkspaceSnapshot, StorageObjectRef.
- **Secure Node**: NodeIdentity, PermissionGrant, SecretRef, SignerRef.
- **P2P Propagation**: Peer, CapabilitySupportManifest, NetworkEvent,
  InboxMessage, OutboxMessage.

## Entities

### Company

Fields:
- `id`: stable company id.
- `type`: `public-company`, `private-company`, or `hybrid-company`.
- `name`: display name.
- `ownerStakeholderId`: required for private and hybrid companies.
- `daoGovernanceRef`: required for public and hybrid companies using DAO
  governance.
- `status`: `draft`, `active`, `suspended`, `archived`.
- `createdAt`, `updatedAt`.

Relationships:
- Has many Stakeholders, CompanyPolicies, Workspaces, Tasks,
  ReputationSignals, and SettlementRecords.

Validation:
- Public companies require a DAO governance reference.
- Private companies require an owner.
- Hybrid companies require both ownership and DAO/partner governance rules.

### Stakeholder

Fields:
- `id`
- `companyId`
- `role`: `dao-member`, `investor`, `partner`, `entrepreneur`, `owner`,
  `c-level`, `member`, `reviewer`.
- `displayName`
- `publicIdentityRef`
- `status`: `active`, `suspended`, `removed`.

Relationships:
- Can request tasks, approve tasks, review evidence, or authorize settlement
  when policy grants permission.

### Agent

Fields:
- `id`
- `publicKey`
- `runtimeKind`: `codex`, `claude`, `cursor`, `hermes`, `openclaw`, `other`.
- `adapterVersion`
- `status`: `pending-handshake`, `active`, `blocked`, `suspended`, `retired`.
- `createdAt`, `lastSeenAt`.

Relationships:
- Has one active AgentPolicy and one active AgentPricingPolicy.
- Has many CapabilityClaims, ProvenCapabilities, Assignments,
  PermissionGrants, PricingConfigurationGrants, and ReputationSignals.

Validation:
- Agent cannot receive work until public key and active AgentPolicy are
  recorded.
- Market-priced agent work also requires an active AgentPricingPolicy before
  routine quote generation.

### AgentPolicy

Fields:
- `id`
- `agentId`
- `acceptedMessageTypes`
- `rejectedMessageTypes`
- `autonomyLimits`
- `acceptedTaskCategories`
- `rejectedTaskCategories`
- `acceptedPermissionScopes`
- `rejectedPermissionScopes`
- `version`
- `effectiveFrom`
- `status`: `active`, `superseded`, `revoked`.

Validation:
- Assignment must use the active AgentPolicy version at decision time.
- AgentPolicy can reject permission scopes but cannot grant access.

### AgentPricingPolicy

Fields:
- `id`
- `agentId`
- `ownerRef`
- `pricingRules`
- `minimumPrice`
- `tokenRef`
- `riskMultipliers`
- `capabilityMultipliers`
- `planningPriceRules`
- `maxQuoteExposure`
- `pricingUpdateMode`: `manual-only` or `bounded-auto`.
- `pricingUpdateRule`: owner-defined rule for manual updates and optional local
  runtime updates.
- `localConfigurationInterfaceRef`: local protocol interface used to request
  pricing changes.
- `pricingConfigurationGrantRef`: present when a local runtime request created
  or activated this version.
- `pendingRuntimeProposalRefs`
- `updatedBy`: `owner`, `authorized-runtime`, or `governance-action`.
- `runtimeProposalRef`: present when local runtime proposed the update.
- `ownerAuthorizationRef`: present when a local runtime update relies on prior
  owner authorization.
- `version`
- `effectiveFrom`
- `status`: `active`, `pending-owner-approval`, `superseded`, `revoked`.

Validation:
- AgentPricingPolicy is agent-owned and cannot be changed by a Company.
- Agent owner may update AgentPricingPolicy manually.
- Protocol exposes a local pricing configuration interface for owner and
  authorized-runtime update requests; it does not define whether the external
  runtime uses a cron job, heartbeat, chat response, endpoint, API client, or
  any other trigger.
- Agent owner may authorize local agent runtime to request or apply pricing
  updates through local protocol communication only with a valid
  PricingConfigurationGrant and only within pricingUpdateRule.
- When pricingUpdateMode is `manual-only`, authorized-runtime requests create
  pending proposals and cannot change the active AgentPricingPolicy until owner
  approval.
- When pricingUpdateMode is `bounded-auto`, authorized-runtime requests that
  satisfy PricingConfigurationGrant and pricingUpdateRule are recorded as active
  AgentPricingPolicy versions.
- Authorized-runtime requests outside pricingUpdateRule become pending owner
  approval and do not affect quote generation.
- Agent identity alone does not authorize pricing configuration changes.
- PricingConfigurationGrant is separate from task-scoped PermissionGrant.
- Routine quote generation must use AgentPricingPolicy and must not invoke
  agent runtime model reasoning by default.
- Token-spending pricing analysis must be modeled as bounded PlanningTask or as
  an explicit local pricing update flow authorized by the agent owner.

### PricingConfigurationGrant

Fields:
- `id`
- `agentId`
- `ownerRef`
- `localRuntimeRef`
- `allowedActions`: `propose-pricing-update` or
  `apply-bounded-pricing-update`.
- `pricingUpdateMode`: `manual-only` or `bounded-auto`.
- `pricingUpdateRuleRef`
- `maxDelta`
- `maxExposure`
- `expiresAt`
- `revokedAt`
- `createdAt`
- `status`: `active`, `revoked`, `expired`.

Validation:
- PricingConfigurationGrant must be issued or revoked by the agent owner.
- PricingConfigurationGrant is local configuration authority; it does not grant
  task execution, workspace access, secrets, wallet access, or evidence
  submission rights.
- PricingConfigurationGrant is local-only and must not be propagated through
  public P2P gossip, peer handshake, or unauthenticated lookup.
- Remote projections may reference only opaque grant refs, hashes, versions, or
  status when an authorized audit path requires them.
- `propose-pricing-update` can create pending runtime proposals only.
- `apply-bounded-pricing-update` can activate pricing updates only when
  pricingUpdateMode is `bounded-auto` and all Pricing Update Rule limits pass.
- A revoked or expired PricingConfigurationGrant cannot authorize new
  AgentPricingPolicy versions.

### CapabilityNamespace

Fields:
- `id`
- `kind`: `global` or `company`.
- `companyId`: required when kind is `company`.
- `prefix`: `global` or `company:{companyId}`.
- `authorityKind`: `protocol-source` or `company-governance`.
- `protocolVersionRef`: required when kind is `global`.
- `sourceCommitRef`: optional source commit, release, or artifact reference for
  global namespaces.
- `governanceRef`: required when kind is `company`.
- `status`: `active`, `deprecated`, `revoked`.

Validation:
- Global namespaces are defined by AAO Protocol source/version, not by DAO or
  Company governance.
- Nodes that do not implement a global CapabilityDefinition version cannot
  accept, resolve, match, or prove that definition, but they may continue
  operating with supported global definition versions and company-scoped
  definitions.
- Unsupported global definition versions block only the task, claim, matching,
  or proof path that depends on that version and must report `upgrade-required`
  or `unsupported-capability`.
- Updating a global namespace or definition requires a protocol version/source
  update to gain that support; it does not mutate existing policy decisions or
  force otherwise compatible nodes off the network.
- Company namespaces are governed by the owning Company.
- Company namespace prefix must match the owning company id.

### CapabilityDefinition

Fields:
- `id`
- `namespaceId`
- `capabilityKey`
- `label`
- `description`
- `aliases`
- `version`
- `definitionHash`
- `status`: `draft`, `active`, `deprecated`, `revoked`.
- `createdByRef`
- `authorityKind`: `protocol-source` or `company-governance`.
- `protocolVersionRef`: required for global definitions.
- `sourceCommitRef`: optional for global definitions.
- `governanceRef`: required for company-scoped definitions.
- `createdAt`
- `deprecatedAt`

Validation:
- CapabilityDefinition key must use `global:*` or `company:{companyId}:*`.
- CapabilityDefinition key prefix must match its CapabilityNamespace.
- Global CapabilityDefinitions must come from the protocol source/version and
  cannot be created, amended, or deprecated through DAO governance.
- Company-scoped CapabilityDefinitions are created, amended, or deprecated by
  the owning Company's governance authority.
- Company-scoped CapabilityDefinitions describe executor capabilities in the
  owning Company's vocabulary; they do not describe capabilities owned by the
  Company itself.
- Changes create a new version; claims, policies, and proofs keep the
  CapabilityDefinition version they referenced.
- Deprecated definitions cannot be used for new claims or policy requirements
  unless a migration policy explicitly allows it.

### CapabilityRequirementRef

Fields:
- `capabilityDefinitionId`
- `capabilityDefinitionVersion`
- `capabilityKey`
- `namespaceId`
- `definitionHash`
- `acceptedAlternativeRefs`: optional exact alternative CapabilityRequirementRefs
  allowed by CompanyPolicy or TaskPolicy.
- `resolutionMode`: `exact-version` or `latest-active-resolved`.
- `resolvedAt`

Validation:
- TaskProposal input may ask for an exact CapabilityDefinition version or the
  latest active definition for a Capability Key.
- Company-scoped capability intents resolve only from local authorized Company
  context available to the node; resolution must not perform public
  network-wide P2P lookups.
- If authorized Company context is unavailable or delayed, resolution returns
  `pending-authorized-context` or an opaque `not-found-in-authorized-scope`
  result rather than revealing whether the capability exists.
- TaskPolicy and PolicyDecision must store exact CapabilityRequirementRefs; they
  must not depend on mutable `latest-active` references.
- Automatic fallback between CapabilityDefinition versions is not allowed.
- Fallback is valid only when CompanyPolicy or TaskPolicy explicitly declares
  exact accepted alternative refs for the task context.
- PolicyDecision must record which exact required or alternative ref was
  evaluated.
- Later CapabilityDefinition changes do not alter existing TaskPolicies,
  PolicyDecisions, assignments, evidence requirements, reputation signals, or
  settlement records.

### CapabilitySupportManifest

Fields:
- `nodeId`
- `protocolVersion`
- `supportedSchemaVersions`
- `supportedTopics`
- `supportedGlobalCapabilityRefs`: exact global CapabilityDefinition refs with
  definition hashes.
- `manifestHash`
- `manifestCid`: optional privacy-safe manifest reference when the summary is
  too large for handshake payloads.
- `createdAt`

Validation:
- Nodes must advertise protocolVersion and CapabilitySupportManifest during
  peer handshake.
- CapabilitySupportManifest must include only global CapabilityDefinition refs.
  It must not include `company:{companyId}:*` definitions.
- Peers must compute global capability compatibility from the intersection of
  supportedGlobalCapabilityRefs, not from protocolVersion alone.
- manifestHash is computed over canonical supportedGlobalCapabilityRefs sorted
  by namespace, capability key, version, and definition hash.
- Company-scoped CapabilityDefinitions are discovered only through authorized
  Company flows and applicable CompanyPolicy, TaskPolicy, and permission checks.
- Company-scoped CapabilityDefinitions must not be discovered through public P2P
  gossip, peer handshake, or unauthenticated search.
- Unsupported global CapabilityDefinition versions affect only dependent task,
  claim, matching, or proof paths; they do not invalidate otherwise compatible
  peer traffic.

### CapabilityClaim

Fields:
- `id`
- `agentId`
- `capabilityDefinitionId`
- `capabilityDefinitionVersion`
- `capabilityKey`
- `namespaceId`
- `definitionHash`
- `declaredAt`
- `status`: `active`, `withdrawn`, `superseded`.

Validation:
- CapabilityClaim must reference an active CapabilityDefinition version.
- CapabilityClaim is not evidence of quality by itself.

### ProvenCapability

Fields:
- `id`
- `agentId`
- `capabilityDefinitionId`
- `capabilityDefinitionVersion`
- `capabilityKey`
- `namespaceId`
- `definitionHash`
- `sourceReputationSignalIds`
- `evidenceBundleIds`
- `derivedAt`
- `status`: `active`, `superseded`, `revoked`.

Validation:
- ProvenCapability must derive from accepted evidence, validation,
  ReviewOutcome, or reputation signals.
- ProvenCapability cannot cross capability namespaces. Company-scoped proof for
  one company does not prove the same local capability for another company.
- ProvenCapability records the CapabilityDefinition version that was proven.

### CompanyPolicy

Fields:
- `id`
- `companyId`
- `name`
- `scope`: company, role, task category, risk level, or agent class.
- `approvalRule`
- `permissionRule`
- `evidenceRule`
- `autonomyRule`
- `reviewRule`
- `quoteRule`
- `planningRule`
- `expirationRule`
- `rewardRule`
- `agentSlashingRule`
- `version`
- `status`: `draft`, `active`, `superseded`, `revoked`.

Validation:
- Active CompanyPolicies must declare approval, permission, evidence, autonomy,
  review, quote, planning, expiration, reward, and agent slashing rule families.
- Changes create a new version; existing tasks keep their policy decision ref.

### TaskPolicy

Fields:
- `id`
- `taskProposalId`
- `taskId`: populated after proposal converts to task.
- `name`
- `requestedCapabilityRefs`
- `permissionRequirements`
- `evidenceRequirements`
- `reviewRequirements`
- `autonomyLimit`
- `budgetLimit`
- `quoteRequirements`
- `planningLimits`
- `expirationRequirements`
- `rewardRule`
- `agentSlashingRule`
- `version`
- `status`: `draft`, `active`, `superseded`, `revoked`.

Validation:
- TaskPolicy cannot weaken applicable CompanyPolicies.
- TaskPolicy requested capabilities must be exact CapabilityRequirementRefs
  resolved from active CapabilityDefinition versions in global or same-company
  capability namespaces.
- TaskPolicy permission requirements are declarative and do not grant access.
- TaskPolicy must state whether unclear work returns `needs-clarification`,
  `needs-decomposition`, or can create a bounded PlanningTask.
- TaskPolicy must define quote requirements before assignment when agent token
  or tool spend is expected.
- TaskPolicy must define or inherit ExpirationRules for quotes, reservations,
  assignment leases, grants, planning tasks, and review windows that can block
  the task.
- Changes create a new version; existing tasks keep their policy decision ref.

### ExpirationRule

Fields:
- `id`
- `scope`: `quote`, `budget-reservation`, `assignment-lease`,
  `permission-grant`, `pricing-configuration-grant`, `planning-task`,
  `review-window`, or `artifact-access-grant`.
- `companyPolicyVersionRef`
- `taskPolicyVersionRef`: optional.
- `duration`
- `gracePeriod`
- `renewalAuthorityRef`
- `onExpireAction`: `release`, `reassign`, `fail`, `dispute`, `release-budget`,
  `no-settlement`, `request-review`, or `reopen`.
- `status`: `active`, `superseded`, `revoked`.

Validation:
- Any entity with `expiresAt` that can block work, access, budget, or review
  must reference an active ExpirationRule.
- Expiration must produce a deterministic next action.
- Expiration cannot grant new access, assignment, payment, or settlement
  authority.

### EventAuthority

Fields:
- `id`
- `companyId`
- `eventType`
- `scopeRef`: company, task, assignment, evidence, review, dispute,
  reputation, settlement, payment, or protocol scope.
- `authorityKind`: `company-policy`, `task-policy`, `policy-decision`,
  `assignment-lease`, `executor`, `validation-authority`, `review-authority`,
  `dispute-authority`, `settlement-authority`, `payment-adapter`,
  `protocol-source`, or `governance-reference`.
- `authorityRef`
- `proofRefs`: policy version, PolicyDecision, PermissionGrant,
  AssignmentLease, ArtifactAccessGrant, ValidationAuthority, ReviewOutcome,
  DisputeCase, DisputeBond, DisputeResolutionPanel, DisputeOutcome,
  GovernanceReference, SolanaPaymentAdapter, or signer refs.
- `validFrom`
- `expiresAt`: optional.
- `status`: `active`, `superseded`, `revoked`, or `expired`.

Validation:
- EventAuthority states who may originate an event; it is not the same thing as
  the P2P node that relays the event.
- EventAuthority must derive from current protocol source, CompanyPolicy,
  TaskPolicy, PolicyDecision, grant, lease, validation authority, review
  authority, dispute authority, settlement authority, governance reference, or
  configured payment adapter.
- Expired or revoked EventAuthority cannot authorize new state-changing events.
- EventAuthority cannot grant broader access, payment, or execution rights than
  the referenced policy, grant, lease, adapter, or governance record allows.

### EventAdmissionRule

Fields:
- `id`
- `companyId`: optional for protocol-global event types.
- `eventType`
- `requiredAuthorityKinds`
- `requiredProofKinds`
- `schemaVersionRange`
- `causalRequirements`
- `privacyRequirements`
- `onFailure`: `reject`, `quarantine`, or `pending-authorized-context`.
- `status`: `active`, `superseded`, or `revoked`.

Validation:
- Every event that can change task state, permissions, artifact access,
  evidence, validation, review, reputation, budget, settlement, or payment
  state must pass an active EventAdmissionRule before local projections change.
- Admission verifies event issuer signature, EventAuthority, authority proofs,
  schema version, payload hash, causal ordering, replay protection, and privacy
  requirements.
- A valid P2P sender signature is necessary for transport acceptance but is not
  sufficient for event admission.
- Events that fail admission must not update authoritative task state,
  permissions, evidence, reputation, settlement, payment, or UI projections.

### TaskProposal

Fields:
- `id`
- `companyId`
- `requesterStakeholderId`
- `objective`
- `taskCategory`
- `riskLevel`: `low`, `medium`, `high`, `critical`.
- `capabilityIntents`: exact CapabilityDefinition refs or latest-active
  requests that must resolve before TaskPolicy activation.
- `budgetLimit`
- `pricingMode`: `fixed-budget`, `quote-required`, or `planning-required`.
- `rewardExpectation`
- `evidenceRequirements`
- `reviewRequirements`
- `status`: `draft`, `submitted`, `preflighted`, `needs-clarification`,
  `needs-decomposition`, `quote-pending`, `budget-reserved`, `blocked`,
  `approved`, `rejected`.

Relationships:
- May have one TaskPolicy.
- Capability intents resolve into TaskPolicy requestedCapabilityRefs before
  policy decision.
- May produce PreflightChecks, ExecutionQuotes, BudgetReservations, and
  PlanningTasks before assignment.
- Produces one or more PolicyDecisions.
- Converts to Task after a PolicyDecision with `decision: allow`.

### PreflightCheck

Fields:
- `id`
- `taskProposalId`
- `nodeId`
- `candidateExecutorRef`: optional.
- `policyCheck`
- `capabilityCheck`
- `budgetCheck`
- `clarityCheck`
- `authorizedContextCheck`
- `result`: `eligible`, `ineligible`, `needs-clarification`,
  `needs-decomposition`, `pending-authorized-context`, or `quote-required`.
- `reasons`
- `checkedAt`

Validation:
- PreflightCheck must be deterministic and must not invoke agent runtime model
  reasoning, external paid tools, or token-spending execution.
- PreflightCheck may reject, block, request clarification, request
  decomposition, or request quote before assignment.
- A PreflightCheck result is not a task assignment and grants no permissions.

### ExecutionQuote

Fields:
- `id`
- `taskProposalId`
- `planningTaskId`: present when quote is for planning work.
- `executorRef`
- `quoteKind`: `execution` or `planning`.
- `priceAmount`
- `tokenRef`
- `scopeSummary`
- `assumptions`
- `maxTokenSpend`
- `maxToolCalls`
- `maxWallClockTime`
- `maxDecompositionDepth`
- `maxSubtasks`
- `acceptedOutcomeKinds`
- `pricingPolicyVersionRef`
- `confidence`
- `validUntil`
- `status`: `submitted`, `accepted`, `rejected`, `expired`, `withdrawn`.

Validation:
- ExecutionQuote is a market offer; the protocol does not set the price.
- ExecutionQuote should be generated from AgentPricingPolicy by default.
- Routine quote generation must not invoke agent runtime model reasoning or paid
  tools.
- ExecutionQuote must fit CompanyPolicy, TaskPolicy, budget limit, risk level,
  and requester authority before acceptance.
- Accepted planning quotes must define bounded limits and accepted stop
  outcomes such as `needs-clarification`, `needs-next-planning-task`, or
  `unplannable-within-limits`.
- Quote acceptance does not allow execution until BudgetReservation,
  PolicyDecision, Assignment, and required PermissionGrants exist.

### BudgetReservation

Fields:
- `id`
- `executionQuoteId`
- `pricingPolicyVersionRef`: present when the accepted quote references an
  AgentPricingPolicy.
- `taskProposalId`
- `companyId`
- `amount`
- `tokenRef`
- `treasuryRef`
- `escrowRef`
- `budgetAllocationIds`
- `reservedAt`
- `expiresAt`
- `status`: `reserved`, `released`, `settled`, `expired`, `disputed`.

Validation:
- BudgetReservation must reference an accepted ExecutionQuote.
- Agent execution that spends model tokens or paid tools cannot begin before a
  matching active BudgetReservation exists.
- BudgetReservation may reserve funds for multiple BudgetAllocations, but only
  policy-authorized Compensable Contributions can receive allocations.
- Settlement consumes, releases, or disputes the BudgetReservation according to
  policy and evidence outcome.
- Baseline Node Validation cannot create a BudgetAllocation.

### BudgetAllocation

Fields:
- `id`
- `budgetReservationId`
- `taskId`
- `contributionKind`: `executor-reward`, `reviewer-fee`,
  `governance-validator-fee`, `dispute-resolver-fee`, `storage-fee`,
  `payment-execution-fee`, `dispute-cost`, `refund`, or `forfeit`.
- `recipientClass`: agent, stakeholder, resolver, validator service, storage
  provider, payment adapter, company treasury, requester, or protocol treasury.
- `recipientRef`: optional until the role is selected.
- `amount`
- `tokenRef`
- `policyRuleRef`
- `requiredEvidenceRefs`
- `status`: `reserved`, `earned`, `released`, `forfeited`, `refunded`, or
  `disputed`.

Validation:
- BudgetAllocation must reference an active BudgetReservation and policy rule.
- BudgetAllocation cannot weaken the total budget limit or exceed the reserved
  amount.
- BudgetAllocation is eligible for payment only after its corresponding
  Compensable Contribution is accepted by evidence, review, validation,
  DisputeOutcome, service record, or PaymentExecutionRecord status.
- `dispute-cost` allocations may reserve funds for resolver costs or dispute
  administration, or may receive forfeited Dispute Bond value after a
  frivolous dispute finding.
- Ordinary message validation, event admission, deduplication, compatibility
  checks, and local projection checks are Baseline Node Validation and cannot
  be allocated from task budget by default.

### PlanningTask

Fields:
- `id`
- `parentTaskProposalId`
- `executionQuoteId`
- `budgetReservationId`
- `objective`
- `maxTokenSpend`
- `maxToolCalls`
- `maxDecompositionDepth`
- `maxSubtasks`
- `acceptedOutcomeKinds`: `plan`, `needs-clarification`,
  `needs-next-planning-task`, `unplannable-within-limits`.
- `producedTaskProposalIds`
- `status`: `quoted`, `assigned`, `completed`, `expired`, `rejected`.

Validation:
- PlanningTask is paid work and follows the same assignment, evidence,
  validation, review, reputation, and settlement gates as other tasks.
- PlanningTask must not exceed quoted limits.
- Producing `needs-clarification`, `needs-next-planning-task`, or
  `unplannable-within-limits` is a valid outcome when supported by evidence.
- PlanningTask cannot recursively create unbounded PlanningTasks; depth and
  subtask count are governed by ExecutionQuote, TaskPolicy, and CompanyPolicy.

### PolicyDecision

Fields:
- `id`
- `taskProposalId`
- `companyPolicyVersionRefs`
- `taskPolicyVersionRef`
- `agentPolicyVersionRef`: present when a candidate agent is evaluated.
- `resolvedCapabilityRefs`
- `decision`: `allow`, `deny`, `needs-review`, `override-required`.
- `reasons`
- `requiredActions`
- `decidedAt`

Validation:
- Assignment cannot exist without an `allow` PolicyDecision.
- PolicyDecision must record every CompanyPolicy, TaskPolicy, and AgentPolicy
  version used during evaluation.
- PolicyDecision must record the exact CapabilityRequirementRefs evaluated,
  including definition hashes.
- `deny` maps the TaskProposal to `rejected`.
- `needs-review` maps the TaskProposal or Task to `needs-review` until the
  required review completes.
- `override-required` maps the TaskProposal or Task to `blocked` until a valid
  Governance Override is recorded.

### Task

Fields:
- `id`
- `proposalId`
- `companyId`
- `policyDecisionId`
- `state`: see Task State Machine.
- `createdAt`, `updatedAt`.

Relationships:
- Has Assignment, BudgetReservation, EvidenceBundle, ReviewRequest,
  ReviewOutcome, ValidationResult, ReputationSignal, and SettlementRecord.

### ExecutorRef

Value:
- Agent executor: `kind: agent`, `agentId`.
- Human executor: `kind: stakeholder`, `stakeholderId`.

Validation:
- Human executor stakeholder must belong to the same company as the task.
- Human execution must be authorized by CompanyPolicy and TaskPolicy.
- Executor interface is derived from kind: agent uses Agent Adapter;
  stakeholder uses Human Interface.

### Assignment

Fields:
- `id`
- `taskId`
- `executorRef`
- `agentPolicyId`: present for agent executor.
- `permissionGrantIds`: required for agent executor.
- `assignmentLeaseId`
- `assignedAt`
- `status`: `active`, `cancelled`, `expired`, `defaulted`, `completed`.

Validation:
- Agent executor assignments require AgentPolicy, PermissionGrants,
  CapabilityClaims or ProvenCapabilities, and reputation to satisfy applicable
  policies.
- Agent executor assignments require accepted ExecutionQuote and active
  BudgetReservation when model token spend or paid tool spend is expected.
- Stakeholder executor assignments require stakeholder membership in the same
  company as the task and CompanyPolicy/TaskPolicy authorization.
- Assignment must have an active AssignmentLease before runtime execution can
  start.

### AssignmentLease

Fields:
- `id`
- `assignmentId`
- `taskId`
- `executorRef`
- `heartbeatInterval`
- `progressEvidenceDeadline`
- `lastHeartbeatAt`
- `lastProgressEvidenceRef`
- `expiresAt`
- `expirationRuleRef`
- `renewalCount`
- `status`: `active`, `renewed`, `released`, `expired`, `defaulted`.

Validation:
- AssignmentLease prevents an executor or node from holding task availability
  indefinitely without heartbeat or progress evidence.
- Agent-executed tasks must produce heartbeat or progress evidence before the
  lease expires.
- Expired leases apply ExpirationRule and may release, reassign, dispute, or
  fail the task.
- Repeated agent lease default may produce a ReputationSignal or agent-slashing
  eligibility when policy allows it.
- AssignmentLease heartbeat is not evidence of task quality or completion.

### PermissionGrant

Fields:
- `id`
- `taskId`
- `agentId`
- `policyDecisionId`
- `scopes`
- `resourceRefs`
- `budgetLimit`
- `secretRefs`
- `issuedAt`
- `expiresAt`
- `revokedAt`
- `status`: `active`, `expired`, `revoked`.

Validation:
- PermissionGrant cannot be issued without an `allow` PolicyDecision.
- PermissionGrant cannot exceed CompanyPolicy, TaskPolicy, AgentPolicy, or
  secure node limits.

### EvidenceBundle

Fields:
- `id`
- `taskId`
- `assignmentId`
- `submittedByExecutorRef`
- `policyDecisionId`
- `artifactRefs`
- `artifactAccessGrantRefs`
- `workspaceSnapshotRefs`
- `logRefs`
- `summary`
- `contentHashes`
- `cidRefs`
- `privacyClassification`: `public`, `private-ref`, `encrypted-ref`.
- `submittedAt`
- `status`: `submitted`, `invalid`, `valid`, `needs-review`, `accepted`,
  `rejected`.

Validation:
- Must include all fields required by the policy evidence rule.
- Must be submitted by the task's assigned executor.
- Must not expose sensitive raw content in public records.
- Evidence that references private or encrypted artifacts must include
  applicable ArtifactAccessGrant refs for validation, review, dispute, or audit
  access.

### ArtifactReference

Fields:
- `id`
- `kind`: `cid`, `hash`, `commit`, `snapshot`, `external-url`,
  `encrypted-object`.
- `uriOrValue`
- `hash`
- `storageProvider`
- `privacyClassification`
- `requiresAccessGrant`
- `createdAt`

### ArtifactAccessGrant

Fields:
- `id`
- `artifactRefId`
- `taskId`
- `companyId`
- `subjectRef`: agent, stakeholder, reviewer, validator, or node.
- `purpose`: `execute`, `review`, `validate`, `dispute`, or `audit`.
- `scopes`: `read-metadata`, `fetch`, `decrypt`, or `validate`.
- `requesterRef`
- `approverRef`
- `policyDecisionId`
- `reviewRequestId`: optional.
- `validationResultId`: optional.
- `issuedAt`
- `expiresAt`
- `expirationRuleRef`
- `revokedAt`
- `accessLogRefs`
- `status`: `active`, `expired`, `revoked`, `denied`.

Validation:
- Private or encrypted ArtifactReferences require an active ArtifactAccessGrant
  before content can be read, fetched, decrypted, or validated.
- ArtifactAccessGrant must be purpose-scoped, time-bounded, revocable, and
  auditable.
- ArtifactAccessGrant cannot grant access outside the referenced task, artifact,
  policy, and subject scope.
- Public or on-chain records must not contain raw private artifact content or
  decryption keys.

### ValidationAuthority

Fields:
- `id`
- `kind`: `mechanical-validator` or `governance-validator`.
- `authorityRef`: node, stakeholder, owner, reviewer, DAO, or validator service.
- `companyId`
- `allowedValidationScopes`
- `artifactAccessGrantRefs`
- `policyVersionRefs`
- `status`: `active`, `suspended`, `revoked`.

Validation:
- Mechanical validators run deterministic checks and must not perform subjective
  review.
- Governance validators require CompanyPolicy, TaskPolicy, owner, reviewer, or
  DAO authorization.
- Validators can access private or encrypted artifacts only through active
  ArtifactAccessGrants.
- ValidationAuthority must be recorded on every ValidationResult.

### ValidationResult

Fields:
- `id`
- `evidenceBundleId`
- `validationAuthorityRef`
- `authorityKind`: `mechanical-validator` or `governance-validator`.
- `result`: `passed`, `failed`, `needs-review`.
- `checks`
- `missingFields`
- `privacyFindings`
- `validatorIdentity`
- `validatedAt`

### ReviewRequest

Fields:
- `id`
- `taskId`
- `evidenceBundleId`
- `policyDecisionId`
- `requiredReviewKind`: `human`, `dao`, `owner`, `hybrid`.
- `candidateReviewerStakeholderIds`
- `governanceRef`
- `quorumRule`
- `conflictPolicy`
- `requestedAt`
- `status`: `pending`, `in-review`, `outcome-recorded`, `cancelled`,
  `expired`.

Validation:
- Must be created after ValidationResult when policy requires review or
  ValidationResult returns `needs-review`.
- Candidate human reviewers must be authorized by CompanyPolicy or TaskPolicy.

### ReviewDecision

Fields:
- `id`
- `reviewRequestId`
- `taskId`
- `evidenceBundleId`
- `reviewerStakeholderId`: present for human reviewer decisions.
- `governanceRef`: present for DAO-referenced decisions.
- `decision`: `accepted`, `rejected`, `needs-more-evidence`, `disputed`.
- `rationaleSummary`
- `conflictDisclosure`
- `decidedAt`

Validation:
- Reviewer must satisfy the ReviewRequest authorization and conflict policy.
- Individual ReviewDecision does not by itself allow reputation or settlement.

### ReviewOutcome

Fields:
- `id`
- `reviewRequestId`
- `taskId`
- `evidenceBundleId`
- `decisionIds`
- `outcome`: `accepted`, `rejected`, `needs-more-evidence`, `disputed`.
- `quorumResult`
- `conflictFindings`
- `rationaleSummary`
- `decidedAt`

Validation:
- Required review must produce ReviewOutcome before reputation or settlement
  changes.
- ReviewOutcome cannot be recorded before ValidationResult.
- Outcome must be derived from ReviewRequest quorum, conflict, DAO, and dispute
  rules.

### ReputationSignal

Fields:
- `id`
- `agentId`
- `companyId`
- `taskId`
- `evidenceBundleId`
- `validationResultId`
- `reviewOutcomeId`: present when review is required.
- `domain`
- `impact`: `positive`, `neutral`, `negative`.
- `scoreDelta`
- `privacyClassification`: `public-aggregate`, `authorized-detail`, or
  `private-company-detail`.
- `reason`
- `createdAt`

Validation:
- Must reference accepted evidence and any required ReviewOutcome.
- Created only for tasks executed by an Agent.
- Public reputation projections must not reveal private company, task, evidence,
  or company-scoped capability details.

### ReputationProjection

Fields:
- `id`
- `agentId`
- `projectionKind`: `public-aggregate`, `capability-aggregate`,
  `authorized-company-detail`, or `private-company-detail`.
- `capabilityRef`: optional.
- `companyId`: present only for authorized or private projections.
- `sourceReputationSignalRefs`
- `aggregateScore`
- `summary`
- `visibilityRuleRef`
- `commitmentHash`
- `updatedAt`

Validation:
- Public projections expose aggregate trust only.
- Company, task, evidence, reviewer, artifact, or company-scoped capability
  details require an authorized projection.
- ReputationProjection must be derived from accepted ReputationSignals.
- Public projections must remain useful for matching without leaking private
  work history.

### DisputeCase

Fields:
- `id`
- `taskId`
- `companyId`
- `openedByRef`
- `disputedRecordRefs`
- `reason`
- `evidenceRefs`
- `openedAt`
- `evidenceDeadline`
- `resolutionDeadline`
- `resolverPoolRef`
- `disputeResolutionPanelId`
- `resolverAuthorityRef`
- `disputeBondId`: present when a blocking dispute posts a bond.
- `disputeCostAllocationId`: present when a pre-reserved budget allocation
  funds the blocking dispute path.
- `governanceDisputeWaiverRef`: present only when DAO-governed authority
  waives the bond requirement.
- `frivolousFindingRef`: present when the dispute is found abusive,
  unsupported, spammy, or bad-faith.
- `blockingEffects`: reputation, settlement, payment, budget, assignment, or
  review.
- `disputeOutcomeId`: present after resolution.
- `status`: `opened`, `evidence-window`, `resolver-selection`,
  `panel-review`, `resolution-pending`, `resolved`, `cancelled`, or `expired`.

Validation:
- DisputeCase must identify what record or outcome is being challenged.
- DisputeCase may challenge ReviewDecision, ReviewOutcome, reviewer conflict
  disclosure, ValidationResult, EvidenceBundle, Assignment, ReputationSignal,
  BudgetReservation, SettlementRecord, or PaymentExecutionRecord.
- Blocking DisputeCases pause final reputation, settlement, payment execution,
  or budget release for the disputed scope.
- A blocking DisputeCase must reference an active DisputeBond or pre-reserved
  dispute-cost BudgetAllocation unless a DAO-governed Governance Dispute Waiver
  is present.
- Private-company owners, executors, reviewers, payment controllers, agents,
  and ordinary nodes cannot waive the bond requirement by default.
- Emergency pause authority, when allowed by policy, does not decide dispute
  merits and does not remove the DisputeBond or dispute-cost requirement for a
  blocking DisputeCase.
- DisputeCase must have bounded evidence and resolution windows.
- Dispute resolution authority must be authorized by CompanyPolicy, TaskPolicy,
  ResolverPool, DAO reference, or governance override.
- Disputes against ReviewDecision or ReviewOutcome must select conflict-free
  DisputeResolvers who were not the task requester, executor, challenged
  reviewer, original validator, payment controller, or otherwise conflicted
  participant.
- Resolved DisputeCase outcome must be referenced by any later settlement,
  reputation, budget, or payment record it affected.

### DisputeBond

Fields:
- `id`
- `disputeCaseId`
- `postedByRef`
- `amount`
- `tokenRef`
- `escrowRef`
- `requiredByPolicyRef`
- `postedAt`
- `releasedAt`
- `forfeitureReason`: present for frivolous, abusive, unsupported, spammy, or
  bad-faith disputes.
- `settlementLineId`: present when forfeiture or refund is itemized.
- `status`: `posted`, `refunded`, `forfeited`, `released`, or `disputed`.

Validation:
- DisputeBond is a refundable guarantee, not Slashing.
- DisputeBond is required before a DisputeCase can apply blocking effects
  unless a pre-reserved dispute-cost BudgetAllocation or DAO-governed
  Governance Dispute Waiver is present.
- If the DisputeOutcome finds the case frivolous, abusive, unsupported, spammy,
  or bad-faith, the bond may be forfeited according to policy and itemized in
  SettlementLines for resolver costs, affected-party costs, or treasury
  recovery.
- If the dispute has merit or is resolved without a frivolous finding, the bond
  must be refunded or released according to policy.
- A private-company owner cannot waive DisputeBond by default.

### ResolverPool

Fields:
- `id`
- `companyId`
- `scope`: company, task category, capability, risk level, or dispute type.
- `candidateResolverRefs`
- `eligibilityRule`
- `conflictRule`
- `selectionRule`
- `quorumRule`
- `minPanelSize`
- `maxPanelSize`
- `evidenceAccessRule`
- `version`
- `status`: `draft`, `active`, `superseded`, or `revoked`.

Validation:
- ResolverPool must be created or referenced by CompanyPolicy, TaskPolicy, DAO
  governance, or owner-approved governance configuration.
- ResolverPool eligibility must exclude conflicted task participants for each
  DisputeCase.
- ResolverPool must define how private evidence access is granted, minimized,
  audited, and revoked.
- ResolverPool changes create new versions; active DisputeCases keep the pool
  version selected at opening.

### DisputeResolver

Fields:
- `id`
- `resolverRef`: stakeholder, DAO-selected identity, approved external
  resolver, or validator service.
- `companyId`: optional when resolver is network-level.
- `capabilityRefs`
- `reputationProjectionRef`
- `conflictDisclosure`
- `status`: `eligible`, `suspended`, `removed`, or `conflicted`.

Validation:
- DisputeResolver must satisfy the ResolverPool eligibility rule for the
  DisputeCase.
- DisputeResolver must not be the task requester, executor, challenged
  reviewer, original validator, payment controller, or otherwise conflicted
  participant.
- DisputeResolver access to private or encrypted evidence requires active
  ArtifactAccessGrants scoped to dispute resolution.

### DisputeResolutionPanel

Fields:
- `id`
- `disputeCaseId`
- `resolverPoolRef`
- `resolverRefs`
- `selectionProofRefs`
- `quorumRule`
- `conflictPolicy`
- `artifactAccessGrantRefs`
- `status`: `selecting`, `active`, `decision-recorded`, `cancelled`, or
  `expired`.

Validation:
- Panel selection must satisfy ResolverPool selection, eligibility, conflict,
  and quorum rules.
- Panel resolvers receive only the ArtifactAccessGrants needed to decide the
  disputed scope.
- A panel cannot finalize a DisputeOutcome until required ResolverDecisions or
  governance references satisfy quorum.

### ResolverDecision

Fields:
- `id`
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverRef`
- `disputedRecordRefs`
- `decision`: `upheld`, `reversed`, `needs-more-evidence`, `no-settlement`,
  `slash-eligible`, `reviewer-misconduct`, or `dismissed`.
- `rationaleSummaryHash`
- `evidenceRefs`
- `conflictDisclosure`
- `decidedAt`

Validation:
- ResolverDecision must be signed by a selected DisputeResolver.
- ResolverDecision must reference only evidence available through authorized
  public refs or active ArtifactAccessGrants.
- Individual ResolverDecision does not by itself finalize reputation,
  settlement, budget release, or payment execution.

### DisputeOutcome

Fields:
- `id`
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverDecisionIds`
- `outcome`: `upheld`, `reversed`, `needs-more-evidence`, `no-settlement`,
  `slash-eligible`, `reviewer-misconduct`, or `dismissed`.
- `quorumResult`
- `conflictFindings`
- `rationaleSummaryHash`
- `decidedAt`

Validation:
- DisputeOutcome applies the DisputeResolutionPanel quorum, conflict, and
  evidence rules.
- DisputeOutcome is required before a blocking DisputeCase can release
  reputation, settlement, budget, or payment effects.
- `reviewer-misconduct` outcomes produce audit and governance actions for the
  reviewer or role assignment; agent slashing remains agent-only.
- Resolved DisputeOutcome must be referenced by later records it changes,
  reverses, blocks, or authorizes.

### SettlementRecord

Fields:
- `id`
- `taskId`
- `companyId`
- `executorRef`
- `policyDecisionId`
- `budgetReservationId`: present when quote/budget reservation was required.
- `evidenceBundleId`
- `reviewOutcomeId`: present when review is required.
- `disputeCaseId`: present when settlement depends on a resolved dispute.
- `reputationSignalId`: present only when the executor is an Agent and policy
  changes reputation.
- `outcome`: `reward`, `slash`, `no-settlement`, `disputed`.
- `amount`
- `tokenRef`
- `settlementLineIds`
- `governanceRef`
- `treasuryRef`
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`: present for default DAO-governed payment flows.
- `paymentExecutionRecordId`
- `commitmentHash`
- `solanaSignatureRef`
- `createdAt`

Validation:
- Cannot be created before passed ValidationResult and required accepted
  ReviewOutcome.
- Must reference the task assignment executor.
- Must consume, release, or dispute any referenced BudgetReservation according
  to the accepted outcome.
- Blocking DisputeCases must be resolved before final settlement or payment
  execution.
- `slash` outcome is valid only when `executorRef.kind` is `agent`.
- Public and hybrid company settlement must include governance or treasury refs
  when policy requires DAO authorization.
- SettlementRecord may include multiple SettlementLines for executor reward,
  reviewer fee, governance validator fee, dispute resolver fee, storage fee,
  payment execution fee, dispute cost, refund, or forfeit.
- SettlementRecord must not pay Baseline Node Validation.
- SettlementRecord authorizes an economic outcome; PaymentExecutionRecord
  records actual payment execution through an adapter.

### SettlementLine

Fields:
- `id`
- `settlementRecordId`
- `taskId`
- `budgetAllocationId`
- `contributionKind`: `executor-reward`, `reviewer-fee`,
  `governance-validator-fee`, `dispute-resolver-fee`, `storage-fee`,
  `payment-execution-fee`, `dispute-cost`, `refund`, or `forfeit`.
- `recipientRef`
- `amount`
- `tokenRef`
- `basisRefs`: EvidenceBundle, ReviewDecision, ReviewOutcome,
  ValidationResult, ResolverDecision, DisputeOutcome, StorageObjectRef,
  DisputeBond, PaymentExecutionRecord, or governance refs.
- `outcome`: `pay`, `refund`, `forfeit`, `no-pay`, or `disputed`.
- `createdAt`

Validation:
- SettlementLine must reference a BudgetAllocation unless it records
  no-settlement or a governance-authorized correction.
- SettlementLine must reference accepted evidence, review, validation, dispute,
  service, payment, or governance records that justify the economic outcome.
- Baseline Node Validation cannot produce SettlementLines.
- Reviewer, validator, resolver, storage, and payment execution lines are valid
  only when CompanyPolicy or TaskPolicy explicitly authorizes those
  compensable contributions.
- Dispute-cost, refund, and forfeit lines may reference DisputeBond when a
  DisputeOutcome or governance correction determines refund or forfeiture.

### SolanaPaymentAdapter

Fields:
- `id`
- `adapterKind`: `dao-default`, `custom-solana`, or `disabled`.
- `daoPaymentInterfaceRef`
- `supportedTokenRefs`
- `signerPolicyRef`
- `status`: `active`, `suspended`, `revoked`.

Validation:
- SolanaPaymentAdapter is modular and replaceable.
- The default adapter consumes the configured DAOPaymentInterface for
  DAO-governed treasury payment flows.
- Adapter cannot execute payment without accepted SettlementRecord and required
  governance authorization.
- Adapter must not expose private evidence, artifact, grant, or workspace data
  on-chain.

### DAOPaymentInterface

Fields:
- `id`
- `companyId`
- `daoGovernanceRef`
- `treasuryRef`
- `paymentInstructionSchemaRef`
- `observedEventRefs`
- `status`: `active`, `paused`, `revoked`.

Validation:
- DAOPaymentInterface is the default interface AAO listens to and consumes for
  DAO-governed treasury payment flows.
- DAOPaymentInterface does not define AAO policy by itself; it supplies payment
  authorization and treasury execution inputs.
- Public and hybrid companies using DAO treasury payment must configure an
  active DAOPaymentInterface or explicitly disable payment execution.

### PaymentExecutionRecord

Fields:
- `id`
- `settlementRecordId`
- `settlementLineIds`
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`
- `paymentInstructionRef`
- `amount`
- `tokenRef`
- `treasuryRef`
- `recipientRef`
- `status`: `prepared`, `submitted`, `confirmed`, `failed`, `cancelled`.
- `solanaSignatureRef`
- `failureReason`
- `createdAt`
- `confirmedAt`

Validation:
- PaymentExecutionRecord cannot exist without an accepted SettlementRecord.
- PaymentExecutionRecord may execute one or more payable SettlementLines.
- PaymentExecutionRecord records payment execution attempt and status; it is not
  the SettlementRecord itself.
- Failed payment execution does not rewrite evidence, review, reputation, or
  settlement; it creates a payment failure state requiring retry, cancellation,
  or governance action.

### Workspace

Fields:
- `id`
- `companyId`
- `name`
- `manifestRef`
- `status`: `active`, `archived`.

### NodeIdentity

Fields:
- `id`
- `publicKey`
- `walletRef`
- `signerRef`
- `secretVaultRef`
- `permissions`
- `createdAt`

## Task State Machine

```text
draft
 -> proposed
 -> preflighted
 -> quote-pending
 -> budget-reserved
 -> approved
 -> assigned
 -> running
 -> evidence-submitted
 -> needs-review
 -> validated
 -> accepted
 -> settlement-pending
 -> settled

Failure/dispute states:
proposed -> needs-clarification
proposed -> needs-decomposition
proposed -> blocked
preflighted -> blocked
quote-pending -> blocked
proposed -> rejected
assigned -> lease-expired
assigned -> failed
evidence-submitted -> rejected
needs-review -> disputed
accepted -> disputed
rejected -> disputed
accepted agent task -> settlement-pending -> slashed
```

Rules:
- `preflighted` requires PreflightCheck.
- `quote-pending` is used when policy requires ExecutionQuote before
  assignment.
- `budget-reserved` requires accepted ExecutionQuote and active
  BudgetReservation.
- `approved` requires an `allow` PolicyDecision.
- `assigned` requires an eligible executor. Agent executors require active
  AgentPolicy and required PermissionGrants; market-priced agent execution also
  requires an active AgentPricingPolicy reference on the accepted quote.
  Stakeholder executors require same-company membership and human execution
  authorization.
- `running` requires an active AssignmentLease for agent-executed work.
- `lease-expired` applies when AssignmentLease expires before heartbeat or
  progress evidence satisfies the lease rule.
- `assigned` for token-spending agent execution requires BudgetReservation when
  quote policy requires it.
- `evidence-submitted` requires EvidenceBundle.
- `accepted` requires passed ValidationResult and ReviewOutcome when policy
  requires review.
- `disputed` can challenge evidence, validation, review, assignment,
  reputation, budget, settlement, or payment before finality and requires
  DisputeBond, pre-reserved dispute-cost BudgetAllocation, or DAO-governed
  Governance Dispute Waiver before blocking effects apply; DisputeOutcome is
  required before blocked effects continue.
- `settled` requires SettlementRecord.
- Payment execution requires SettlementRecord plus SolanaPaymentAdapter when
  payment is enabled.
- `slashed` requires SettlementRecord with agent executor and `outcome: slash`.

## Derived Projections

- Company Policy dashboard.
- Agent eligibility and capability search.
- Quote and budget reservation queue.
- Budget allocation and settlement line ledger.
- Task lifecycle board.
- Evidence validation queue.
- Review queue.
- Reputation and settlement ledger.
- Dispute case queue.
- Payment execution queue.
- Assignment lease and availability monitor.
- P2P inbox/outbox health.

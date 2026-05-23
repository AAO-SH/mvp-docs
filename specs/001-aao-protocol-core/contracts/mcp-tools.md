# Contract: MCP Tools and Resources

AAO exposes MCP tools to external AI runtimes through the Agent Adapter. Agents
are untrusted and can only act through these policy-bound surfaces.

## Tools

### `aao.agent.handshake`

Purpose: Register or refresh an agent identity, Agent Policy, Agent Pricing
Policy, and Capability Claims.

Input:
- `publicKey`
- `runtimeKind`
- `adapterVersion`
- `acceptedMessageTypes`
- `rejectedMessageTypes`
- `acceptedPermissionScopes`
- `rejectedPermissionScopes`
- `autonomyLimits`
- `agentPricingPolicy`
- `capabilityClaims`

Output:
- `agentId`
- `agentPolicyId`
- `agentPricingPolicyId`
- `status`
- `rejectedReasons`

Rules:
- No task can be assigned until handshake is accepted.
- Agent Policy changes create a new policy version.
- Agent Pricing Policy changes create a new pricing policy version.
- Capability Claims are versioned CapabilityDefinition refs, not Proven
  Capabilities, and include the referenced definition hash.
- CapabilityDefinition keys must use `global:*` or `company:{companyId}:*`.
- `global:*` CapabilityDefinitions are accepted only when the local AAO Protocol
  source/version implements them.
- Unsupported global CapabilityDefinition versions are rejected only for the
  dependent claim or task path with `upgrade-required` or
  `unsupported-capability`; other supported capability versions may continue.
- The adapter validates global Capability Claims against the local node's
  Capability Support Manifest.
- Company-scoped Capability Claims are validated through Company-authorized
  context, not public peer capability manifests.
- When Company-authorized context is unavailable, responses must use
  `pending-authorized-context` or `not-found-in-authorized-scope`; they must not
  reveal whether a company-scoped CapabilityDefinition exists.

### `aao.agent.pricing.get`

Purpose: Let an agent owner or authorized local runtime inspect the active Agent
Pricing Policy used for routine quote generation.

Input:
- `agentId`
- `requesterRef`

Output:
- `agentPricingPolicyId`
- `pricingPolicyVersionRef`
- `pricingSummary`
- `pricingUpdateMode`
- `pricingUpdateRuleSummary`
- `updatedBy`
- `runtimeProposalRef`
- `effectiveFrom`
- `status`

Rules:
- Company requesters cannot use this tool to change an agent's pricing.
- Responses must expose only the pricing details visible to the requester.
- Reading Agent Pricing Policy must not invoke agent runtime reasoning.

### `aao.agent.pricing.update`

Purpose: Update Agent Pricing Policy through the local protocol configuration
interface, either manually by the agent owner or through an authorized local
runtime request.

Input:
- `agentId`
- `requesterRef`
- `updateMode`: `owner-manual` or `authorized-runtime`
- `pricingUpdateMode`: `manual-only` or `bounded-auto`
- `pricingRules`
- `minimumPrice`
- `tokenRef`
- `riskMultipliers`
- `capabilityMultipliers`
- `planningPriceRules`
- `maxQuoteExposure`
- `pricingUpdateRule`
- `pricingConfigurationGrantRef`
- `runtimeProposalRef`
- `ownerAuthorizationRef`

Output:
- `agentPricingPolicyId`
- `pricingPolicyVersionRef`
- `status`: `active`, `pending-owner-approval`, or `rejected`
- `rejectedReasons`

Rules:
- Only the agent owner or an authorized local runtime may request updates.
- Authorized-runtime requests must include a valid Pricing Configuration Grant;
  the Agent identity alone is not sufficient.
- This contract defines the local configuration surface only; it does not define
  whether the runtime caller is triggered by cron, heartbeat, chat, endpoint,
  API client, or any other runtime mechanism.
- When pricingUpdateMode is `manual-only`, authorized-runtime updates become
  `pending-owner-approval`.
- When pricingUpdateMode is `bounded-auto`, authorized-runtime updates must
  satisfy the owner-defined pricingUpdateRule to become active automatically.
- Accepted updates create a new Agent Pricing Policy version.
- Rejected or pending updates do not affect active quote generation.
- The tool updates local protocol configuration; it does not assign work or
  permit task execution.

### `aao.agent.pricing.grant`

Purpose: Let the agent owner issue or revoke local runtime authority for pricing
configuration updates.

Input:
- `agentId`
- `ownerRef`
- `localRuntimeRef`
- `action`: `issue` or `revoke`
- `allowedActions`
- `pricingUpdateMode`
- `pricingUpdateRuleRef`
- `maxDelta`
- `maxExposure`
- `expiresAt`

Output:
- `pricingConfigurationGrantId`
- `status`
- `rejectedReasons`

Rules:
- Only the agent owner may issue or revoke a Pricing Configuration Grant.
- Pricing Configuration Grant does not grant task execution, workspace access,
  secrets, wallet access, or evidence submission rights.
- Pricing Configuration Grant is local-only; this tool must not publish grant
  contents to public P2P topics or unauthenticated peers.
- Revoked or expired grants cannot authorize Agent Pricing Policy updates.

### `aao.task.getAssignable`

Purpose: Let an agent inspect funded tasks it is eligible to execute after quote
acceptance and budget reservation.

Input:
- `agentId`
- `capabilityDefinitionFilter`
- `maxRiskLevel`

Output:
- `tasks[]` with `taskId`, `objectiveSummary`, `riskLevel`,
  `resolvedCapabilityRefs`, `evidenceRequirements`, `policyDecisionId`,
  `executionQuoteId`, and `budgetReservationId`.

Rules:
- Tool returns only tasks that satisfy current Agent Permissions and applicable
  policies.
- Tool returns only execution-ready tasks with accepted Execution Quote and
  active Budget Reservation when model token spend or paid tool spend is
  expected.
- Assignment eligibility is evaluated against resolved CapabilityDefinition
  versions, not mutable latest-active capability inputs.
- The adapter must not automatically fall back to another CapabilityDefinition
  version unless the resolved TaskPolicy or CompanyPolicy declares that exact
  alternative.
- Company-scoped task eligibility is evaluated from local authorized Company
  context. The tool must not perform public network-wide P2P lookups before
  answering.

### `aao.task.getQuoteRequests`

Purpose: Let an agent inspect preflighted work requests it may quote without
starting execution.

Input:
- `agentId`
- `capabilityDefinitionFilter`
- `maxRiskLevel`

Output:
- `quoteRequests[]` with `taskProposalId`, `objectiveSummary`, `riskLevel`,
  `resolvedCapabilityRefs`, `budgetLimit`, `pricingMode`,
  `evidenceRequirements`, `planningLimits`, and `quoteRequirements`.

Rules:
- The tool returns only requests that passed Preflight Check for the caller's
  visible policy and capability scope.
- Returning a quote request grants no assignment, permission, secrets, or
  execution authority.
- The adapter must not invoke agent runtime execution merely to inspect quote
  requests.

### `aao.quote.submit`

Purpose: Submit a scoped Execution Quote for execution or planning work.

Input:
- `agentId`
- `taskProposalId`
- `quoteKind`: `execution` or `planning`
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
- `requestedBudgetAllocations`: optional allocations for policy-required
  compensable review, validation, dispute, storage, or payment execution costs.
- `pricingPolicyVersionRef`
- `confidence`
- `validUntil`

Output:
- `executionQuoteId`
- `status`
- `rejectedReasons`

Rules:
- Execution Quotes do not assign work and do not permit model token spend.
- Agent-submitted quotes must reference the Agent Pricing Policy version used
  unless a prior Planning Task explicitly authorized paid pricing analysis.
- Routine quote submission must be derived from Agent Pricing Policy and must
  not invoke runtime reasoning by default.
- Planning quotes must include bounded token, tool, decomposition depth, and
  subtask limits.
- Quote acceptance and Budget Reservation are required before assignment.
- Quote-requested allocations are not payable until policy creates
  BudgetAllocations and SettlementLines.
- Baseline Node Validation cannot be included as a requested allocation.

### `aao.task.acceptAssignment`

Purpose: Bind an eligible agent to an approved task.

Input:
- `agentId`
- `taskId`
- `agentPolicyId`

Output:
- `assignmentId`
- `assignmentLeaseId`
- `leaseExpiresAt`
- `heartbeatInterval`
- `permissionGrantIds`
- `taskState`
- `evidenceRequirements`

Rules:
- Assignment fails if Agent Policy, permission requirements, reputation, or
  capability requirements no longer match.
- Assignment fails if an accepted Execution Quote and active Budget Reservation
  are required but absent.
- Agent runtime execution must not begin before assignment, active
  AssignmentLease, and required Permission Grants are returned.

### `aao.assignment.heartbeat`

Purpose: Renew assignment availability and prove the executor has not abandoned
the task.

Input:
- `assignmentId`
- `assignmentLeaseId`
- `executorRef`
- `progressEvidenceRef`: optional.
- `heartbeatSummary`

Output:
- `assignmentLeaseId`
- `leaseStatus`
- `leaseExpiresAt`
- `nextRequiredAction`

Rules:
- Heartbeat does not prove task quality or completion.
- Missing heartbeat or progress evidence before lease expiry applies the
  AssignmentLease ExpirationRule.
- Expired leases may release, reassign, dispute, or fail the task according to
  policy.

### `aao.artifact.access.request`

Purpose: Request scoped access to a private or encrypted artifact reference for
execution, validation, review, dispute, or audit.

Input:
- `artifactRefId`
- `taskId`
- `subjectRef`
- `purpose`: `execute`, `review`, `validate`, `dispute`, or `audit`
- `scopes`
- `requesterRef`

Output:
- `artifactAccessGrantId`
- `status`
- `expiresAt`
- `rejectedReasons`

Rules:
- Private or encrypted artifacts cannot be read, fetched, decrypted, or
  validated without an active Artifact Access Grant.
- Grants must be purpose-scoped, time-bounded, revocable, and auditable.
- Grant responses must not include raw private content or decryption keys.

### `aao.evidence.submit`

Purpose: Submit artifact references and evidence after execution.

Input:
- `assignmentId`
- `artifactRefs`
- `artifactAccessGrantRefs`
- `workspaceSnapshotRefs`
- `logRefs`
- `summary`
- `contentHashes`
- `cidRefs`
- `privacyClassification`

Output:
- `evidenceBundleId`
- `validationStatus`
- `missingFields`
- `nextRequiredAction`

Rules:
- Raw sensitive content is rejected unless submitted as encrypted/private ref.
- Evidence submission does not imply acceptance.

### `aao.dispute.open`

Purpose: Open a bounded Dispute Case against evidence, validation, review,
assignment, reputation, budget, or settlement.

Input:
- `taskId`
- `openedByRef`
- `disputedRecordRefs`
- `reason`
- `evidenceRefs`
- `disputeBondRef`: required when blocking effects are requested unless a
  dispute cost allocation or governance waiver applies
- `disputeCostAllocationRef`: optional pre-reserved dispute-cost allocation
- `governanceDisputeWaiverRef`: optional DAO-governed waiver

Output:
- `disputeCaseId`
- `status`
- `evidenceDeadline`
- `resolutionDeadline`
- `resolverPoolRef`
- `disputeResolutionPanelId`
- `disputeBondId`
- `bondStatus`: `required`, `posted`, `waived`, `not-required`, or `rejected`
- `disputeCostAllocationId`
- `blockingEffects`

Rules:
- Dispute Case must identify the disputed record or outcome.
- Disputes may challenge ReviewDecision or ReviewOutcome when a reviewer is
  suspected of lying, colluding, being conflicted, or misapplying policy.
- Blocking disputes pause final reputation, settlement, payment execution, or
  budget release only for the disputed scope.
- Blocking disputes require an active Dispute Bond, pre-reserved dispute-cost
  Budget Allocation, or DAO-governed Governance Dispute Waiver before blocking
  effects apply.
- Private-company owners, executors, reviewers, payment controllers, agents,
  and ordinary nodes cannot waive Dispute Bond by default.
- Dispute opening does not reveal private evidence without Artifact Access
  Grants.
- Dispute resolver selection must use the policy-defined Resolver Pool and
  exclude conflicted task participants.
- Disputes later found frivolous, abusive, unsupported, spammy, or bad-faith
  may forfeit the Dispute Bond according to policy; this is not agent slashing.

### `aao.dispute.resolver.decision.submit`

Purpose: Let a selected Dispute Resolver submit a signed decision for a Dispute
Case.

Input:
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverRef`
- `disputedRecordRefs`
- `decision`
- `rationaleSummaryHash`
- `evidenceRefs`
- `artifactAccessGrantRefs`
- `conflictDisclosure`

Output:
- `resolverDecisionId`
- `disputeOutcomeId`: present when quorum is satisfied.
- `status`
- `nextRequiredAction`

Rules:
- Resolver must belong to the selected Dispute Resolution Panel.
- Resolver must not be the task requester, executor, challenged reviewer,
  original validator, payment controller, or otherwise conflicted participant.
- Private or encrypted evidence access must reference active Artifact Access
  Grants scoped to dispute resolution.
- Individual ResolverDecision does not finalize the dispute until quorum and
  conflict rules produce a Dispute Outcome.

### `aao.payment.status`

Purpose: Inspect payment execution state for an accepted Settlement Record.

Input:
- `settlementRecordId`
- `requesterRef`

Output:
- `paymentExecutionRecordId`
- `settlementRecordId`
- `settlementLineIds`
- `adapterKind`
- `daoPaymentInterfaceRef`
- `status`
- `solanaSignatureRef`
- `failureReason`

Rules:
- Settlement authorization and payment execution are separate.
- Payment status responses must not expose private evidence, workspace content,
  grants, or decryption material.

### `aao.task.status`

Purpose: Return lifecycle state and next action for a task.

Input:
- `taskId`
- `requesterId`

Output:
- `state`
- `nextRequiredAction`
- `responsibleParty`
- `preflightCheckId`
- `executionQuoteId`
- `budgetReservationId`
- `budgetAllocationIds`
- `assignmentLeaseId`
- `evidenceBundleId`
- `reviewRequestId`
- `reviewOutcomeId`
- `disputeCaseId`
- `disputeBondId`
- `disputeCostAllocationId`
- `governanceDisputeWaiverRef`
- `settlementRecordId`
- `settlementLineIds`
- `paymentExecutionRecordId`

## Resources

### `aao://task/{taskId}`

Read-only task lifecycle state, preflight, quote, budget reservation, budget
allocations, policy
decision, assignment lease, evidence refs, review state, dispute state,
resolver panel refs, reputation impact, settlement refs, settlement lines, and
payment execution ref.

### `aao://agent/{agentId}`

Read-only Agent Policy, Agent Pricing Policy summary, Capability Claims, Proven
Capabilities, and reputation signals visible to the requester.

### `aao://company/{companyId}/policies`

Read-only active CompanyPolicy versions and rule summaries visible to the
caller.

### `aao://task/{taskId}/policy`

Read-only TaskPolicy version and rule summary visible to the caller.

# Contract: Protocol Events

AAO nodes exchange and persist immutable lifecycle events. Every event uses the
same envelope so storage, P2P, UI, and audit projections can consume it.

## Event Envelope

```json
{
  "eventId": "evt_...",
  "eventType": "task.proposed",
  "schemaVersion": "1.0.0",
  "companyId": "cmp_...",
  "actorId": "stakeholder_or_agent_or_node_id",
  "issuerRef": "stakeholder_or_agent_or_node_or_adapter_id",
  "eventAuthorityRef": "event_authority_...",
  "authorityProofRefs": ["policy_decision_or_grant_or_adapter_ref"],
  "causationId": "evt_...",
  "correlationId": "task_or_workflow_id",
  "occurredAt": "2026-05-19T00:00:00.000Z",
  "payloadHash": "sha256:...",
  "issuerSignature": "base64-signature",
  "payload": {}
}
```

Rules:
- `eventId` is unique across the node.
- `schemaVersion` is required for every event type.
- `payloadHash` is computed over canonical payload JSON.
- Sensitive payload details must be replaced by privacy-safe references.
- `issuerSignature` signs the canonical event envelope and payload hash; relay
  metadata is not part of the event issuer signature.
- P2P sender signatures authenticate transport. Event issuer signatures plus
  Event Authority authenticate who was allowed to originate the event.
- Events that can change task state, permissions, artifact access, evidence,
  validation, review, reputation, budget, settlement, or payment execution must
  pass Event Admission Rules before local projections change.
- Events that fail admission are rejected, quarantined, or kept pending and must
  not update authoritative state.

## Event Admission Examples

- `task.assigned`: Event Authority must come from the Company/Task policy path
  that produced an allowed PolicyDecision.
- `assignment.heartbeat.recorded`: Event Authority must come from the assigned
  executor or authorized adapter for the active AssignmentLease.
- `evidence.submitted`: Event Authority must come from the active assignment's
  executor.
- `artifact.access.grant.issued`: Event Authority must come from the Company or
  task authority allowed to approve access to the private artifact.
- `evidence.validated`: Event Authority must match the recorded
  ValidationAuthority.
- `review.outcome.recorded`: Event Authority must satisfy the ReviewRequest
  quorum, conflict, and governance rules.
- `budget.allocation.recorded`: Event Authority must come from the policy,
  budget, or governance authority allowed to reserve funds for that
  contribution kind.
- `settlement.line.recorded`: Event Authority must come from the settlement
  authority and reference accepted contribution evidence or outcome refs.
- `dispute.case.opened`: Event Authority must come from dispute policy and
  must prove bond, dispute-cost allocation, or DAO-governed waiver when
  blocking effects are requested.
- `dispute.bond.posted`: Event Authority must come from the party opening the
  blocking Dispute Case or an authorized payer for that party.
- `dispute.bond.waived`: Event Authority must come from DAO-governed authority.
- `dispute.resolver.decision.recorded`: Event Authority must come from a
  selected DisputeResolver on the active DisputeResolutionPanel.
- `dispute.outcome.recorded`: Event Authority must satisfy the Dispute
  Resolution Panel quorum and conflict rules.
- `settlement.recorded`: Event Authority must come from the policy/governance
  settlement authority for the task.
- `payment.execution.recorded`: Event Authority must come from the configured
  SolanaPaymentAdapter, signer, or DAO payment interface authority.

## Event Types

### `company.created`

Payload:
- `companyId`
- `type`
- `name`
- `ownerStakeholderId`
- `daoGovernanceRef`

### `agent.handshake.completed`

Payload:
- `agentId`
- `publicKey`
- `runtimeKind`
- `adapterVersion`
- `agentPolicyId`
- `agentPricingPolicyId`
- `capabilityClaims`: exact versioned CapabilityDefinition refs with
  definition hashes

### `agent.pricing.policy.versioned`

Payload:
- `agentPricingPolicyId`
- `agentId`
- `version`
- `pricingPolicyHash`
- `pricingUpdateMode`: `manual-only` or `bounded-auto`
- `updatedBy`: `owner`, `authorized-runtime`, or `governance-action`
- `runtimeProposalRef`: present when a local runtime proposed the update
- `pricingConfigurationGrantRef`: present when an authorized local runtime
  requested or applied the update
- `ownerAuthorizationRef`: present when required for authorized-runtime updates
- `effectiveFrom`
- `status`: `active`, `pending-owner-approval`, `superseded`, or `revoked`

Rules:
- Company actors cannot emit pricing policy versions for agents they do not
  own.
- Authorized-runtime updates must reference a valid Pricing Configuration
  Grant; Agent identity alone is not pricing authority.
- `pricingConfigurationGrantRef` is an opaque local/audit reference. Event
  payloads must not expose grant contents, delegated secret material, or private
  automation authority.
- Authorized-runtime updates must satisfy the owner-defined Pricing Update Rule
  before becoming active automatically.
- Runtime pricing proposals outside the rule remain pending owner approval and
  do not affect active quote generation.
- Events record the accepted or pending pricing configuration result, not the
  external runtime mechanism that triggered it.
- Event payloads must not include private prompt or model transcript details
  from runtime pricing analysis.

### `agent.pricing.configuration.grant.recorded`

Payload:
- `pricingConfigurationGrantId`
- `agentId`
- `ownerRef`
- `localRuntimeRef`
- `action`: `issued` or `revoked`
- `allowedActions`
- `pricingUpdateMode`
- `pricingUpdateRuleRef`
- `expiresAt`
- `status`: `active`, `revoked`, or `expired`

Rules:
- Only the agent owner can issue or revoke the grant.
- The grant authorizes local pricing configuration requests only; it does not
  authorize task execution or resource access.
- Grant-recorded events are local or authorized-audit events. Public P2P
  propagation must not expose grant contents.

### `capability.definition.versioned`

Payload:
- `capabilityDefinitionId`
- `namespaceId`
- `capabilityKey`
- `version`
- `status`
- `definitionHash`
- `authorityKind`: `protocol-source` or `company-governance`
- `protocolVersionRef`: present for `global:*`
- `sourceCommitRef`: present when available for `global:*`
- `governanceRef`: present for `company:{companyId}:*`

Rules:
- Company-scoped capability definition events are visible only through
  Company-authorized projections or encrypted/private references. Public P2P
  propagation must not reveal company-scoped capability vocabulary.

### `company.policy.versioned`

Payload:
- `companyPolicyId`
- `companyId`
- `version`
- `policyHash`
- `governanceRef`

### `task.proposed`

Payload:
- `taskProposalId`
- `requesterStakeholderId`
- `objectiveSummary`
- `taskCategory`
- `riskLevel`
- `capabilityIntents`: exact CapabilityDefinition refs or latest-active
  requests from the requester
- `evidenceRequirements`
- `reviewRequirements`
- `budgetLimit`
- `pricingMode`

### `task.policy.versioned`

Payload:
- `taskPolicyId`
- `taskProposalId`
- `taskId`
- `version`
- `requestedCapabilityRefs`: exact CapabilityRequirementRefs with definition
  hashes
- `quoteRequirements`
- `planningLimits`
- `policyHash`

### `task.preflight.completed`

Payload:
- `preflightCheckId`
- `taskProposalId`
- `candidateExecutorRef`: present when preflight evaluates a specific executor
- `result`: `eligible`, `ineligible`, `needs-clarification`,
  `needs-decomposition`, `pending-authorized-context`, or `quote-required`
- `reasons`
- `checkedAt`

### `execution.quote.submitted`

Payload:
- `executionQuoteId`
- `taskProposalId`
- `executorRef`
- `quoteKind`: `execution` or `planning`
- `priceAmount`
- `tokenRef`
- `scopeSummaryHash`
- `maxTokenSpend`
- `maxToolCalls`
- `maxWallClockTime`
- `maxDecompositionDepth`
- `maxSubtasks`
- `acceptedOutcomeKinds`
- `pricingPolicyVersionRef`
- `confidence`
- `validUntil`

### `execution.quote.accepted`

Payload:
- `executionQuoteId`
- `taskProposalId`
- `acceptedByStakeholderId`
- `acceptedAt`

### `budget.reserved`

Payload:
- `budgetReservationId`
- `executionQuoteId`
- `pricingPolicyVersionRef`: present when quote references Agent Pricing Policy
- `taskProposalId`
- `companyId`
- `amount`
- `tokenRef`
- `treasuryRef`
- `escrowRef`
- `budgetAllocationIds`
- `expiresAt`

### `budget.allocation.recorded`

Payload:
- `budgetAllocationId`
- `budgetReservationId`
- `taskId`
- `contributionKind`: `executor-reward`, `reviewer-fee`,
  `governance-validator-fee`, `dispute-resolver-fee`, `storage-fee`,
  `payment-execution-fee`, `dispute-cost`, `refund`, or `forfeit`
- `recipientClass`
- `recipientRef`
- `amount`
- `tokenRef`
- `policyRuleRef`
- `status`

Rules:
- Budget allocations are allowed only for policy-authorized Compensable
  Contributions or refund/forfeit handling.
- Baseline Node Validation must not create a Budget Allocation.

### `planning.task.created`

Payload:
- `planningTaskId`
- `parentTaskProposalId`
- `executionQuoteId`
- `budgetReservationId`
- `maxTokenSpend`
- `maxToolCalls`
- `maxDecompositionDepth`
- `maxSubtasks`
- `acceptedOutcomeKinds`

### `expiration.rule.applied`

Payload:
- `expirationRuleRef`
- `targetRef`
- `targetKind`: `quote`, `budget-reservation`, `assignment-lease`,
  `permission-grant`, `pricing-configuration-grant`, `planning-task`,
  `review-window`, or `artifact-access-grant`
- `onExpireAction`
- `appliedAt`
- `resultingState`

### `task.decomposition.recorded`

Payload:
- `planningTaskId`
- `parentTaskProposalId`
- `producedTaskProposalIds`
- `outcome`: `plan`, `needs-clarification`, `needs-next-planning-task`, or
  `unplannable-within-limits`
- `evidenceBundleId`

### `policy.decision.recorded`

Payload:
- `policyDecisionId`
- `taskProposalId`
- `companyPolicyVersionRefs`
- `taskPolicyVersionRef`
- `agentPolicyVersionRef`
- `resolvedCapabilityRefs`: exact CapabilityRequirementRefs evaluated,
  including any policy-declared alternative selected
- `decision`: `allow`, `deny`, `needs-review`, or `override-required`
- `reasons`
- `requiredActions`

### `task.assigned`

Payload:
- `taskId`
- `assignmentId`
- `assignmentLeaseId`
- `executorRef`
- `agentPolicyId`: present for agent executor
- `permissionGrantIds`: present for agent executor
- `policyDecisionId`
- `executionQuoteId`: present when quote is required
- `budgetReservationId`: present when budget reservation is required
- `leaseExpiresAt`
- `heartbeatInterval`

### `assignment.heartbeat.recorded`

Payload:
- `assignmentId`
- `assignmentLeaseId`
- `taskId`
- `executorRef`
- `progressEvidenceRef`
- `leaseExpiresAt`
- `heartbeatRecordedAt`

### `assignment.lease.expired`

Payload:
- `assignmentId`
- `assignmentLeaseId`
- `taskId`
- `executorRef`
- `expirationRuleRef`
- `nextAction`: `release`, `reassign`, `fail`, or `dispute`
- `expiredAt`

### `permission.grant.issued`

Payload:
- `permissionGrantId`
- `taskId`
- `agentId`
- `policyDecisionId`
- `scopes`
- `resourceRefs`
- `expiresAt`

### `artifact.access.grant.issued`

Payload:
- `artifactAccessGrantId`
- `artifactRefId`
- `taskId`
- `subjectRef`
- `purpose`
- `scopes`
- `approverRef`
- `expiresAt`
- `status`

Rules:
- Payloads must not include raw private artifact contents or decryption keys.
- Grant contents are visible only to authorized local or audit projections.

### `artifact.access.grant.revoked`

Payload:
- `artifactAccessGrantId`
- `artifactRefId`
- `taskId`
- `revokedByRef`
- `revokedAt`

### `artifact.access.recorded`

Payload:
- `artifactAccessGrantId`
- `artifactRefId`
- `taskId`
- `subjectRef`
- `purpose`
- `scopesUsed`
- `accessedAt`
- `accessResult`: `allowed`, `denied`, `expired`, or `revoked`

Rules:
- Access records must not include raw private artifact contents or decryption
  keys.

### `evidence.submitted`

Payload:
- `evidenceBundleId`
- `taskId`
- `assignmentId`
- `submittedByExecutorRef`
- `artifactRefs`
- `artifactAccessGrantRefs`
- `workspaceSnapshotRefs`
- `contentHashes`
- `cidRefs`
- `privacyClassification`

### `evidence.validated`

Payload:
- `validationResultId`
- `evidenceBundleId`
- `validationAuthorityRef`
- `authorityKind`: `mechanical-validator` or `governance-validator`
- `result`
- `checks`
- `missingFields`
- `privacyFindings`

### `dispute.case.opened`

Payload:
- `disputeCaseId`
- `taskId`
- `openedByRef`
- `disputedRecordRefs`
- `disputedRecordKinds`
- `reasonHash`
- `evidenceRefs`
- `evidenceDeadline`
- `resolutionDeadline`
- `resolverPoolRef`
- `disputeBondId`: present when a blocking dispute posts a bond
- `disputeCostAllocationId`: present when pre-reserved dispute cost covers the
  blocking dispute path
- `governanceDisputeWaiverRef`: present only when DAO-governed authority
  waives the bond requirement
- `blockingEffects`

Rules:
- A Dispute Case with blocking effects must reference an active Dispute Bond,
  pre-reserved dispute-cost Budget Allocation, or DAO-governed Governance
  Dispute Waiver before the blocking effects can apply.
- Private-company owners, executors, reviewers, payment controllers, agents,
  and ordinary nodes cannot waive the bond requirement by default.

### `dispute.bond.posted`

Payload:
- `disputeBondId`
- `disputeCaseId`
- `postedByRef`
- `amount`
- `tokenRef`
- `escrowRef`
- `requiredByPolicyRef`
- `postedAt`
- `status`: `posted`

Rules:
- Payloads must not reveal private dispute evidence.
- Posting a Dispute Bond is not Slashing and is not itself a resolver payment.

### `dispute.bond.waived`

Payload:
- `disputeCaseId`
- `governanceDisputeWaiverRef`
- `daoGovernanceRef`
- `reasonHash`
- `waivedAt`

Rules:
- Only DAO-governed authority can waive the Dispute Bond by default.
- Owner-only private-company authority cannot emit this waiver unless the
  company is operating through a DAO-governed authority path.

### `dispute.bond.released`

Payload:
- `disputeBondId`
- `disputeCaseId`
- `releaseKind`: `refund`, `forfeit`, or `release`
- `frivolousFinding`: present when releaseKind is `forfeit`
- `settlementLineId`: present when the release or forfeiture is itemized
- `releasedAt`

Rules:
- Forfeiture can pay only policy-authorized dispute costs, affected-party
  costs, or treasury recovery through Settlement Lines.
- Forfeiture is not agent Slashing.

### `dispute.panel.selected`

Payload:
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverPoolRef`
- `resolverRefs`
- `selectionProofRefs`
- `quorumRule`
- `conflictPolicy`
- `artifactAccessGrantRefs`

Rules:
- Resolver refs must exclude conflicted task participants.
- Private evidence access for resolvers must be represented through
  ArtifactAccessGrant refs, not raw content or keys.

### `dispute.resolver.decision.recorded`

Payload:
- `resolverDecisionId`
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverRef`
- `disputedRecordRefs`
- `decision`
- `rationaleSummaryHash`
- `evidenceRefs`
- `conflictDisclosure`
- `decidedAt`

Rules:
- Individual ResolverDecision does not finalize a blocking dispute.

### `dispute.outcome.recorded`

Payload:
- `disputeOutcomeId`
- `disputeCaseId`
- `disputeResolutionPanelId`
- `resolverDecisionIds`
- `outcome`: `upheld`, `reversed`, `needs-more-evidence`, `no-settlement`,
  `slash-eligible`, `reviewer-misconduct`, or `dismissed`
- `quorumResult`
- `conflictFindings`
- `frivolousFinding`: optional finding for abusive, unsupported, spammy, or
  bad-faith disputes
- `rationaleSummaryHash`
- `decidedAt`

### `dispute.case.resolved`

Payload:
- `disputeCaseId`
- `taskId`
- `disputeOutcomeId`
- `disputeResolutionPanelId`
- `resolverAuthorityRef`
- `outcome`
- `frivolousFinding`: optional finding for abusive, unsupported, spammy, or
  bad-faith disputes
- `rationaleSummaryHash`
- `resolvedAt`

### `review.requested`

Payload:
- `reviewRequestId`
- `taskId`
- `evidenceBundleId`
- `requiredReviewKind`
- `candidateReviewerStakeholderIds`
- `governanceRef`
- `quorumRule`
- `conflictPolicy`

### `review.decision.recorded`

Payload:
- `reviewDecisionId`
- `reviewRequestId`
- `taskId`
- `evidenceBundleId`
- `reviewerStakeholderId`
- `governanceRef`
- `decision`
- `conflictDisclosure`
- `rationaleSummary`

### `review.outcome.recorded`

Payload:
- `reviewOutcomeId`
- `reviewRequestId`
- `taskId`
- `evidenceBundleId`
- `decisionIds`
- `outcome`
- `quorumResult`
- `conflictFindings`
- `rationaleSummary`

### `reputation.signal.recorded`

Payload:
- `reputationSignalId`
- `agentId`
- `taskId`
- `domain`
- `impact`
- `scoreDelta`
- `privacyClassification`: `public-aggregate`, `authorized-detail`, or
  `private-company-detail`
- `evidenceBundleId`
- `validationResultId`
- `reviewOutcomeId`: present when review is required

### `reputation.projection.updated`

Payload:
- `reputationProjectionId`
- `agentId`
- `projectionKind`
- `capabilityRef`
- `companyId`: present only for authorized or private projections
- `aggregateScore`
- `commitmentHash`

### `settlement.recorded`

Payload:
- `settlementRecordId`
- `taskId`
- `executorRef`
- `budgetReservationId`: present when quote/budget reservation was required
- `disputeCaseId`: present when settlement depends on resolved dispute
- `outcome`
- `amount`
- `tokenRef`
- `settlementLineIds`
- `governanceRef`
- `treasuryRef`
- `reviewOutcomeId`: present when review is required
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`
- `paymentExecutionRecordId`
- `commitmentHash`
- `solanaSignatureRef`

### `settlement.line.recorded`

Payload:
- `settlementLineId`
- `settlementRecordId`
- `taskId`
- `budgetAllocationId`
- `contributionKind`: `executor-reward`, `reviewer-fee`,
  `governance-validator-fee`, `dispute-resolver-fee`, `storage-fee`,
  `payment-execution-fee`, `dispute-cost`, `refund`, or `forfeit`
- `recipientRef`
- `amount`
- `tokenRef`
- `basisRefs`
- `outcome`: `pay`, `refund`, `forfeit`, `no-pay`, or `disputed`

Rules:
- Settlement lines must reference accepted evidence, review, validation,
  dispute, service, payment, or governance records that justify the economic
  outcome.
- Baseline Node Validation must not produce a Settlement Line.

### `payment.execution.recorded`

Payload:
- `paymentExecutionRecordId`
- `settlementRecordId`
- `settlementLineIds`
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`
- `paymentInstructionRef`
- `amount`
- `tokenRef`
- `treasuryRef`
- `recipientRef`
- `status`: `prepared`, `submitted`, `confirmed`, `failed`, or `cancelled`
- `solanaSignatureRef`
- `failureReason`

### `event.admission.rejected`

Payload:
- `receivedEventId`
- `receivedEventType`
- `companyId`
- `senderNodeId`
- `issuerRef`
- `eventAuthorityRef`
- `reason`
- `quarantineRef`: present when the event is retained for audit.
- `rejectedAt`

Rules:
- This event is local or authorized-audit only.
- It must not expose private payload contents from the rejected event.

## Ordering Guarantees

- Any event that changes authoritative state must pass Event Admission Rules
  before ordering guarantees are applied to local projections.
- `permission.grant.issued` must follow `policy.decision.recorded` with
  `decision: allow`.
- `task.assigned` must follow `policy.decision.recorded` with `decision: allow`
  and reference required permission grants for agent executors and an
  AssignmentLease.
- `assignment.heartbeat.recorded` must follow `task.assigned`.
- `assignment.lease.expired` must follow `task.assigned` when heartbeat or
  progress evidence does not satisfy the active lease.
- Agent-executed tasks that spend model tokens or paid tools must follow
  `execution.quote.accepted` and `budget.reserved` before `task.assigned`.
- Agent-submitted quotes must reference an active Agent Pricing Policy version
  or an accepted Planning Task quote that authorized paid pricing analysis.
- `budget.allocation.recorded` must follow `budget.reserved` and policy rules
  that authorize the contribution kind.
- `planning.task.created` must follow accepted planning quote and active budget
  reservation.
- `expiration.rule.applied` must follow the target's `expiresAt` and must record
  the deterministic next action.
- `task.decomposition.recorded` must follow `planning.task.created` and its
  evidence path.
- Private or encrypted artifact access must follow an authorized
  ArtifactAccessGrant and record `artifact.access.recorded`.
- `evidence.submitted` must follow `task.assigned` and reference required
  ArtifactAccessGrants for private or encrypted artifacts.
- `review.requested` must follow `evidence.validated` when policy or
  validation requires review.
- `review.outcome.recorded` must follow required `review.decision.recorded`
  events or a DAO governance reference that satisfies the review request.
- `dispute.case.opened` may block reputation, settlement, payment execution,
  budget release, assignment, or review only for the disputed scope, and only
  after it references an active `dispute.bond.posted`, pre-reserved
  dispute-cost Budget Allocation, or DAO-governed `dispute.bond.waived`.
- Disputes against ReviewDecision or ReviewOutcome must select a conflict-free
  DisputeResolutionPanel before final resolution.
- `dispute.bond.released` must follow `dispute.case.resolved` or a governance
  correction that determines refund, forfeiture, or release.
- `dispute.resolver.decision.recorded` must follow `dispute.panel.selected`.
- `dispute.outcome.recorded` must follow enough ResolverDecision or governance
  references to satisfy the panel quorum rule.
- `dispute.case.resolved` must follow the dispute evidence window, resolution
  window, and DisputeOutcome.
- `reputation.signal.recorded` must follow passed validation, any required
  accepted ReviewOutcome, and any blocking DisputeCase resolution for
  agent-executed tasks.
- `settlement.recorded` must follow passed validation and any required accepted
  ReviewOutcome, and must follow reputation signal and blocking DisputeCase
  resolution when policy changes agent reputation.
- `settlement.line.recorded` must follow accepted `settlement.recorded` and
  must not pay Baseline Node Validation.
- `payment.execution.recorded` must follow accepted `settlement.recorded` and
  any payable `settlement.line.recorded` refs plus required DAO payment
  interface authorization when payment execution is enabled.
- `settlement.recorded` with `outcome: slash` is valid only for agent
  executors.

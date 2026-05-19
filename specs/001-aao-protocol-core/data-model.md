# Data Model: AAO Protocol Core

## Bounded Contexts

- **Company Registry**: Organization, Stakeholder, RoleAssignment.
- **Agent Registry**: Agent, AgentPolicyProfile, CapabilityClaim,
  EvidenceBackedCapability.
- **Policy & Governance**: Policy, PolicyRule, PolicyDecision,
  GovernanceReference.
- **Task Execution Graph**: TaskProposal, Task, Assignment, TaskDependency.
- **Evidence & Validation**: EvidenceBundle, ArtifactReference,
  ValidationResult.
- **Review**: ReviewRequest, ReviewDecision.
- **Reputation**: ReputationSignal, CapabilityScore.
- **Settlement**: SettlementRecord, SettlementCommitment.
- **Workspace & Storage**: Workspace, WorkspaceSnapshot, StorageObjectRef.
- **Secure Node**: NodeIdentity, PermissionGrant, SecretRef, SignerRef.
- **P2P Propagation**: Peer, NetworkEvent, InboxMessage, OutboxMessage.

## Entities

### Organization

Fields:
- `id`: stable organization id.
- `type`: `public-company`, `private-company`, or `hybrid-company`.
- `name`: display name.
- `ownerStakeholderId`: required for private and hybrid organizations.
- `daoGovernanceRef`: required for public and hybrid organizations using DAO
  governance.
- `status`: `draft`, `active`, `suspended`, `archived`.
- `createdAt`, `updatedAt`.

Relationships:
- Has many Stakeholders, Policies, Workspaces, Tasks, ReputationSignals, and
  SettlementRecords.

Validation:
- Public organizations require a DAO governance reference.
- Private organizations require an owner.
- Hybrid organizations require both ownership and DAO/partner governance rules.

### Stakeholder

Fields:
- `id`
- `organizationId`
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
- Has one active AgentPolicyProfile.
- Has many CapabilityClaims, EvidenceBackedCapabilities, Assignments, and
  ReputationSignals.

Validation:
- Agent cannot receive work until public key and policy profile are recorded.

### AgentPolicyProfile

Fields:
- `id`
- `agentId`
- `acceptedMessageTypes`
- `rejectedMessageTypes`
- `permissionLimits`
- `autonomyLimits`
- `acceptedTaskCategories`
- `rejectedTaskCategories`
- `declaredCapabilities`
- `effectiveFrom`
- `status`: `active`, `superseded`, `revoked`.

Validation:
- Assignment must use the active profile version at decision time.

### Policy

Fields:
- `id`
- `organizationId`
- `name`
- `scope`: organization, role, task category, risk level, or agent class.
- `approvalRule`
- `evidenceRule`
- `autonomyRule`
- `reviewRule`
- `rewardRule`
- `slashingRule`
- `version`
- `status`: `draft`, `active`, `superseded`, `revoked`.

Validation:
- Active policies must declare all six rule families.
- Changes create a new version; existing tasks keep their policy decision ref.

### TaskProposal

Fields:
- `id`
- `organizationId`
- `requesterStakeholderId`
- `objective`
- `taskCategory`
- `riskLevel`: `low`, `medium`, `high`, `critical`.
- `requestedCapabilities`
- `budgetLimit`
- `rewardExpectation`
- `evidenceRequirements`
- `reviewRequirements`
- `status`: `draft`, `submitted`, `blocked`, `approved`, `rejected`.

Relationships:
- Produces one or more PolicyDecisions.
- Converts to Task after approval.

### PolicyDecision

Fields:
- `id`
- `taskProposalId`
- `policyId`
- `policyVersion`
- `decision`: `approved`, `blocked`, `rejected`, `needs-review`.
- `reasons`
- `requiredActions`
- `decidedAt`

Validation:
- Assignment cannot exist without an approved PolicyDecision.

### Task

Fields:
- `id`
- `proposalId`
- `organizationId`
- `policyDecisionId`
- `state`: see Task State Machine.
- `createdAt`, `updatedAt`.

Relationships:
- Has Assignment, EvidenceBundle, ReviewRequest, ValidationResult,
  ReputationSignal, and SettlementRecord.

### Assignment

Fields:
- `id`
- `taskId`
- `agentId`
- `agentPolicyProfileId`
- `assignedAt`
- `status`: `active`, `cancelled`, `expired`, `completed`.

Validation:
- Agent profile must satisfy policy, permission, capability, and reputation
  constraints.

### EvidenceBundle

Fields:
- `id`
- `taskId`
- `assignmentId`
- `submittedByAgentId`
- `policyDecisionId`
- `artifactRefs`
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
- Must not expose sensitive raw content in public records.

### ArtifactReference

Fields:
- `id`
- `kind`: `cid`, `hash`, `commit`, `snapshot`, `external-url`,
  `encrypted-object`.
- `uriOrValue`
- `hash`
- `storageProvider`
- `privacyClassification`
- `createdAt`

### ValidationResult

Fields:
- `id`
- `evidenceBundleId`
- `result`: `passed`, `failed`, `needs-review`.
- `checks`
- `missingFields`
- `privacyFindings`
- `validatorIdentity`
- `validatedAt`

### ReviewDecision

Fields:
- `id`
- `taskId`
- `evidenceBundleId`
- `reviewerStakeholderId`
- `governanceRef`
- `decision`: `accepted`, `rejected`, `needs-more-evidence`, `disputed`.
- `rationaleSummary`
- `decidedAt`

Validation:
- Required review must complete before reputation or settlement changes.

### ReputationSignal

Fields:
- `id`
- `agentId`
- `organizationId`
- `taskId`
- `evidenceBundleId`
- `validationResultId`
- `reviewDecisionId`
- `domain`
- `impact`: `positive`, `neutral`, `negative`.
- `scoreDelta`
- `reason`
- `createdAt`

Validation:
- Must reference accepted evidence and any required review.

### SettlementRecord

Fields:
- `id`
- `taskId`
- `organizationId`
- `agentId`
- `policyDecisionId`
- `evidenceBundleId`
- `reviewDecisionId`
- `reputationSignalId`
- `outcome`: `reward`, `slash`, `no-settlement`, `disputed`.
- `amount`
- `tokenRef`
- `commitmentHash`
- `solanaSignatureRef`
- `createdAt`

Validation:
- Cannot be created before accepted validation path.

### Workspace

Fields:
- `id`
- `organizationId`
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
 -> blocked
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
proposed -> rejected
assigned -> failed
evidence-submitted -> rejected
needs-review -> disputed
accepted -> settlement-pending -> slashed
```

Rules:
- `approved` requires an approved PolicyDecision.
- `assigned` requires an eligible Agent and active AgentPolicyProfile.
- `evidence-submitted` requires EvidenceBundle.
- `accepted` requires passed ValidationResult and ReviewDecision when policy
  requires review.
- `settled` or `slashed` requires SettlementRecord.

## Derived Projections

- Organization policy dashboard.
- Agent eligibility and capability search.
- Task lifecycle board.
- Evidence validation queue.
- Review queue.
- Reputation and settlement ledger.
- P2P inbox/outbox health.

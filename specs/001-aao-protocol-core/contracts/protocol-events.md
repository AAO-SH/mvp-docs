# Contract: Protocol Events

AAO nodes exchange and persist immutable lifecycle events. Every event uses the
same envelope so storage, P2P, UI, and audit projections can consume it.

## Event Envelope

```json
{
  "eventId": "evt_...",
  "eventType": "task.proposed",
  "schemaVersion": "1.0.0",
  "organizationId": "org_...",
  "actorId": "stakeholder_or_agent_or_node_id",
  "causationId": "evt_...",
  "correlationId": "task_or_workflow_id",
  "occurredAt": "2026-05-19T00:00:00.000Z",
  "payloadHash": "sha256:...",
  "payload": {}
}
```

Rules:
- `eventId` is unique across the node.
- `schemaVersion` is required for every event type.
- `payloadHash` is computed over canonical payload JSON.
- Sensitive payload details must be replaced by privacy-safe references.

## Event Types

### `organization.created`

Payload:
- `organizationId`
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
- `agentPolicyProfileId`
- `declaredCapabilities`

### `task.proposed`

Payload:
- `taskProposalId`
- `requesterStakeholderId`
- `objectiveSummary`
- `taskCategory`
- `riskLevel`
- `requestedCapabilities`
- `evidenceRequirements`
- `reviewRequirements`
- `budgetLimit`

### `policy.decision.recorded`

Payload:
- `policyDecisionId`
- `taskProposalId`
- `policyId`
- `policyVersion`
- `decision`
- `reasons`
- `requiredActions`

### `task.assigned`

Payload:
- `taskId`
- `assignmentId`
- `agentId`
- `agentPolicyProfileId`
- `policyDecisionId`

### `evidence.submitted`

Payload:
- `evidenceBundleId`
- `taskId`
- `assignmentId`
- `artifactRefs`
- `workspaceSnapshotRefs`
- `contentHashes`
- `cidRefs`
- `privacyClassification`

### `evidence.validated`

Payload:
- `validationResultId`
- `evidenceBundleId`
- `result`
- `checks`
- `missingFields`
- `privacyFindings`

### `review.decision.recorded`

Payload:
- `reviewDecisionId`
- `taskId`
- `evidenceBundleId`
- `reviewerStakeholderId`
- `governanceRef`
- `decision`
- `rationaleSummary`

### `reputation.signal.recorded`

Payload:
- `reputationSignalId`
- `agentId`
- `taskId`
- `domain`
- `impact`
- `scoreDelta`
- `evidenceBundleId`
- `validationResultId`
- `reviewDecisionId`

### `settlement.recorded`

Payload:
- `settlementRecordId`
- `taskId`
- `agentId`
- `outcome`
- `amount`
- `tokenRef`
- `commitmentHash`
- `solanaSignatureRef`

## Ordering Guarantees

- `task.assigned` must follow approved `policy.decision.recorded`.
- `evidence.submitted` must follow `task.assigned`.
- `reputation.signal.recorded` must follow accepted validation/review path.
- `settlement.recorded` must follow reputation signal when policy changes
  reputation.

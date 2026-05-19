# Contract: MCP Tools and Resources

AAO exposes MCP tools to external AI runtimes through the Agent Adapter. Agents
are untrusted and can only act through these policy-bound surfaces.

## Tools

### `aao.agent.handshake`

Purpose: Register or refresh an agent identity and policy profile.

Input:
- `publicKey`
- `runtimeKind`
- `adapterVersion`
- `acceptedMessageTypes`
- `rejectedMessageTypes`
- `permissionLimits`
- `autonomyLimits`
- `declaredCapabilities`

Output:
- `agentId`
- `agentPolicyProfileId`
- `status`
- `rejectedReasons`

Rules:
- No task can be assigned until handshake is accepted.
- Profile changes create a new policy profile version.

### `aao.task.getAssignable`

Purpose: Let an agent inspect tasks it is eligible to execute.

Input:
- `agentId`
- `capabilityFilter`
- `maxRiskLevel`

Output:
- `tasks[]` with `taskId`, `objectiveSummary`, `riskLevel`,
  `evidenceRequirements`, and `policyDecisionId`.

Rules:
- Tool returns only tasks that satisfy current agent permissions and policy.

### `aao.task.acceptAssignment`

Purpose: Bind an eligible agent to an approved task.

Input:
- `agentId`
- `taskId`
- `agentPolicyProfileId`

Output:
- `assignmentId`
- `taskState`
- `evidenceRequirements`

Rules:
- Assignment fails if policy, profile, reputation, or capability no longer
  matches.

### `aao.evidence.submit`

Purpose: Submit artifact references and evidence after execution.

Input:
- `assignmentId`
- `artifactRefs`
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

### `aao.task.status`

Purpose: Return lifecycle state and next action for a task.

Input:
- `taskId`
- `requesterId`

Output:
- `state`
- `nextRequiredAction`
- `responsibleParty`
- `evidenceBundleId`
- `reviewDecisionId`
- `settlementRecordId`

## Resources

### `aao://task/{taskId}`

Read-only task lifecycle state, policy decision, assignment, evidence refs,
review state, reputation impact, and settlement ref.

### `aao://agent/{agentId}`

Read-only agent policy profile, declared capabilities, evidence-backed
capabilities, and reputation signals visible to the requester.

### `aao://organization/{organizationId}/policies`

Read-only active policy versions and rule summaries visible to the caller.

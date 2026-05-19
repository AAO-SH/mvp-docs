# Contract: Operator UI State

The operator UI must use the same lifecycle language as the domain model,
evidence bundles, logs, and audit records.

## Task Lifecycle States

| State | Meaning | User Action |
|-------|---------|-------------|
| `proposed` | Task has been submitted for policy check | Wait or inspect proposal |
| `blocked` | Policy prevents progress until action occurs | Resolve required action |
| `approved` | Policy allows assignment | Assign or wait for eligible agent |
| `assigned` | Agent is bound to the task | Monitor execution |
| `running` | Agent execution is underway | Monitor evidence requirements |
| `evidence-submitted` | Agent submitted evidence bundle | Validate evidence |
| `needs-review` | Human or DAO review is required | Review evidence |
| `validated` | Evidence passed validation | Accept or route to settlement |
| `accepted` | Task outcome accepted | Prepare settlement |
| `rejected` | Task outcome rejected | Request new work or slash if policy says |
| `settlement-pending` | Settlement commitment is being prepared | Sign or wait |
| `settled` | Reward/no-settlement outcome recorded | Inspect audit trail |
| `slashed` | Penalty outcome recorded | Inspect audit trail |
| `failed` | Agent or workflow failed | Reassign, cancel, or review |

## Required UI Fields

Every task detail view shows:
- task id and objective summary;
- organization and governance model;
- current state;
- next required action;
- responsible party;
- policy decision;
- assigned agent;
- evidence bundle reference;
- validation result;
- review decision when present;
- reputation impact when present;
- settlement commitment when present.

## Consistency Rules

- UI labels must match domain state names or documented human-readable aliases.
- Error messages must include the policy or evidence reason where available.
- Review screens must show evidence references and privacy classification.
- Settlement screens must distinguish commitment recording from token transfer.
- Agent pages must separate declared capability from evidence-backed capability.

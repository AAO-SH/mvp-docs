# Contract: P2P Messages

AAO uses libp2p to propagate protocol events, peer capability summaries, review
signals, quote lifecycle events, and evidence availability notices. P2P
messages carry references and commitments, not raw private company data.

## Topics

- `/aao/v1/events`: lifecycle event announcements.
- `/aao/v1/evidence`: evidence bundle availability notices.
- `/aao/v1/reputation`: reputation signal announcements.
- `/aao/v1/peer-capabilities`: agent and node capability summaries.
- `/aao/v1/review`: review request and decision availability notices.
- `/aao/v1/quotes`: quote, budget reservation, and planning availability
  notices.
- `/aao/v1/assignments`: assignment lease, heartbeat, and lease expiry notices.
- `/aao/v1/disputes`: dispute case availability and resolution notices.
- `/aao/v1/payments`: payment execution status notices.
- `/aao/v1/agent-pricing`: privacy-safe Agent Pricing Policy version
  announcements visible to authorized peers.

## Message Envelope

```json
{
  "messageId": "msg_...",
  "topic": "/aao/v1/events",
  "schemaVersion": "1.0.0",
  "senderNodeId": "node_...",
  "companyId": "cmp_...",
  "eventId": "evt_...",
  "eventIssuerRef": "stakeholder_or_agent_or_node_or_adapter_id",
  "eventAuthorityRef": "event_authority_...",
  "payloadCid": "bafy...",
  "payloadHash": "sha256:...",
  "signature": "base64-signature",
  "sentAt": "2026-05-19T00:00:00.000Z"
}
```

Rules:
- Nodes dedupe by `messageId`, `eventId`, and `payloadHash`.
- Nodes verify sender identity and signature before accepting message.
- `senderNodeId` identifies the peer that sent or relayed the message. It does
  not by itself identify the lifecycle event issuer.
- For lifecycle event payloads, nodes must also verify the embedded
  issuer signature, Event Authority, and local Event Admission Rule before the
  event affects state.
- Nodes fetch payloads through IPFS only when local policy permits.
- Messages with missing or mismatched hashes are rejected.
- Quote and planning payloads must not include raw private task details when
  propagated publicly; use privacy-safe summaries, hashes, CIDs, or encrypted
  references.
- Quote payloads for agent executors reference the Agent Pricing Policy version
  or hash used, but do not expose private pricing strategy beyond authorized
  projections.
- Agent Pricing Policy update requests are local configuration actions, not
  public P2P gossip. P2P may announce accepted or pending version refs only when
  an authorized projection needs them.
- Pricing Configuration Grants are local-only and must not be included in peer
  handshakes, public gossip topics, or unauthenticated lookup responses. P2P may
  carry only opaque refs, hashes, versions, or status when an authorized audit
  path requires them.
- Assignment lease and heartbeat messages must be small, signed, and
  privacy-safe. Missing heartbeat or progress evidence can release or reassign
  task availability without exposing private work contents.
- Artifact Access Grant contents must not be gossiped. P2P may announce only
  opaque grant refs or hashes to authorized audit projections.
- Public reputation messages must carry Reputation Projection commitments or
  aggregate values, not raw private task history.
- Payment messages carry Settlement Line refs, Payment Execution Record status,
  and Solana signature refs, not private evidence or workspace data.
- Baseline Node Validation is not announced as a paid contribution.
- Dispute messages may carry Dispute Bond refs, dispute-cost Budget Allocation
  refs, DAO-governed waiver refs, and bond status, but not private evidence,
  escrow secrets, or grant contents.

## Direct Protocols

### `/aao/v1/request-event`

Request a specific event by `eventId`.

### `/aao/v1/request-evidence`

Request an evidence bundle manifest by `evidenceBundleId`.

### `/aao/v1/request-assignment-lease`

Request assignment lease status by `assignmentLeaseId`.

Rules:
- Replies include lease status, expiry, and heartbeat summary only.
- Replies must not include private task contents or runtime transcripts.

### `/aao/v1/request-dispute-case`

Request a Dispute Case summary by `disputeCaseId`.

Rules:
- Replies include disputed refs, deadlines, blocking effects, and outcome
  status only.
- Replies may include Dispute Bond status, dispute-cost Budget Allocation refs,
  and DAO-governed waiver refs when the requester is authorized to see them.
- Replies may include Resolver Pool, Dispute Resolution Panel, ResolverDecision,
  and DisputeOutcome refs when the requester is authorized to see them.
- Private evidence details require authorized Artifact Access Grants.

### `/aao/v1/request-payment-execution`

Request payment execution status by `paymentExecutionRecordId`.

Rules:
- Replies include adapter refs, status, and Solana signature refs only.
- Payment status replies must not expose private evidence or grant contents.

### `/aao/v1/request-agent-pricing-policy`

Request a privacy-safe Agent Pricing Policy summary by `agentId` and
`pricingPolicyVersionRef`.

Rules:
- Only authorized local or peer contexts may receive detailed pricing policy
  summaries.
- Public replies may include only version refs, hashes, and availability status.
- Private runtime proposal details must not be gossiped.
- Pricing Configuration Grant contents must not be returned over this protocol.
- The direct protocol does not define how a local runtime decides to request a
  pricing update.

### `/aao/v1/peer-handshake`

Exchange node identity, supported topics, protocol version, and public
capability summary.

Payload:
- `nodeId`
- `protocolVersion`
- `supportedSchemaVersions`
- `supportedTopics`
- `supportedGlobalCapabilityRefs`: exact global CapabilityDefinition refs with
  definition hashes
- `globalCapabilityManifestHash`
- `globalCapabilityManifestCid`: optional, when the full manifest is too large
  for the handshake payload

Rules:
- Capability summaries carry versioned CapabilityDefinition refs and definition
  hashes so peers do not compare agents against mutable capability labels.
- supportedGlobalCapabilityRefs includes only `global:*` definitions. It must
  not include `company:{companyId}:*` definitions.
- Peers compute global capability compatibility from the intersection of
  supportedGlobalCapabilityRefs, not from protocolVersion alone.
- globalCapabilityManifestHash is computed over canonical
  supportedGlobalCapabilityRefs sorted by namespace, capability key, version,
  and definition hash.
- Global CapabilityDefinition versions are accepted only when the receiving
  peer advertises support for them; otherwise peers report `upgrade-required` or
  `unsupported-capability` for that capability path while continuing compatible
  message exchange.
- Company-scoped definitions are discovered through Company-authorized flows,
  not through public peer handshake.
- Public P2P compatibility checks do not resolve company-scoped capability
  intents. Company-scoped resolution uses authorized local Company context and
  may remain pending until encrypted or permissioned Company state arrives.

## Failure Handling

- Invalid signature: reject and record peer warning.
- Invalid or missing Event Authority: reject, quarantine, or keep pending
  according to the Event Admission Rule; do not update local projections.
- Missing payload: keep message in pending-fetch queue.
- Privacy policy violation: reject payload and do not propagate.
- Duplicate message: ignore payload but update peer health metrics.
- Missing assignment heartbeat: apply AssignmentLease expiry handling rather
  than letting an executor hold availability indefinitely.
- Unsupported schema version: reject with upgrade-required reason.
- Unsupported global CapabilityDefinition version: reject or keep only the
  dependent task, claim, proof, or matching path pending until protocol upgrade;
  keep compatible peer traffic active.
- Missing authorized Company context: return `pending-authorized-context` or
  opaque `not-found-in-authorized-scope`; do not ask public peers whether the
  company-scoped CapabilityDefinition exists.

# Contract: P2P Messages

AAO uses libp2p to propagate protocol events, peer capability summaries, review
signals, and evidence availability notices. P2P messages carry references and
commitments, not raw private company data.

## Topics

- `/aao/v1/events`: lifecycle event announcements.
- `/aao/v1/evidence`: evidence bundle availability notices.
- `/aao/v1/reputation`: reputation signal announcements.
- `/aao/v1/peer-capabilities`: agent and node capability summaries.
- `/aao/v1/review`: review request and decision availability notices.

## Message Envelope

```json
{
  "messageId": "msg_...",
  "topic": "/aao/v1/events",
  "schemaVersion": "1.0.0",
  "senderNodeId": "node_...",
  "organizationId": "org_...",
  "eventId": "evt_...",
  "payloadCid": "bafy...",
  "payloadHash": "sha256:...",
  "signature": "base64-signature",
  "sentAt": "2026-05-19T00:00:00.000Z"
}
```

Rules:
- Nodes dedupe by `messageId`, `eventId`, and `payloadHash`.
- Nodes verify sender identity and signature before accepting message.
- Nodes fetch payloads through IPFS only when local policy permits.
- Messages with missing or mismatched hashes are rejected.

## Direct Protocols

### `/aao/v1/request-event`

Request a specific event by `eventId`.

### `/aao/v1/request-evidence`

Request an evidence bundle manifest by `evidenceBundleId`.

### `/aao/v1/peer-handshake`

Exchange node identity, supported topics, protocol version, and public
capability summary.

## Failure Handling

- Invalid signature: reject and record peer warning.
- Missing payload: keep message in pending-fetch queue.
- Privacy policy violation: reject payload and do not propagate.
- Duplicate message: ignore payload but update peer health metrics.
- Unsupported schema version: reject with upgrade-required reason.

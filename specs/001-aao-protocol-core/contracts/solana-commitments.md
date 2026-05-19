# Contract: Solana and Realms Commitments

Solana records commitments and economic outcomes. Detailed evidence and private
workspace data remain off-chain.

## Commitment Types

### Policy Commitment

Fields:
- `organizationId`
- `policyId`
- `policyVersion`
- `policyHash`
- `governanceRef`
- `createdAt`

### Evidence Commitment

Fields:
- `taskId`
- `evidenceBundleId`
- `evidenceHash`
- `evidenceCid`
- `privacyClassification`
- `createdAt`

### Review Commitment

Fields:
- `taskId`
- `reviewDecisionId`
- `decision`
- `reviewHash`
- `governanceRef`
- `createdAt`

### Reputation Commitment

Fields:
- `agentId`
- `taskId`
- `reputationSignalId`
- `impact`
- `scoreDelta`
- `evidenceBundleId`
- `createdAt`

### Settlement Commitment

Fields:
- `settlementRecordId`
- `taskId`
- `agentId`
- `outcome`
- `amount`
- `tokenRef`
- `evidenceBundleId`
- `reviewDecisionId`
- `commitmentHash`
- `createdAt`

## Realms Governance Reference

For DAO-governed public and hybrid organizations, AAO records:
- `realmId`
- `governanceAccount`
- `proposalId`
- `voteRecordRefs`
- `proposalState`
- `decisionHash`

Rules:
- AAO treats Realms proposal/vote state as governance input evidence.
- AAO does not duplicate DAO voting mechanics in the first slice.
- Settlement cannot be recorded when required Realms approval is absent.

## Privacy Rules

- No raw private artifacts on-chain.
- No secrets, auth tokens, private keys, or workspace contents on-chain.
- Hashes and CIDs must reference canonical off-chain evidence manifests.
- If evidence is encrypted, commitment references the encrypted object and the
  decryption authority policy, not the key.

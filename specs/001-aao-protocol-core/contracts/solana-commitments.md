# Contract: Solana and Realms Commitments

Solana records commitments and economic outcomes. Detailed evidence and private
workspace data remain off-chain.

## Commitment Types

### Policy Commitment

Fields:
- `policyKind`: `company-policy`, `task-policy`, `agent-policy`, or
  `agent-pricing-policy`
- `companyId`: required for company and task policy commitments
- `taskId`: required for task policy commitments
- `agentId`: required for agent and agent-pricing policy commitments
- `policyId`
- `policyVersion`
- `policyHash`
- `governanceRef`
- `createdAt`

Rules:
- Task policy commitments include the hash of resolved CapabilityRequirementRefs,
  including Capability Definition ids, versions, namespaces, keys, and
  definition hashes.
- Solana or DAO governance references do not define `global:*`
  CapabilityDefinitions; global definitions are protocol source/version
  artifacts. Company-scoped definitions may include Company governance refs.

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
- `reviewRequestId`
- `reviewOutcomeId`
- `outcome`
- `reviewHash`
- `governanceRef`
- `createdAt`

### Dispute Bond Commitment

Fields:
- `disputeBondId`
- `disputeCaseId`
- `postedByRef`
- `amount`
- `tokenRef`
- `escrowRef`
- `status`: `posted`, `refunded`, `forfeited`, `released`, or `disputed`
- `frivolousFindingHash`: present when forfeiture is based on a frivolous,
  abusive, unsupported, spammy, or bad-faith dispute
- `commitmentHash`
- `createdAt`

Rules:
- Dispute Bond commitments prove only economic guardrails and status, not
  private dispute evidence.
- Private-company owner authority does not create a default bondless dispute
  path.

### Governance Dispute Waiver Commitment

Fields:
- `governanceDisputeWaiverRef`
- `disputeCaseId`
- `daoGovernanceRef`
- `reasonHash`
- `commitmentHash`
- `createdAt`

Rules:
- DAO-governed waiver commitments may replace bond commitments for blocking
  disputes when policy permits.
- Private-company owner authority does not create a default waiver commitment.

### Reputation Commitment

Fields:
- `agentId`
- `taskId`
- `reputationSignalId`
- `reputationProjectionId`
- `impact`
- `scoreDelta`
- `evidenceBundleId`
- `createdAt`

Rules:
- Public reputation commitments must reference privacy-safe Reputation
  Projections or aggregate commitments, not raw private task history.

### Budget Reservation Commitment

Fields:
- `budgetReservationId`
- `executionQuoteId`
- `pricingPolicyVersionRef`
- `pricingPolicyHash`
- `taskProposalId`
- `companyId`
- `amount`
- `tokenRef`
- `treasuryRef`
- `escrowRef`
- `expiresAt`
- `commitmentHash`
- `createdAt`

Rules:
- Budget reservation commitments prove that quoted work was funded before
  assignment when policy requires token or paid-tool spend protection.
- Budget reservation commitments record which Agent Pricing Policy version was
  used for the accepted quote when the executor is an agent.
- Private quote details remain off-chain; on-chain commitments reference hashes
  and economic bounds only.

### Settlement Commitment

Fields:
- `settlementRecordId`
- `taskId`
- `executorRef`
- `budgetReservationId`
- `disputeCaseId`
- `outcome`
- `amount`
- `tokenRef`
- `settlementLineCommitmentRefs`
- `evidenceBundleId`
- `reviewOutcomeId`
- `disputeOutcomeId`: present when settlement depends on resolved dispute.
- `governanceRef`
- `treasuryRef`
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`
- `paymentExecutionRecordId`
- `commitmentHash`
- `createdAt`

Rules:
- Settlement commitments record the authorized economic outcome.
- Settlement line commitments itemize executor reward, reviewer fee, governance
  validator fee, dispute resolver fee, storage cost, payment execution cost,
  dispute cost, refund, or forfeit when policy authorizes those contribution
  kinds.
- Baseline Node Validation must not create settlement line commitments.
- Settlement commitments do not prove that real payment execution has completed
  unless a confirmed Payment Execution Commitment is also present.
- Blocking Dispute Cases must be resolved before final settlement or payment
  execution commitments.

### Payment Execution Commitment

Fields:
- `paymentExecutionRecordId`
- `settlementRecordId`
- `settlementLineCommitmentRefs`
- `solanaPaymentAdapterRef`
- `daoPaymentInterfaceRef`
- `paymentInstructionHash`
- `amount`
- `tokenRef`
- `treasuryRef`
- `recipientRef`
- `status`: `prepared`, `submitted`, `confirmed`, `failed`, or `cancelled`
- `solanaSignatureRef`
- `createdAt`
- `confirmedAt`

Rules:
- Payment execution commitments are produced by a modular Solana Payment
  Adapter after Settlement is authorized.
- The default Solana Payment Adapter listens to and consumes the configured DAO
  Payment Interface for DAO-governed treasury flows.
- Payment execution failures do not rewrite Settlement, Evidence, Review, or
  Reputation commitments; they require retry, cancellation, or governance
  action.

## Realms Governance Reference

For DAO-governed public and hybrid companies, AAO records:
- `realmId`
- `governanceAccount`
- `proposalId`
- `voteRecordRefs`
- `proposalState`
- `decisionHash`
- `daoPaymentInterfaceRef`
- `treasuryPaymentInstructionRefs`

Rules:
- AAO treats Realms proposal/vote state as governance input evidence.
- AAO does not duplicate DAO voting mechanics in the first slice.
- AAO consumes the configured DAO Payment Interface as the default treasury
  payment input for DAO-governed companies.
- Settlement cannot be recorded when required Realms approval is absent.

## Privacy Rules

- No raw private artifacts on-chain.
- No secrets, auth tokens, private keys, or workspace contents on-chain.
- Hashes and CIDs must reference canonical off-chain evidence manifests.
- If evidence is encrypted, commitment references the encrypted object and the
  decryption authority policy, not the key.
- Artifact Access Grant contents, decryption keys, private reputation details,
  and private dispute evidence must stay off-chain.

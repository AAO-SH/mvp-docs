# State

Last updated: 2026-05-23.

## Project Reference

- Project: AAO Protocol
- Project memory: `.planning/PROJECT.md`
- Requirements: `.planning/REQUIREMENTS.md`
- Roadmap: `.planning/ROADMAP.md`
- Conflict report: `.planning/INGEST-CONFLICTS.md`

## Current Position

- Current phase: Phase 1, Protocol Foundation and Safety Gates.
- Implementation status: not started from GSD roadmap.
- Planning status: Speckit and Grill with Docs source set ingested.
- Known blockers: none.

## Active Constraints

- Preserve constitutional gates for evidence-first execution, policy-driven autonomy,
  verifiable reputation, human plus AI governance, secure node boundaries, and Solana
  settlement with off-chain private data.
- Keep domain code independent from frameworks, network, storage, Solana, UI, and runtime
  adapters.
- Use strict TypeScript, Clean Architecture, DDD bounded contexts, and Jest BDD/TDD.
- Treat ADRs 0001 through 0004 as locked until explicitly superseded.

## Accumulated Context

- The foundation spec intentionally remains in one large `001-aao-protocol-core` feature.
- Capability terminology is resolved: Company Capability Key is a company-scoped executor
  capability term, not a capability performed by the Company.
- Agent execution must be quote-gated and budgeted before token spend.
- Local runtime pricing automation is allowed only through local configuration grants and
  update rules.
- Assignment Leases protect against claimed work that never delivers.
- Baseline node validation is mandatory but unpaid; specialized, authorized, evidenced
  contributions can be paid.
- Disputes provide a way to challenge dishonest or mistaken reviewers through
  conflict-free third parties.

## Suggested Next Action

Start Phase 1 by generating implementation tasks from `.planning/ROADMAP.md` and the
current Speckit plan. The first task group should establish the monorepo skeleton,
domain package boundaries, Jest harness, event authority/admission primitives, and
security/performance gates.

## Open Items

- Decide later whether to convert `research.md` decisions into additional ADRs.
- Decide later whether to split the foundation spec after Phase 1 or Phase 2 experience.
- Optional local maintenance: the global `gsd-sdk` command appears stale compared with
  the local GSD install.

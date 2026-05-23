# Ingest Conflict Report

Generated from the approved Speckit, Grill with Docs, and ADR source set.

## Blockers

None.

## Warnings

None.

## Informational Resolutions

### ADR precedence

ADRs 0001 through 0004 lock event authority, independent dispute resolution,
compensable contribution rules, and dispute-bond behavior. Later planning should not
reinterpret those decisions without a new ADR.

### Research decisions are accepted design input

`specs/001-aao-protocol-core/research.md` contains decisions that are not all promoted
to ADRs. During ingest they are treated as accepted design input, with lower precedence
than ADRs and `CONTEXT.md`.

### Single foundation spec is intentional

The `001-aao-protocol-core` specification is large and covers several bounded contexts.
The user chose to keep it as one foundation spec for now. GSD planning should phase the
work instead of splitting the spec during ingest.

### Tooling docs excluded

Internal GSD installer files and `.codex/get-shit-done` workflow documentation were not
ingested as product requirements. They guide this ingestion workflow but are not AAO
Protocol source-of-truth documents.

# CONTRIBUTING.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Contribution scope
Contributions to SUAS-specs should improve specification clarity, resolve contradictions, add validated detail, or formally record open decisions.

## Contribution process
1. identify affected documents
2. propose edits with epistemic labels where needed
3. update cross-references
4. update `DECISIONS.md` for any new or resolved `DECISION_PENDING` item
5. update `STATUS.md` and `CHANGELOG.md` when release-relevant
6. run repository validation checks

## Review standards
- no unsupported claims
- no invented partner agreements
- no claims about VA or Medi-Cal eligibility without verified authority
- no clinical or legal conclusions without explicit review basis
- preserve canonical state names and loop vocabulary

## Decision record process
Architecture or policy choices that remain unresolved should be added to `DECISIONS.md` as new ADR entries. Resolved items should note status, rationale, and implications for impacted files.

## Documentation style
Every file should use the standard bootstrap header and include related-spec links. Use `OBSERVED`, `INFERRED`, `SPECULATIVE`, and `NOT_COMPUTABLE` labels where they materially clarify certainty.

## Non-goals
This repository does not accept production code as part of the bootstrap specification run.

Related specs: [CHANGELOG.md](CHANGELOG.md), [VERSIONING.md](VERSIONING.md), [DECISIONS.md](DECISIONS.md), [SPEC_AUDIT.md](SPEC_AUDIT.md).

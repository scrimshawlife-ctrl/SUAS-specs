# VERSIONING.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Versioning policy
SUAS specifications use semantic versioning.

## Semver meaning
- `MAJOR`: breaking conceptual or normative changes.
- `MINOR`: additive spec content or clarified requirements without breaking previously compliant implementations.
- `PATCH`: editorial fixes, typo corrections, or non-normative clarifications.

## Current version
`0.1.0` represents the initial specification bootstrap. [INFERRED] Pre-1.0 releases remain subject to substantial revision as decisions are resolved.

## Release process
1. propose change
2. review affected documents
3. update `DECISIONS.md` if unresolved choices change
4. update `STATUS.md`
5. record release in `CHANGELOG.md`
6. tag a spec release

## Canonical authority
SUAS-specs is the canonical specification authority. Implementations must conform to released specs and should not outrun unresolved normative constraints.

## Compatibility note
If an implementation diverges from the released spec, the divergence must be resolved by either bringing implementation back into conformance or releasing an updated spec version.

Related specs: [CHANGELOG.md](CHANGELOG.md), [STATUS.md](STATUS.md), [CONTRIBUTING.md](CONTRIBUTING.md), [README.md](README.md).

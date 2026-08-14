# SETTLEMENT.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Settlement purpose
`Settlement` records why a support episode is complete enough to move from active coordination into closure. In MVP, billing is `FUTURE`; settlement here means operational closure, not financial adjudication.

## Criteria
Settlement should consider:
- whether all relevant service requests reached terminal states
- whether follow-up obligations are complete
- whether the veteran confirmed completion when policy requires it
- whether unresolved risks have documented handling paths

## State relationship
A case should move `RESOLVED` when operational objectives are met and `CLOSED` when settlement checks and closure documentation are complete. Service requests should usually be `CLOSED`, `CANCELLED`, `DECLINED`, `EXPIRED`, or `UNFULFILLABLE` before final case closure.

## Veteran confirmation
Veteran confirmation is preferred when available and consent-compatible, but lack of confirmation alone may not always block closure if policy defines alternate closure evidence. [DECISION_PENDING via ADR-028]

## Data retention after settlement
Post-settlement records remain subject to retention policy from [PRIVACY.md](PRIVACY.md). Closure should reduce active visibility while preserving auditability.

## Constraints
- settlement is not benefits adjudication
- settlement is not insurance billing
- settlement does not imply long-term outcome success

## Future scope
[SPECULATIVE] Later releases may introduce financial reconciliation, but that is out of MVP scope.

Related specs: [FOLLOWUP.md](FOLLOWUP.md), [CASES.md](CASES.md), [PRIVACY.md](PRIVACY.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).

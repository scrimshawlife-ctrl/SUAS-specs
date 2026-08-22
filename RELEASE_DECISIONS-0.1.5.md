# RELEASE_DECISIONS-0.1.5.md — D-012 safety/crisis copy decision ledger

**Release:** `0.1.5`
**Owner:** `@scrimshawlife-ctrl`
**Owner action date:** `2026-08-22` PT
**Supersedes for D-012 only:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) D-012 boundary
**Base decision ledgers:** [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) for all other D-001–D-025 entries
**Target:** implementation-authoritative specification release; **not** production-operating approval
**Production readiness:** `NOT_READY`

This ledger closes D-012 by approving the on-screen safety/crisis copy and crisis destinations released in [SAFETY_COPY.md](SAFETY_COPY.md), governed by [SAFETY.md](SAFETY.md). It approves **wording and destinations only**. It does not authorize production deployment, real veteran data, real notification/dispatch effects, Support Signal thresholds (D-011), or any automated emergency dispatch.

| ID | Release status | v0.1.5 boundary |
|---|---|---|
| D-012 | `DECIDED` | The approved on-screen safety/crisis copy is [SAFETY_COPY.md](SAFETY_COPY.md). The only authorized crisis destinations are `911` (immediate danger / medical emergency) and the `988` Suicide & Crisis Lifeline (call or text; Veterans reach the Veterans Crisis Line through `988`). Implementations render this copy where [SAFETY.md](SAFETY.md) requires approved copy and MUST NOT invent alternative crisis wording, numbers, URLs, or agency contacts presented as official. SUAS remains a coordination platform and performs no automated emergency dispatch, diagnosis, or suicidality determination. Copy approval is not production-operating approval; real operation with real veteran data remains gated by SPEC-018 and [ENVIRONMENT.md](ENVIRONMENT.md). |

## Consequences

1. Implementations may render the D-012 approved copy from [SAFETY_COPY.md](SAFETY_COPY.md) on the crisis-entry, triage, red-state, request-status, provider, ride/lodging/food, trusted-contact, cancel, banner, footer, and operator surfaces. `SUAS_SAFETY_COPY_MODE=approved` selects it ([ENVIRONMENT.md](ENVIRONMENT.md) §3).
2. The crisis destinations are exactly `911` and `988`. No other hotline number, URL, or external agency contact may ship as official without a further released decision.
3. Canonical domain state machines, consent rules, provider-neutral fulfillment semantics, and the no-automated-dispatch / no-diagnosis / no-suicidality-prediction non-goals do not change. Trusted-contact alerts still require an `ACTIVE` `can_receive`+`RED` grant ([SAFETY.md](SAFETY.md) §3.3, [CONSENT.md](CONSENT.md)).
4. Crisis and practical-support surfaces must obey the state-truthfulness principle in [SAFETY_COPY.md](SAFETY_COPY.md) §5: `REQUESTED ≠ ACCEPTED ≠ DISPATCHED ≠ ARRIVED ≠ RESOLVED` are distinct domain conditions, surfaced only from recorded facts, mapped onto the canonical Service Request / Fulfillment states and the [MVP_REFERENCE.md](MVP_REFERENCE.md) §7.2 truthfulness table. `DISPATCHED`/`ARRIVED` describe a verified practical-support provider only and never emergency-services dispatch.
5. The approved/forbidden phrase lists in [SAFETY_COPY.md](SAFETY_COPY.md) §4 bind every crisis surface; forbidden phrases (e.g. "Help is on the way", "You are safe now", "Emergency team dispatched") must not appear unless the specific verified fact exists.
6. An emergency referral alone does not make a request `RESOLVED`, and safety is not inferred; operator surfaces record `ESCALATION RECORDED` / `SAFETY STATUS UNKNOWN` truthfully ([SAFETY_COPY.md](SAFETY_COPY.md) §3).
7. Production use still requires SPEC-018 readiness evidence, environment authorization, and every applicable still-open decision closure. D-011 Support Signal thresholds remain `DECISION_PENDING`.

## Unchanged release-wide safety boundary

v0.1.5 remains implementation-authoritative but not production-operating. It does **not** authorize:

- production deployment;
- operation with real veteran data;
- a live controlled pilot;
- automated emergency dispatch or any real notification of public-safety agencies;
- production Support Signal thresholds;
- compliance claims;
- production capacity/SLO/RTO/RPO claims;
- small-cell aggregate reporting.

Those surfaces require SPEC-017 implementation conformance plus SPEC-018 readiness evidence and every applicable still-open decision closure.

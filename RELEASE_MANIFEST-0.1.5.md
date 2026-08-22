# RELEASE_MANIFEST-0.1.5.md — SUAS D-012 safety/crisis copy decision release

**Release version:** `0.1.5`
**Release date:** `2026-08-22` PT
**Owner:** `@scrimshawlife-ctrl`
**Supersedes:** `0.1.4`
**Base release:** [RELEASE_MANIFEST-0.1.4.md](RELEASE_MANIFEST-0.1.4.md)
**Decision ledgers:** [RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md) for D-012; [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise unchanged
**Lifecycle:** `released` (ratified by owner merge of this patch)
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.5 is a decision-only patch for **D-012**. It approves the on-screen safety/crisis copy and crisis destinations in the new released artifact [SAFETY_COPY.md](SAFETY_COPY.md), governed by [SAFETY.md](SAFETY.md).

It approves **wording, destinations, and the state-truthfulness contract** only. It does not change canonical domain state machines, public API semantics, event schema, consent rules, or readiness gates, and it does not authorize production operation, real veteran data, or any automated emergency dispatch.

It adds/updates:

- [SAFETY_COPY.md](SAFETY_COPY.md) — the released D-012 approved crisis copy (veteran-facing, banners/footer, operator-side), the approved/forbidden language rules, and the `REQUESTED ≠ ACCEPTED ≠ DISPATCHED ≠ ARRIVED ≠ RESOLVED` state-truthfulness principle;
- [RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md) — the D-012 decision ledger entry;
- `SAFETY.md` — D-012 now closed; approved destinations (911 / 988); §5.1 state truthfulness; updated non-goals and testability;
- `DECISIONS.md` — D-012 `DECIDED`;
- `MVP_REFERENCE.md` §7.3 — crisis copy now approved and `MUST_MATCH` the D-012 wording;
- `ENVIRONMENT.md` — `SUAS_SAFETY_COPY_MODE=approved` renders the released copy (non-production);
- lineage/index updates to `VERSIONING.md`, `CHANGELOG.md`, `README.md`, `HANDOFF.md`.

## Released artifact set

All artifacts released by v0.1.4 remain released. v0.1.5 additionally releases `SAFETY_COPY.md`, the D-012 decision ledger, this manifest, and the D-012 clarifications to `SAFETY.md`, `DECISIONS.md`, `MVP_REFERENCE.md`, and `ENVIRONMENT.md`.

Draft Rev 3 files remain draft unless explicitly marked otherwise. They are not redefined by this release.

## D-012 authority

The approved on-screen safety/crisis copy is [SAFETY_COPY.md](SAFETY_COPY.md). The following remain canonical:

1. The only authorized crisis destinations are `911` and the `988` Suicide & Crisis Lifeline (call or text; Veterans via `988`).
2. Implementations render the approved copy where `SAFETY.md` requires it and MUST NOT invent alternative crisis wording, numbers, URLs, or agency contacts presented as official.
3. SUAS performs no automated emergency dispatch, diagnosis, or suicidality determination; it never claims to be an emergency-response service.
4. Trusted-contact alerts still require an `ACTIVE` `can_receive`+`RED` grant, re-checked at send.
5. Crisis/practical-support surfaces obey the `REQUESTED ≠ ACCEPTED ≠ DISPATCHED ≠ ARRIVED ≠ RESOLVED` truthfulness contract; a later state is shown only from its recorded fact, and `DISPATCHED`/`ARRIVED` are practical-support-provider states, never emergency dispatch.
6. Copy approval is not production-operating approval; SPEC-018 and `ENVIRONMENT.md` still gate real operation.

## Runtime pins

- Expected specification stack identifier: `0.1.5`.
- API contract remains `/api/v0`; product/domain/API semantics are unchanged.
- Event schema remains `0.1.0`.
- Database schema version is unchanged by this release (D-012 adds no migration).
- Production QuestionnaireVersion, Support Signal thresholds (D-011), production deployment, live pilot operation, real external provider effects, real public-safety notification, and sensitive aggregate reporting remain unavailable.

## Readiness boundary

This patch makes no readiness gate `READY` and does not authorize production deployment, real veteran data, live pilot operation, real provider side effects, automated emergency dispatch, or payment-card handling. SPEC-017 implementation conformance plus SPEC-018 readiness evidence remain required.

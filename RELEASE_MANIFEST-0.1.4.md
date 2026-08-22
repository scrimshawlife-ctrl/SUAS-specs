# RELEASE_MANIFEST-0.1.4.md — SUAS implementation-conformance codification release

**Release version:** `0.1.4`
**Release date:** `2026-08-22` PT
**Owner:** `@scrimshawlife-ctrl`
**Supersedes:** `0.1.3`
**Base release:** [RELEASE_MANIFEST-0.1.3.md](RELEASE_MANIFEST-0.1.3.md)
**Decision ledgers:** inherited unchanged — [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise. **v0.1.4 closes no D-0xx owner decision.**
**Lifecycle:** `released` (ratified by owner merge of this patch)
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.4 is an implementation-conformance codification patch. It adopts the accepted **Bucket I** (editorial / underspecified) specification gaps surfaced during SPEC-017 implementation and catalogued in `scrimshawlife-ctrl/SUAS` `docs/SPEC_DESIGN_GAPS.md` / `docs/SPEC_GAP_PROPOSALS.md` (proposals P-1 through P-23).

Every change either (a) codifies a mechanism the implementation already runs and tests, or (b) is a pure editorial clarification (authority labeling, a mapping table, or an enumeration already closed in code). It **invents no new product/domain behavior**, makes and closes **no owner decision**, and changes **no** product roles, safety/privacy principles, readiness gates, API selector (`/api/v0`), or event schema (`0.1.0`).

Two additive, backward-compatible logical-model fields are documented because merged, tested implementation already carries them and two released MVP surfaces depend on them for truthfulness:

- `notifications.subject_type` + `subject_id` (P-12), which makes MVP `RESPONDER_NOTIFIED` truthfully reachable;
- `resources.contact_method_kind` (P-13), which lets the veteran resource list offer a direct call/email/web action instead of guessing a scheme from free text.

Both are additive and nullable; absent them, prior behavior is unchanged. Under a strict reading these are backward-compatible contract *additions*; this patch adopts them at patch level as owner-accepted codifications with this explicit changelog notice (VERSIONING.md §2).

## Adopted gaps

| Proposal | Target artifacts |
|---|---|
| P-1 lifecycle-header vs manifest reconciliation | VERSIONING.md |
| P-2 Follow-Up `coordination_attempt_count` naming | FOLLOWUP.md |
| P-3 version-identity set + schema-version mechanism | VERSIONING.md, ENVIRONMENT.md |
| P-4 category ↔ capability ↔ port mapping | FULFILLMENT.md |
| P-5 three independent "mode" concepts | PROVIDER_INTEGRATIONS.md |
| P-6 mandatory per-capability manual adapter + registry | PROVIDER_INTEGRATIONS.md |
| P-7 closed Service Request cancel/expiry/escalation sets | DISPATCH.md |
| P-8 "blocking" Service Request definition | CASES.md |
| P-9 `ACTIVATE` as the only `ASSIGNED → ACTIVE` edge | CASES.md |
| P-10 Follow-Up reschedule/overdue handling | FOLLOWUP.md |
| P-11 `CLOSED → OPEN` sole reopen edge | CASES.md |
| P-12 notification subject reference | DATA_MODEL.md, NOTIFICATIONS.md |
| P-13 structured `contact_method_kind` + typed referral destination | RESOURCES.md, REFERRALS.md |
| P-14 Follow-Up `responsible_type` enum + `referral_id` | DATA_MODEL.md, FOLLOWUP.md |
| P-15 SUAS-admin auditable grant record | DATA_MODEL.md, AUTH.md |
| P-16 assignment/fulfillment projection + reconciliation sub-state | DATA_MODEL.md, DISPATCH.md |
| P-17 closed system-basis registry | CONSENT.md |
| P-18 `grantee_id` typing per `grantee_type` | CONSENT.md |
| P-19 closed permission/scope pairing | CONSENT.md |
| P-20 interim purpose-matching rule | CONSENT.md |
| P-21 deterministic effective-signal selection | SUPPORT_SIGNALS.md, DATA_MODEL.md |
| P-22 `priority_signal_level` on the Case | CASES.md, DATA_MODEL.md |
| P-23 QRF label → canonical fact mapping | MVP_REFERENCE.md |

## Released artifact set

All artifacts released by v0.1.3 remain released. v0.1.4 additionally releases this manifest and the patch-level clarifications above to DATA_MODEL.md, NOTIFICATIONS.md, RESOURCES.md, REFERRALS.md, AUTH.md, CASES.md, FOLLOWUP.md, DISPATCH.md, PROVIDER_INTEGRATIONS.md, FULFILLMENT.md, CONSENT.md, SUPPORT_SIGNALS.md, MVP_REFERENCE.md, VERSIONING.md, and ENVIRONMENT.md. Per VERSIONING.md §1, this manifest's released-artifact set overrides any stale inline `draft` header on a listed file.

Draft Rev 3 files remain draft unless explicitly marked otherwise. They are not redefined by this release.

## Runtime pins

- Expected specification stack identifier: `0.1.4`.
- API contract remains `/api/v0`; product/domain/API semantics are unchanged.
- Event schema remains `0.1.0`.
- Database schema version is the implementation-owned monotonic integer (highest applied numbered migration), declared explicitly by the build (VERSIONING.md §3, ENVIRONMENT.md §9).
- Production QuestionnaireVersion, Support Signal scoring/thresholds (D-011), official safety copy (D-012), production deployment, live pilot operation, real external provider effects, and sensitive aggregate reporting remain unavailable.

## Not closed by this release

The Bucket II (`D-0xx`) owner decisions and Bucket III contradictions catalogued in `docs/SPEC_DESIGN_GAPS.md` remain owner-only and are **not** addressed: notably D-011 thresholds, D-012 crisis copy/destinations, PARTIAL/DISPUTED/FAILED policy (G-I-6/7/8), Settlement summary shape (G-I-19), category-required-details (G-I-20), the signal-driven case command (G-I-28 action), responder on-duty / chat / dashboard-metric / Quick-Share contracts (G-I-30..33), auth timing/rate-limit constants (G-I-34), tenant resolution at sign-in (G-I-35), notification template vocabulary (G-I-37), and the consent-template publication surface (G-I-38).

## Readiness boundary

This patch makes no readiness gate `READY` and does not authorize production deployment, real veteran data, live pilot operation, real provider side effects, or payment-card handling. SPEC-017 implementation conformance plus SPEC-018 readiness evidence remain required.

# DECISIONS.md — Open decisions (do not guess)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [STATUS.md](STATUS.md), [PRODUCT.md](PRODUCT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [SPEC-001.md](SPEC-001.md), [FRICTION.md](FRICTION.md)

This file lists decisions that are **not** made in v0.1.0. Implementation must not invent values. Use `DECISION_PENDING` or `NOT_COMPUTABLE` until a later spec version records a decision.

---

## 1. Rules

1. Do not guess.
2. Do not treat a vendor preference, prototype behavior, blog post, or informal conversation as a decision.
3. Do not lock a cloud/auth/communications/service-provider/queue/cache vendor into domain architecture.
4. Provider choices are adapter/configuration decisions; they do not redefine Service Request or Fulfillment contracts.
5. Do not name pilot partner organizations before D-008 closes. Use placeholders.
6. Do not write exact Support Signal scoring weights before D-011 closes with versioned test vectors.
7. Do not invent capacity, SLO, RTO/RPO, or reporting-privacy thresholds.
8. When a decision is made, record decision, date (PT), owner, spec version, consequences, and supersedes.

---

## 2. Open decisions

| ID | Decision | Status | Blocking / boundary |
|---|---|---|---|
| D-001 | Production hosting / cloud provider | `DECISION_PENDING` | Production deployment |
| D-002 | Auth provider (or in-house) | `DECISION_PENDING` | Auth implementation |
| D-003 | SMS provider | `DECISION_PENDING` | SMS/phone OTP when enabled |
| D-004 | Email provider | `DECISION_PENDING` | Email auth/notification when enabled |
| D-005 | Database hosting | `DECISION_PENDING` | Production deployment |
| D-006 | Legal / HIPAA classification | `DECISION_PENDING`; `HIPAA_APPLICABILITY = DECISION_PENDING` | Legal/compliance claims |
| D-007 | Retention durations for operational data, events, backups, idempotency records | `DECISION_PENDING` | Privacy/security/production retention |
| D-008 | Pilot partner organizations | `DECISION_PENDING` | Named pilot operation |
| D-009 | Responder staffing model / coverage hours | `DECISION_PENDING` | Pilot/operations coverage; no 24/7 claim |
| D-010 | Service funding sources / amounts | `DECISION_PENDING`; funding path `FUTURE` | Does not block no-funding MVP fulfillment |
| D-011 | Exact Support Signal scoring rules / thresholds | `DECISION_PENDING` | Production signal engine |
| D-012 | Approved safety/crisis-resource copy | `DECISION_PENDING` | Production safety UI |
| D-013 | Counsel review of compliance regime register before pilot | `DECISION_PENDING` | Pilot operation; review is not compliance claim |
| D-014 | Whether geocoding/maps API is required for MVP | `DECISION_PENDING` | Resource/coverage implementation; may be safely deferred if feature absent |
| D-015 | Full Case Note veteran visibility beyond MVP default | `DECISION_PENDING`; MVP default denies full notes | Future visibility choice |
| D-016 | Identity-proofing beyond self-attest + working passwordless channel | `DECISION_PENDING`; MVP default remains inferred | Future enrollment proofing choice |
| D-017 | Enabled transportation provider adapter(s) by environment/coverage | `DECISION_PENDING` | External transportation only; manual path remains valid |
| D-018 | Enabled temporary shelter/room provider adapter(s) | `DECISION_PENDING` | External shelter only; manual path remains valid |
| D-019 | Enabled food-support provider adapter(s) | `DECISION_PENDING` | External food integration only |
| D-020 | Whether any external peer-support adapter is enabled | `DECISION_PENDING`; internal/manual QRF valid | External peer-support only |
| D-021 | Release-specific target workload/capacity envelope | `DECISION_PENDING` | SCALE release plan; no invented regional user count |
| D-022 | Durable job/queue implementation | `DECISION_PENDING` | Production-critical async work |
| D-023 | Release-specific performance SLOs / alert thresholds | `DECISION_PENDING` | Production scale/operations acceptance |
| D-024 | Production RTO/RPO / backup-restore objectives | `DECISION_PENDING` | Production resilience/readiness |
| D-025 | Aggregate reporting privacy policy, including small-cell suppression/aggregation rule and threshold | `DECISION_PENDING` | Production aggregate reporting; affected slices remain unavailable until policy accepted |

---

## 2.1 Proposed closes (not accepted)

[FRICTION.md](FRICTION.md) proposes possible closes. They remain proposals until owner decision.

| ID | Proposal | Current status |
|---|---|---|
| D-014 | `GEOCODE_MAPS` not required for MVP | still `DECISION_PENDING` |
| D-012 narrow | 911 + 988 display-only; local copy remains open | still `DECISION_PENDING` |

---

## 3. Already decided architecture/product boundaries

- SUAS is coordination, not diagnosis/EHR/automated emergency dispatch.
- Canonical loop/concepts remain distinct.
- Modular monolith is default; microservices require measured need + released spec.
- App correctness does not depend on one process; horizontal stateless application scaling is the target contract.
- Production-critical async work is durable; exact queue/job product remains open.
- External fulfillment is capability-port based; provider SDKs adapter-local; manual coordination first-class.
- Provider-specific status never becomes canonical Service Request/Fulfillment state.
- The referenced MVP is the visual/interaction source of truth subject to canonical auth/safety/privacy/accessibility/domain/truthfulness overrides.
- Veteran auth is passwordless; no social-login assumption; privileged users require MFA.
- No generative primary Support Signal or other safety-critical generative decision.
- No automated 911/PSAP dispatch.
- Billing/Medi-Cal adapter remains `FUTURE`; no billability claim.
- Controlled pilot remains approximately 25–50 veterans in Santa Clara County unless explicitly changed.
- Pilot size is not a production architecture ceiling.
- Aggregate operational metrics are not clinical/causal outcome evidence.
- Specs are canonical; implementation cites released specs.

---

## 4. Provider-decision rule

Closing D-017–D-020 selects enabled conforming adapters/configuration. It does not permit vendor-specific domain types/statuses. A provider replacement should require adapter/config changes only unless it introduces a genuinely new product capability requiring a spec change.

---

## 5. Reporting-privacy decision rule

D-025 must define the production aggregate reporting privacy rule before affected small/sensitive slices are exposed. Until it closes:

- no numeric suppression threshold is canonical;
- test fixtures may use explicitly labeled non-production values;
- raw veteran-level data remains governed by normal authorization/export rules, not aggregate-report permissions;
- reporting features that cannot safely aggregate without the policy remain unavailable rather than guessing a threshold.

---

## 6. Decision record template

```text
### D-0XX — <title>
- Status: decided
- Date (PT):
- Owner:
- Spec version:
- Decision:
- Alternatives considered:
- Consequences:
- Supersedes:
```

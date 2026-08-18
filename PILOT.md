# PILOT.md — Santa Clara County controlled pilot (SUAS v0.1)

**Status:** `draft` / `0.1.0`; SPEC-014 dependency-blocked.  
**Related:** [PRODUCT.md](PRODUCT.md), [OPERATIONS.md](OPERATIONS.md), [ANALYTICS.md](ANALYTICS.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Scope

| Item | Value |
|---|---|
| Population | approximately 25–50 veterans in the controlled pilot cohort |
| Geography | Santa Clara County, California |
| Categories | `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT` |
| Readiness | `NOT_READY` |

The controlled cohort remains intentionally bounded even if market/community interest exceeds the cohort size. **Traction is not permission to silently expand the pilot.** Additional demand may be waitlisted, tracked separately, or handled through a later accepted operating/release decision.

The 25–50 cohort is an operating/research boundary, not a technical capacity ceiling. D-021 separately defines the target production workload envelope for a release.

Do not invent partner organizations. D-008 owns named partner decisions. D-009 owns staffing/coverage. D-013 counsel review is required before operating the pilot and is not a compliance claim.

---

## 2. Enrollment

MVP pilot identity default remains self-attested veteran status plus a working configured passwordless email and/or phone channel; D-016 remains open for proofing beyond that default.

No VA API, DD-214 upload, or in-person proofing is required by this draft pilot contract.

Enrollment creates User + VeteranProfile + PilotEnrollment, emits the accepted enrollment fact, collects only allowed fields, and does not authorize third-party disclosure without accepted consent/system basis.

If the controlled cohort is full, the product may expose a truthful waitlist/interest path. Waitlist/interest records must not be represented as `ENROLLED` PilotEnrollment rows or counted as pilot participants.

---

## 3. Staffing / operations

Before go-live:
- at least one participating Organization and sufficient responder coverage for the accepted operating window exist under D-008/D-009;
- queue, Follow-Up, provider/manual path, incident, and recovery duties have assigned owners;
- no 24/7 claim exceeds actual accepted staffing.

---

## 4. Resource/provider readiness

Every active Resource used operationally has current verification according to the accepted Resource contract. Every enabled external adapter passes provider conformance. Manual coordination remains available where the target workflow relies on it.

Provider degradation/fallback has been exercised in STAGING. Provider outage does not silently close a Service Request.

---

## 5. Consent / safety

Small scale does not waive Consent, tenant isolation, audit, provider minimum-necessary disclosure, or safety boundaries.

No automated emergency dispatch or clinical/suicide-prediction claim is introduced by pilot operation.

---

## 6. Scale / resilience boundary

Pilot traffic is one release workload profile, not architecture definition.

Launch-applicable evidence includes stateless app behavior, durable jobs, atomic contested actions, persistent idempotency, bounded APIs, provider reconciliation, callback safety, observability, failure drills, and backup/restore according to released SCALE/RESILIENCE contracts.

D-021/D-023 define the actual release load/performance envelope. D-024 defines recovery objectives.

---

## 7. Duration

Start/end dates remain `NOT_COMPUTABLE` until scheduled and are then recorded as Pilot configuration/operation facts.

---

## 8. Feedback / analytics

Feedback is operational/satisfaction data, not a clinical instrument.

Pilot reporting follows [ANALYTICS.md](ANALYTICS.md):
- confirmed fulfillment is not conflated with mere provider/Fulfillment completion;
- open work is not silently counted as success/failure;
- provider/manual/Referral metrics remain operational;
- no suicides-prevented/lives-saved/clinical-efficacy/causal health claims;
- D-025 must close before affected small/sensitive aggregate slices are exposed in production reporting.

Interest/waitlist demand outside the enrolled pilot cohort may be tracked as a separate product/traction metric only if a later accepted data model/privacy rule defines it. It is not pilot enrollment and not evidence of service outcomes.

---

## 9. Launch gate

Pilot may start only when:
- required specs are released at SPEC-016;
- implementation conformance SPEC-017 is complete for the target build;
- SPEC-018 launch readiness passes or has explicit owner-approved spec waivers;
- launch-applicable gates are READY/waived;
- D-013 is closed;
- launch-required partner/staffing/provider/environment/retention/capacity/SLO/recovery/reporting decisions are closed or affected features are explicitly unavailable;
- PRODUCTION bootstrap is complete;
- required load/failure/restore evidence is recorded.

---

## 10. Exit criteria

Pilot exits when the accepted target duration/completion condition occurs, enrollment reaches zero, an audited incident/decision stops operation, or SPEC-018 readiness is withdrawn. Post-pilot/post-launch revision proceeds through SPEC-019.

---

## 11. Non-goals

- silently expanding the pilot because demand is high;
- treating waitlist/interest as enrolled veterans;
- treating cohort size as system capacity;
- named partnerships/coverage without evidence;
- permanent housing semantics under `SHELTER`;
- clinical/causal outcome claims;
- selecting a provider merely to make the pilot look complete.

---

## 12. Testability

Synthetic pilot simulation exercises enrollment boundaries, one manual fulfillment path, configured fake provider adapters including duplicate/timeout behavior, full canonical loop, tenant isolation, allowed operational analytics, and cohort-vs-waitlist separation if a waitlist feature is later released.

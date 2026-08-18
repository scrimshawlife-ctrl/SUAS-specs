# PILOT.md — Santa Clara County pilot (SUAS v0.1)

**Related:** [PRODUCT.md](PRODUCT.md), [OPERATIONS.md](OPERATIONS.md), [ANALYTICS.md](ANALYTICS.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Scope

| Item | Value |
|---|---|
| Population | Approximately 25–50 veterans |
| Geography | Santa Clara County, California |
| Categories | `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT` |
| Readiness | `NOT_READY` |

Do not invent partners. Use `PARTNER_ORG_001`, `PARTNER_ORG_002`, … until D-008 is closed.

Counsel review of the [COMPLIANCE.md](COMPLIANCE.md) register is D-013 `DECISION_PENDING` and is required before operating this pilot. Closing D-013 is not a compliance claim.

Responder staffing and coverage hours remain D-009 `DECISION_PENDING`.

The pilot is intentionally small, but it runs on the same correctness, durability, provider-idempotency, tenant-isolation, and resilience rules required by the production architecture. Pilot size is not permission to build pilot-only architectural ceilings.

---

## 2. Enrollment

1. MVP enrollment identity-proofing (`INFERRED` operational default): self-attested veteran status plus a working email and/or phone via passwordless auth. No VA identity API, DD-214 upload, or in-person proofing is required by this draft pilot contract. D-016 remains open.
2. Create User + VeteranProfile + PilotEnrollment `ENROLLED`.
3. Emit `VETERAN_ENROLLED`.
4. Collect only fields allowed by [DOMAIN_MODEL.md](DOMAIN_MODEL.md) and [PRIVACY.md](PRIVACY.md).
5. Consent templates must be accepted before third-party disclosure.

Configured pilot enrollment capacity remains approximately 25–50. Growth beyond that is handled by a later accepted operating/spec decision; it must not require a core architecture rewrite.

---

## 3. Staffing

- At least one Organization with ACTIVE responders is required before go-live (D-008, D-009).
- Organization participation is represented by Organization + membership records.
- SUAS-admin incident/recovery coverage follows [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), and [RESILIENCE.md](RESILIENCE.md).

---

## 4. Resources and provider readiness

Before go-live:

1. Every `ACTIVE` Resource used by the pilot has current verification per [RESOURCES.md](RESOURCES.md).
2. Every enabled external provider adapter conforms to [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).
3. A manual coordination path exists for MVP service categories where no reliable direct integration is configured.
4. Provider outage/degraded-mode procedures have been exercised in STAGING.

---

## 5. Consent and safety

Pilot operations do not bypass Consent Grants. Small scale is not an exception.

Safety behavior follows [SAFETY.md](SAFETY.md). Pilot operation does not introduce automated emergency dispatch or clinical claims.

---

## 6. Scale and resilience boundary

Pilot traffic is a deployment profile, not an architecture definition.

Before launch, the implementation must demonstrate the launch-applicable portions of:

- stateless application behavior;
- durable production-critical jobs;
- atomic contested claims/assignments;
- bounded/paginated list APIs;
- provider mutation idempotency and reconciliation;
- duplicate webhook safety;
- queue/provider/database health observability;
- backup/restore procedure;
- graceful degradation/manual fallback.

See [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), and [TESTING.md](TESTING.md).

---

## 7. Duration

Start/end dates are `NOT_COMPUTABLE` until scheduled. Record them on the Pilot row when known.

---

## 8. Feedback and analytics

Use first-class Feedback entities. Veteran and responder satisfaction are operational metrics, not clinical instruments.

No pilot result may be converted into a suicides-prevented, clinical-efficacy, or diagnosis-performance claim.

---

## 9. Incidents and operations

Follow [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) and [OPERATIONS.md](OPERATIONS.md). Distinguish technical incidents from veteran-support/safety incidents.

External dependency outages do not silently close Service Requests. They enter the documented retry, reconciliation, reroute, or manual path.

---

## 10. Launch gate

The pilot may start only when:

- the required specification artifacts are released;
- SPEC-017 implementation conformance is complete for the target build;
- SPEC-018 launch-applicable readiness review passes or has explicit owner-approved spec waivers;
- all launch-applicable readiness gates in [STATUS.md](STATUS.md) are `READY` or explicitly waived;
- D-013 is closed;
- required partner/staffing/provider/environment decisions for enabled features are closed;
- the PRODUCTION bootstrap checklist is `CHECKLIST_COMPLETE`;
- load, failure, and restore evidence required by the target launch has been recorded.

---

## 11. Exit criteria

The pilot may exit when any of the following is recorded on the Pilot row:

- target duration completes and **SPEC-019** post-pilot/post-launch revision is opened;
- enrollment is withdrawn to zero;
- a safety/security/technical incident requires an audited stop;
- **SPEC-018** readiness is withdrawn or a launch waiver is revoked.

---

## 12. Non-goals

- named county contracts without evidence
- VA partnership claims
- permanent housing semantics under MVP `SHELTER`
- clinical-efficacy claims
- treating the 25–50 participant target as the technical capacity ceiling
- selecting a provider merely to satisfy this pilot document

---

## 13. Testability

The pilot simulation suite must:

- enroll synthetic pilot users only;
- exercise the full canonical loop;
- exercise at least one manual fulfillment path;
- exercise configured fake provider adapters and duplicate/timeout behavior;
- produce allowed operational metrics;
- validate tenant isolation;
- use no production veteran data.

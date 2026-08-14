# PILOT.md — Santa Clara County pilot (SUAS v0.1)

**Related:** [PRODUCT.md](PRODUCT.md), [OPERATIONS.md](OPERATIONS.md), [ANALYTICS.md](ANALYTICS.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md)

---

## 1. Scope

| Item | Value |
|---|---|
| Population | Approximately 25–50 veterans |
| Geography | Santa Clara County, California |
| Categories | `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT` |
| Readiness | `NOT_READY` |

Do **not** invent partners. Use `PARTNER_ORG_001`, `PARTNER_ORG_002`, … until D-008 is closed.

Environment bootstrap (empty-system first-run) is [ONBOARDING.md](ONBOARDING.md). Counsel review of the [COMPLIANCE.md](COMPLIANCE.md) register is **D-013** `DECISION_PENDING` and is required before operating this pilot. Closing D-013 is not a compliance claim.

Responder staffing and coverage hours: D-009 `DECISION_PENDING`.

---

## 2. Enrollment

1. Veteran identity-proofing process is `DECISION_PENDING` (must be documented before SPEC-014).
2. Create User + VeteranProfile + PilotEnrollment `ENROLLED`.
3. Emit `VETERAN_ENROLLED`.
4. Collect only fields in [DOMAIN_MODEL.md](DOMAIN_MODEL.md). Do not collect SSN, service-record dumps, or medical history.
5. Consent templates accepted before any third-party share.

Capacity: refuse or waitlist above 50 without a spec change.

---

## 3. Staffing

- At least one Organization with ACTIVE responders is required before go-live (D-008, D-009).
- Org participation is recorded as Organization + memberships, not as informal side channels.
- SUAS-admin coverage for incidents: [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

---

## 4. Resource verification

Before go-live, every `ACTIVE` Resource used in the pilot must have `last_verified_at` within 30 days (freshness recommendation in [RESOURCES.md](RESOURCES.md)). Ongoing verification is an ops duty.

---

## 5. Consent

Pilot operations do not bypass Consent Grants. Small scale is not an exception.

---

## 6. Duration

Start/end dates: `NOT_COMPUTABLE` until scheduled. Record them on the `Pilot` row when known.

---

## 7. Feedback

Use `Feedback` entities. Veteran and responder satisfaction are operational metrics ([ANALYTICS.md](ANALYTICS.md)), not clinical instruments.

---

## 8. Incidents

Follow [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md). Distinguish technical incidents from veteran-support / safety incidents.

---

## 9. Ops support

See [OPERATIONS.md](OPERATIONS.md). App reliability and veteran-support ops are separate.

---

## 10. Exit criteria

The pilot may exit when **any** of the following is recorded on the Pilot row:

- Target duration completed and SPEC-015 opened
- Enrollment withdrawn to zero
- Safety or security incident requiring stop (SUAS-admin decision, audited)
- SPEC-014 waiver or fail that withdraws readiness

MVP gates in [STATUS.md](STATUS.md) must be `READY` (or an explicit spec waiver) before start. D-013 must be closed. Environment bootstrap checklist must be `CHECKLIST_COMPLETE` in PRODUCTION ([ONBOARDING.md](ONBOARDING.md)).

---

## 11. Non-goals

- Named county contracts (`NOT_COMPUTABLE`)
- VA partnership claims
- Expanding past 50 veterans without a spec change
- Using the pilot to justify clinical-efficacy claims

---

## 12. Testability

Pilot simulation suite in [TESTING.md](TESTING.md): enroll N synthetic veterans (N<=50), run the loop, produce operational metrics, no production data.

# PRIVACY.md — Data minimization and collection boundaries (SUAS v0.1)

**Related:** [SECURITY.md](SECURITY.md), [COMPLIANCE.md](COMPLIANCE.md), [CONSENT.md](CONSENT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

Treat veteran support data as highly sensitive regardless of HIPAA classification. Collect the minimum needed to run the canonical loop.

---

## 2. Principles

| Principle | Rule |
|---|---|
| Minimization | If a field is not required by a specified workflow, do not collect it |
| Purpose limitation | Use data only for the purpose recorded on the Consent Grant or documented system basis |
| Collection boundaries | §3 |
| Retention | Durations D-007 `DECISION_PENDING`; do not invent |
| Deletion | Veteran/admin deletion requests follow a documented process; events/audit are not silently destroyed |
| Exports | Audited; SUAS-admin or the veteran (own data); never to non-prod |
| Access logging | Reads of sensitive fields emit Audit Events |
| Consent history | Preserved separately ([CONSENT.md](CONSENT.md)) |

---

## 3. Collection boundaries — discourage / do not collect by default

Do **not** collect the following unless a **specific approved workflow** (later released spec) requires it:

- Continuous GPS / background location
- Full device contact lists
- SSN
- Medical history
- Diagnoses
- DD-214 or equivalent service-record dumps
- Device telemetry beyond what AUTH/session security requires

Location: a one-time, purpose-scoped location (for example a pickup address on a `TRANSPORTATION` request) may be collected with `can_view`+`location` grants for parties who need it. Continuous tracking is out of scope.

Sensitive free-text (notes, check-in free-text, feedback) is stored, access-logged, and not written to application logs.

---

## 4. Veteran export

A Veteran may request an export of their profile, check-ins, grants, cases (veteran-visible fields), requests, and settlements. Exact package format `DECISION_PENDING`. Export is audited.

---

## 5. Non-goals

- Selling data
- Secondary research without a later spec and consent purpose
- Claiming HIPAA compliance ([SECURITY.md](SECURITY.md))

---

## 6. Testability

PRIVACY gate ([TESTING.md](TESTING.md), [STATUS.md](STATUS.md)):

- Schema/API reject SSN and DD-214 fields.
- No continuous GPS endpoint.
- Export is audited.
- Sensitive values absent from log fixtures.


---

## 7. Actors

- Veteran: subject; may export own data; may revoke grants.
- Responder: need-to-know only.
- Org-admin / SUAS-admin: access reviews; exports audited.
- Trusted Contact: only granted objects.

---

## 8. Events

Exports, deletion requests, and sensitive-field reads emit Audit Events. Consent history uses `CONSENT_GRANTED` / `CONSENT_REVOKED` plus ConsentEvents.

---

## 9. Retention and deletion (open)

Durations are D-007 `DECISION_PENDING`. Until closed:

- Do not implement an automatic purge of Audit Events or Domain Events.
- Soft-delete operational rows when a user is revoked.
- A veteran deletion request is recorded and fulfilled only to the extent a later spec allows after D-007.

---

## 10. Links

[SECURITY.md](SECURITY.md), [COMPLIANCE.md](COMPLIANCE.md), [CONSENT.md](CONSENT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md), [DECISIONS.md](DECISIONS.md), [TESTING.md](TESTING.md)

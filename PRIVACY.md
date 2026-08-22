# PRIVACY.md — Data minimization and collection boundaries (SUAS v0.1)

**Related:** [SECURITY.md](SECURITY.md), [COMPLIANCE.md](COMPLIANCE.md), [CONSENT.md](CONSENT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md), [DECISIONS.md](DECISIONS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md)

**Authority:** released via [RELEASE_MANIFEST-0.1.6.md](RELEASE_MANIFEST-0.1.6.md). Any leftover inline `draft` marker is stale and is not authority ([VERSIONING.md](VERSIONING.md) §1).

---

## 1. Purpose

Treat veteran support data as highly sensitive regardless of HIPAA classification. Collect and disclose only the minimum needed to run the canonical loop.

---

## 2. Principles

| Principle | Rule |
|---|---|
| Minimization | If a field is not required by a specified workflow, do not collect it |
| Purpose limitation | Use data only for the purpose recorded on the Consent Grant or documented system basis |
| Provider minimization | External providers receive only a capability-specific projection, never a whole Support Case by default |
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

Location: a one-time, purpose-scoped location (for example a pickup address on a `TRANSPORTATION` request) may be collected and disclosed only to parties whose consent/system basis covers that use. Continuous tracking is out of scope.

Sensitive free-text (notes, check-in free-text, feedback) is stored, access-logged, and not written to application logs.

---

## 4. Provider disclosure boundary

External fulfillment providers are third parties unless a later accepted contract explicitly classifies a relationship differently. Their APIs, webhooks, SDKs, or business terms do not expand SUAS data rights.

Rules:

1. Build a capability-specific provider projection from SUAS-owned fields.
2. Default projection excludes full Case Notes, Check-In responses, Support Signal basis, Trusted Circle contacts, audit history, and unrelated requests.
3. Send only fields needed for the specific fulfillment action. Example: transportation may need pickup/destination/contact coordination; it does not need the veteran's unrelated check-in history.
4. Provider responses/webhooks are treated as untrusted external input and mapped into normalized SUAS fields before persistence.
5. Do not persist complete vendor payloads when normalized fields and an external reference are sufficient.
6. Do not write provider request/response bodies containing veteran data to ordinary logs.
7. Provider replacement/rerouting creates a new disclosure decision; do not reuse a prior provider's consent basis automatically.
8. Adapter telemetry should prefer opaque identifiers (`service_request_id`, `fulfillment_attempt_id`, adapter id, external reference) over names, phone numbers, addresses, or free text.
9. If a provider requires data outside the accepted projection, the integration is not production-ready until the relevant consent/privacy/spec boundary is updated and accepted.

For D-018 temporary-shelter search/inventory, the released projection is limited to the fields listed in [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) §13.2. Raw payment-card data, government identity documents, full Case/Check-In content, Support Signal basis, medical history, military records, and unrelated household information are excluded. A provider requirement outside that projection fails to a human/spec decision rather than expanding disclosure implicitly.

See [CONSENT.md](CONSENT.md) and [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

---

## 5. Veteran export

A Veteran may request an export of their profile, check-ins, grants, cases (veteran-visible fields), requests, and settlements. Exact package format `DECISION_PENDING`. Export is audited.

Veteran-visible fields (D-015 `DECIDED` v0.1 default): own Check-Ins, own Service Request status, Settlement fields written for them, Follow-Up prompts addressed to them, Support Case existence and status. Export **must not** include full Case Notes, Contact Attempts, other veterans, responder internal queue fields, or other Organizations. See [CASES.md](CASES.md) section 8.

Provider-side records are not automatically part of the SUAS export unless SUAS stores them as canonical veteran-visible fields. External provider data-rights/export behavior is provider/legal-contract specific and must not be invented.

---

## 6. Non-goals

- Selling data
- Secondary research without a later spec and consent purpose
- Claiming HIPAA compliance ([SECURITY.md](SECURITY.md))
- Mirroring provider data lakes or storing full third-party payload history without a specified need

---

## 7. Testability

PRIVACY gate ([TESTING.md](TESTING.md), [STATUS.md](STATUS.md)):

- Schema/API reject SSN and DD-214 fields.
- No continuous GPS endpoint.
- Export is audited.
- Sensitive values absent from log fixtures.
- Provider adapter fixtures show only the accepted minimum projection.
- Full Case Notes, Check-In response bodies, Support Signal basis, Trusted Circle data, and audit history are absent from provider payloads unless an explicit accepted workflow requires them.
- Provider webhook/request bodies are not written to ordinary logs.

---

## 8. Actors

- Veteran: subject; may export own data; may revoke grants.
- Responder: need-to-know only.
- Org-admin / SUAS-admin: access reviews; exports audited.
- Trusted Contact: only granted objects.
- Service Provider: only the minimum data projection needed for the consented fulfillment purpose.

---

## 9. Events

Exports, deletion requests, sensitive-field reads, and provider disclosures emit Audit Events or equivalent auditable disclosure records. Consent history uses `CONSENT_GRANTED` / `CONSENT_REVOKED` plus ConsentEvents.

---

## 10. Retention and deletion (open)

Durations are D-007 `DECISION_PENDING`. Until closed:

- Do not implement an automatic purge of Audit Events or Domain Events.
- Soft-delete operational rows when a user is revoked.
- A veteran deletion request is recorded and fulfilled only to the extent a later spec allows after D-007.
- Do not assume SUAS deletion automatically deletes provider-side copies; provider/legal obligations are `NOT_COMPUTABLE` until the provider is selected and applicable agreements are reviewed.

---

## 11. Links

[SECURITY.md](SECURITY.md), [COMPLIANCE.md](COMPLIANCE.md), [CONSENT.md](CONSENT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md), [DECISIONS.md](DECISIONS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [TESTING.md](TESTING.md)

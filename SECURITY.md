# SECURITY.md — Security controls and threats (SUAS v0.1)

**Related:** [AUTH.md](AUTH.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ADMIN.md](ADMIN.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DEPLOYMENT.md](DEPLOYMENT.md), [PRODUCT.md](PRODUCT.md), [ONBOARDING.md](ONBOARDING.md)

---

## 1. Purpose

Treat veteran support data as **highly sensitive regardless of HIPAA**. Specify controls and threat categories. Do **not** claim HIPAA compliance.

```
HIPAA_APPLICABILITY = DECISION_PENDING
```

D-006 remains open. Implementation must not display "HIPAA compliant" or similar.

The regime register is [COMPLIANCE.md](COMPLIANCE.md). That file does not make SUAS HIPAA-compliant or anything-compliant.

---

## 2. Required controls

| Control | Rule |
|---|---|
| TLS | All network traffic in transit (clients to app, app to DB, app to notification providers) |
| Encryption at rest | Database and backups encrypted. Key management `DECISION_PENDING` |
| RBAC | Roles in [AUTH.md](AUTH.md) / [ADMIN.md](ADMIN.md) |
| Tenant isolation | `tenant_id` on tenant-owned rows; no cross-tenant query without SUAS-admin audited path |
| Row-level authz | Authentication is not authorization; every read/write checks role + tenant + row + consent |
| MFA | Required for Responder, Org-admin, SUAS-admin |
| Secrets | No secrets in git, logs, or client bundles. Secret store `DECISION_PENDING` |
| Rate limits | Auth challenges, list endpoints, notification send |
| Sessions | Revocable; invalidate on revoke ([AUTH.md](AUTH.md)) |
| Audit | Immutable Audit Events ([EVENT_MODEL.md](EVENT_MODEL.md)) |
| Backups | Per environment; restore testing required ([DEPLOYMENT.md](DEPLOYMENT.md)) |
| Restore testing | Periodic; recorded |
| Retention | D-007 `DECISION_PENDING` |
| Deletion | Soft-delete plus process; events not casually purged |
| Least privilege | Host, DB, and application roles |
| No prod data in dev | Absolute |
| No sensitive data in logs | Identifiers only |

---

## 3. Medi-Cal / billing boundary

Billing adapter is `FUTURE`. Do not store payment card data. Do not assert Medi-Cal billability. See [PRODUCT.md](PRODUCT.md) and [SETTLEMENT.md](SETTLEMENT.md).

```
Fulfillment -> Funding Eligibility -> Funding Source -> Optional Billing Adapter
STATUS = FUTURE
```

---

## 4. Threat categories

Implementation and review must address each:

| Category | Example | Mitigation (specified) |
|---|---|---|
| Broken access control | Responder reads unassigned veteran check-ins | Row-level authz + consent/basis |
| Cross-tenant leakage | Org A query returns Org B cases | tenant_id + tests |
| Responder overreach | Enumerating Trusted Circle addresses | Deny by default; incident-only path |
| Trusted-contact overexposure | Membership used as visibility | Grants required |
| Compromised responder account | Stolen session | MFA, session revoke, audit, least privilege |
| Notification leakage | Message to wrong address or after revoke | Re-check grant; record consent_basis |
| Stale consent | Cached allow after revoke | Evaluate at use time |
| Insecure audit logs | Mutable logs, admin wipe | Immutable store; no app DELETE |
| Resource poisoning | Fake resource that misleads veterans | Org-owned writes, verification, freshness |
| Malicious notes/content | Script or phishing in CaseNote | Treat body as untrusted; encode at render |
| Accidental production-data exposure | Prod dump in TEST | Separate DBs; no prod-in-dev |

---

## 5. Non-goals

- Claiming HIPAA compliance
- Claiming SOC2/ISO without evidence (`NOT_COMPUTABLE`)
- Inventing legal notification deadlines ([INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md))

---

## 6. Testability

Critical suites: cross-tenant isolation, responder authorization, audit-event immutability, notification consent, trusted-circle visibility.

SECURITY is a control set; the MVP gate names for product acceptance are listed in [STATUS.md](STATUS.md). Security tests are still mandatory in [TESTING.md](TESTING.md).

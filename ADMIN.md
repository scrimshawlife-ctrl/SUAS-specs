# ADMIN.md — Administration surfaces (SUAS v0.1)

**Related:** [AUTH.md](AUTH.md), [SECURITY.md](SECURITY.md), [PILOT.md](PILOT.md), [RESOURCES.md](RESOURCES.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md)

---

## 1. Purpose

Define what Organization Administrators and SUAS System Administrators can manage. **Org-admin ≠ global SUAS-admin.**

---

## 2. SUAS System Administrator

May manage (all audited):

| Area | Notes |
|---|---|
| Users | Status, revoke, recovery; not silent impersonation |
| Organizations | Create/suspend/archive; placeholders until D-008 |
| Membership | Across orgs |
| Cases | Read/break-glass with reason; not routine ownership |
| Service Requests | Same |
| Resources | Any org; verification |
| Questionnaire versions | Draft/publish/supersede |
| Signal-rule versions | Publish; D-011 may still be open — do not publish invented weights |
| Consent templates | Publish versions |
| Notification templates | Publish versions; no safety-critical logic |
| Pilot config | [PILOT.md](PILOT.md) |
| Audit | Read Audit Events / Domain Events |
| Reports | [ANALYTICS.md](ANALYTICS.md) operational metrics |
| System config | Feature flags that do **not** redefine specs |

---

## 3. Organization Administrator

Scoped to **one** Organization:

- Users who have membership in that org (invite, suspend, revoke membership)
- Responders and `active_for_queue`
- Org-owned Resources and Service Offers
- Org queue visibility
- Org notification defaults (cannot override veteran consent)

Cannot:

- Change global signal rules, questionnaire versions, consent templates, system config
- See other orgs' cases or veterans
- Grant themselves `SUAS_ADMIN`

---

## 4. Rules

- MFA required ([AUTH.md](AUTH.md)).
- Every privileged write emits an Audit Event.
- Least privilege.
- Production data is not exported to non-prod ([SECURITY.md](SECURITY.md)).

---

## 5. Non-goals

- Clinical admin
- Billing admin (FUTURE)
- Impersonation without a later break-glass spec

---

## 6. Testability

- Org-admin API to another tenant returns 404/403 without leakage.
- Org-admin cannot publish questionnaire versions.
- SUAS-admin actions are audited.


---

## 7. Events

Admin writes do not all have Domain Event types. They **must** have Audit Events. Domain Events that admin actions may cause indirectly: `VETERAN_ENROLLED` (enrollment), questionnaire publish (audit only in v0.1).

---

## 8. States

Admin-managed lifecycles reuse entity states in [DOMAIN_MODEL.md](DOMAIN_MODEL.md): User, Organization, OrganizationMembership, Pilot, QuestionnaireVersion.

---

## 9. Non-goals (expanded)

- Clinical coding administration
- Billing / Medi-Cal administration (`FUTURE`)
- Editing published questionnaire or signal versions in place ([VERSIONING.md](VERSIONING.md))
- Granting Trusted Circle visibility by admin fiat

---

## 10. Links

[AUTH.md](AUTH.md), [SECURITY.md](SECURITY.md), [PILOT.md](PILOT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CONSENT.md](CONSENT.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ANALYTICS.md](ANALYTICS.md)

# TRUSTED_CIRCLE.md — Trusted Circle and Trusted Contacts (SUAS v0.1)

**Related:** [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [GLOSSARY.md](GLOSSARY.md)

**Actors:** Veteran, Trusted Contact, System, Responder (does not enumerate the circle without a grant).

---

## 1. Purpose

The Trusted Circle is the set of Trusted Contacts a Veteran has invited and that have accepted. Each contact is bound by **explicit Consent Grants**. Membership alone grants **no** visibility into Check-Ins, Support Signals, Support Cases, Service Requests, or location.

---

## 2. Lifecycle

`INVITED` → `ACCEPTED` → `SUSPENDED` | `REMOVED` | `REVOKED`

| State | Meaning |
|---|---|
| `INVITED` | Invite sent; contact has not accepted. No data visibility. |
| `ACCEPTED` | Contact accepted. Visibility and alerts still require grants. |
| `SUSPENDED` | Temporarily cannot receive or view. Grants remain but evaluate as deny while suspended. |
| `REMOVED` | Veteran ended the relationship. Future use denied. History kept. |
| `REVOKED` | Forced end (veteran or admin-with-audit). Future use denied. History kept. |

---

## 3. Invite / accept

1. Veteran creates a Trusted Contact with relationship label and invite email and/or phone.
2. System sends invite via Notifications only to the addresses the veteran provided. Template version recorded.
3. Accept binds a User if the contact enrolls, or records accept on the invite token.
4. Domain: `TRUSTED_CONTACT_INVITED`. Accept is audited. Consent is **not** implied by accept; the veteran must still issue grants (may be bundled in the same UI as separate explicit actions).

---

## 4. Relationship

`relationship_label` is required free or enumerated text (exact enum `DECISION_PENDING`). It is not a permission.

---

## 5. Granular permissions

Permissions live on Consent Grants, not on the membership row. Typical grants: `can_receive` levels; `can_view` objects. See [CONSENT.md](CONSENT.md).

A contact may have notification preferences ([NOTIFICATIONS.md](NOTIFICATIONS.md)). Preferences never grant consent.

---

## 6. Consent dependencies

- Alert at a signal level: `ACTIVE` grant `can_receive` + that level, and contact not `SUSPENDED`/`REMOVED`/`REVOKED`.
- View an object: `ACTIVE` grant `can_view` + that object.
- Red-state: [SAFETY.md](SAFETY.md) §3.3.
- Revoking a grant stops future use even if the contact remains `ACCEPTED`.
- Removing the contact denies all future use regardless of leftover grants (evaluate membership first).

---

## 7. Suspension, removal, revocation

- Veteran may suspend, remove, or revoke.
- SUAS-admin may revoke with audit (abuse / safety incident), not for convenience.
- Each transition writes Audit Events. If grants are bulk-revoked as part of removal, emit `CONSENT_REVOKED` per grant.

---

## 8. Responder access

Responders do not receive a roster of Trusted Contacts by default. A responder may see that an alert was sent (event) when they have case assignment. They may not read contact addresses without a documented incident process.

---

## 9. Events

`TRUSTED_CONTACT_INVITED`, `TRUSTED_CONTACT_ALERTED`, `CONSENT_GRANTED`, `CONSENT_REVOKED`.

---

## 10. Non-goals

- Broad visibility from membership
- Import of the veteran's full phone book ([PRIVACY.md](PRIVACY.md))
- Silent addition of contacts by responders

---

## 11. Testability

Critical suite: **trusted-circle visibility**.

- Accepted contact without grants sees no signal, no answers, no requests, no location.
- Grant then revoke → access ends.
- Suspended contact is not notified.
- Invite does not emit `TRUSTED_CONTACT_ALERTED`.

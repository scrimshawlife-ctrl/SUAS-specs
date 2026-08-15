# RESOURCES.md — Resource catalog (SUAS v0.1)

**Related:** [REFERRALS.md](REFERRALS.md), [DISPATCH.md](DISPATCH.md), [ADMIN.md](ADMIN.md), [ANALYTICS.md](ANALYTICS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md)

**Actors:** Organization Administrator (org-owned writes), SUAS System Administrator, Responder (read / verify), Veteran (limited public fields).

---

## 1. Purpose

A Resource is a first-class catalog entry describing an available support offering. Stale resources are an operational problem, not silently hidden as "smart."

---

## 2. Required fields

| Field | Notes |
|---|---|
| `organization` | Owning Organization |
| `service_name` | Human name |
| `category` | MVP: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT` |
| `eligibility` | Text; do not invent Medi-Cal or VA eligibility rules |
| `counties` | Pilot default includes Santa Clara; do not claim statewide coverage |
| `coverage_geometry` | Optional; type `DECISION_PENDING` |
| `hours` | Optional text/structured |
| `contact_method` | How a responder reaches the resource |
| `referral_method` | How a Referral is sent |
| `cost` | Optional; not a billing claim |
| `capacity` | Optional; not real-time unless verified |
| `active` | Boolean |
| `last_verified_at` | **Required** |
| `verification_source` | **Required** (who/what verified) |

---

## 3. Freshness bands (operational recommendations)

| Band | Age of `last_verified_at` | Recommendation |
|---|---|---|
| Fresh | < 30 days | Prefer in matching lists |
| Aging | 30–90 days | Show a freshness warning to the responder |
| Stale | > 90 days | Show a stale warning; do not auto-hide unless `active=false` |

These bands are **operational recommendations**, not legal coverage claims. A stale resource may still be selected; the warning is mandatory in the responder UI.

Critical suite: **stale-resource handling** ([TESTING.md](TESTING.md)).

---

## 4. Veteran-visible fields

When a Referral is being discussed with the Veteran, the veteran may see: `service_name`, `category`, `counties`, `hours` (if present), `cost` (if present). They do not see internal `contact_method` credentials, verification source internals, or other veterans' capacity notes.

---

## 5. Verification

Org-admin or SUAS-admin (or a responder with org permission) updates `last_verified_at` and `verification_source`. Periodic freshness report job: [ARCHITECTURE.md](ARCHITECTURE.md), [OPERATIONS.md](OPERATIONS.md).

Do not invent partner capabilities. Placeholder orgs: `PARTNER_ORG_001` ….

---

## 6. States

`ACTIVE` ↔ `INACTIVE` (`active` boolean). Referenced resources are not hard-deleted.

---

## 7. Non-goals

- Live county inventory feeds (`NOT_COMPUTABLE`)
- VA or Medi-Cal eligibility engines
- Generative resource invention
- Coverage claims beyond recorded fields

---

## 8. Testability

- Missing `last_verified_at` rejected.
- Stale band warning present.
- Inactive resources are not assignable.
- Category must be a known code.


---

## 9. Events

Resource create/update/verify/deactivate emit Audit Events. There is no Domain Event in the v0.1 catalog for resource changes (catalog is operational). Adding `RESOURCE_VERIFIED` would be a MINOR change to [EVENT_MODEL.md](EVENT_MODEL.md).

Stale-band computation is a read-time function of `last_verified_at` versus server now. It is not a stored state that can drift.

---

## 10. Actors (restated)

- Organization Administrator: write org-owned resources; verify.
- SUAS System Administrator: write any; verify; deactivate across orgs.
- Responder: read; verify if org permission granted.
- Veteran: limited public fields only when a Referral is in discussion ([§4](#4-veteran-visible-fields)).

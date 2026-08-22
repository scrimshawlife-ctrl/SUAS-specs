# RESOURCES.md — Resource catalog (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-005 preflight; not implementation authority.  
**Related:** [REFERRALS.md](REFERRALS.md), [DISPATCH.md](DISPATCH.md), [ADMIN.md](ADMIN.md), [ANALYTICS.md](ANALYTICS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md)

**Actors:** Organization Administrator, SUAS System Administrator, authorized Responder, Veteran (limited public fields).

---

## 1. Purpose

A Resource is a first-class catalog entry describing an available support offering. A Resource may be API-backed, referral-only, phone/email/manual, information-only, or currently unavailable.

Catalog freshness, provider transactional availability, and actual fulfillment are separate concepts.

---

## 2. Core fields

| Field | Rule |
|---|---|
| `organization` / provider linkage | owning/serving entity; placeholders until decisions close |
| `service_name` | human-facing name |
| `category` | MVP `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT` |
| `eligibility` | recorded criteria only; do not invent VA/Medi-Cal rules |
| `counties` | recorded coverage; pilot default may include Santa Clara |
| `coverage_geometry` | optional; D-014/physical representation open |
| `hours` | optional |
| `contact_method` | public/operational contact path, no credentials |
| `contact_method_kind` | optional scheme discriminator for `contact_method` (0.1.4), one of `PHONE`,`EMAIL`,`URL`,`FREEFORM`; absent/`FREEFORM` means unstructured text and no direct action is offered |
| `referral_method` | how a Referral is initiated |
| `cost` | optional/informational; not billing authority |
| `capacity` | optional snapshot; not presumed real-time |
| `integration_modes` | optional subset of `API`,`WEBHOOK`,`DEEP_LINK`,`PHONE`,`EMAIL`,`MANUAL_COORDINATION`,`NONE` |
| `active` | catalog eligibility for use |
| `last_verified_at` | required |
| `verification_source` | required |

Provider API credentials/secrets are never Resource fields.

`contact_method_kind` (0.1.4) is what lets the veteran-visible surface offer a direct call/email/web action for a recorded `PHONE`/`EMAIL`/`URL` value ([MVP_REFERENCE.md](MVP_REFERENCE.md) §8) instead of guessing a scheme from free text. The discriminator is additive and backward-compatible: a Resource with no kind (or `FREEFORM`) renders `contact_method` as text exactly as before. The vocabulary is owned here, not by a renderer.

---

## 3. Catalog freshness

Operational freshness bands:

| Band | Age | Required UI behavior |
|---|---|---|
| Fresh | <30 days | normal display |
| Aging | 30–90 days | warning to responder |
| Stale | >90 days | prominent stale warning; not silently hidden if still active |

These are operational recommendations, not legal/service-availability claims.

A stale Resource may still be selected through a responder/manual process if `active=true`, but the warning is mandatory.

---

## 4. Freshness is not live availability

`last_verified_at` and catalog `capacity` do not imply a provider can fulfill a request now.

Where a provider port supports live search/availability:

- live results are represented as normalized Provider Offers/attempt evidence;
- the catalog remains the durable discovery/resource record;
- a failed live availability check does not silently deactivate the Resource;
- provider outage does not erase the Resource;
- stale cached Provider Offers must not masquerade as current Resource truth.

For manual/referral-only providers, responder verification/contact remains valid.

---

## 5. Verification

Verification is an explicit audited action.

Rules:

1. Authorized verifier records `last_verified_at` and `verification_source`.
2. Replaying the same logical verification command is idempotent and does not create false multiple verification history.
3. Verification source text must not contain credentials/secrets or unrelated veteran data.
4. A provider webhook/status callback is not automatically a Resource verification unless the accepted verification policy explicitly treats that source as authoritative.
5. Verification of one Service Offer does not imply all services/capabilities of an Organization are verified.

Periodic freshness reporting is durable operational work but does not mutate Resource status merely because time passed.

---

## 6. Veteran-visible fields

Veteran-facing Resource views expose only appropriate public fields such as:

- `service_name`
- `category`
- public coverage/county
- public hours
- public cost when recorded
- public contact/referral instructions when policy allows

Do not expose:

- internal provider adapter identifiers/configuration;
- API credentials/secrets;
- verification-source internals not intended for public display;
- other veterans' demand/capacity notes;
- responder-only ranking/routing metadata.

---

## 7. Active/inactive state

Catalog state is `ACTIVE` ↔ `INACTIVE` (`active` boolean in current logical model).

- Inactive Resources are not assignable/selectable for new fulfillment/referrals unless a documented historical/recovery path applies.
- Referenced Resources are not hard-deleted.
- Provider integration failure alone does not automatically set `active=false`; manual/referral paths may still be viable.
- Deactivation is an explicit authorized action with audit.

---

## 8. Query/scaling contract

Resource search/list APIs are bounded/paginated and tenant/coverage scoped.

Search/filter may use:

- category;
- geography/coverage;
- active state;
- freshness band;
- integration mode/capability;
- provider/organization;
- responder-selected criteria allowed by accepted product rules.

Do not load the full Resource catalog into browser memory for normal matching. Search/ranking must not make clinical judgments or invent eligibility.

---

## 9. Events/audit

Resource create/update/verify/deactivate emit Audit Events.

There is no accepted Resource Domain Event in the current event catalog. Any future `RESOURCE_*` event requires explicit event-model reconciliation; implementation must not invent it silently.

Freshness band is computed from server time at read/report time and is not stored business state.

---

## 10. Non-goals

- assuming real-time inventory from catalog freshness;
- VA/Medi-Cal eligibility engines;
- generative resource invention;
- provider/API requirement for validity;
- vendor-specific catalog schema;
- storing provider credentials;
- silently deactivating resources because one adapter is down;
- claiming geographic/service coverage beyond recorded evidence.

---

## 11. Testability

- missing `last_verified_at`/verification source rejected where Resource activation requires them;
- stale warnings correct at boundary times;
- inactive Resource not newly assignable;
- provider outage does not erase/deactivate a still-valid manual Resource automatically;
- live Provider Offer expiration does not rewrite catalog verification;
- duplicate verification command is idempotent;
- veteran view excludes internal adapter/verification secrets;
- resource list/search is bounded and tenant scoped;
- unknown category/integration mode rejected.

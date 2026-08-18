# MVP_REFERENCE.md — Visual and interaction reference contract (SUAS v0.1)

**Status:** `draft` / `0.1.0` / not implementation authority until accepted and released.  
**Reference MVP:** `https://suasqrf.org/app/`  
**Related:** [PRODUCT.md](PRODUCT.md), [ONBOARDING.md](ONBOARDING.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [RESOURCES.md](RESOURCES.md), [TESTING.md](TESTING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

The existing SUAS MVP is the visual and interaction reference for production implementation. Production work must preserve its recognizable product identity, information hierarchy, principal actions, and low-friction operating model unless this specification explicitly requires a change.

The reference is not source-code authority and does not override safety, consent, privacy, authentication, accessibility, or canonical domain terminology.

When the reference MVP conflicts with an accepted SUAS specification, the specification wins. The implementation must preserve the closest safe interaction equivalent and record the deliberate divergence in the implementation PR.

---

## 2. Conformance classes

Every referenced surface or element MUST be classified during implementation review as one of:

| Class | Meaning |
|---|---|
| `MUST_MATCH` | Preserve visual identity, hierarchy, placement role, and recognizable interaction. Exact pixels are not required. |
| `MUST_PRESERVE_BEHAVIOR` | The underlying user goal and interaction sequence must remain recognizable, although production copy/auth/safety behavior may differ. |
| `MAY_EVOLVE` | Production may improve the element without changing product intent or creating cognitive overload. |
| `MUST_CHANGE_FOR_PRODUCTION` | The reference behavior conflicts with canonical security, consent, privacy, accessibility, or domain rules and must be replaced with the closest safe equivalent. |

No implementation may silently delete a `MUST_MATCH` or `MUST_PRESERVE_BEHAVIOR` element.

---

## 3. Visual principles derived from the MVP

Production SUAS MUST preserve these characteristics:

1. **Action-first hierarchy.** The primary user intent is visible immediately. Do not bury urgent support or service actions under dashboards or settings.
2. **Low cognitive load.** Prefer direct labels, large action targets, short routes, and progressive disclosure over dense administrative UI.
3. **Human-service orientation.** The interface should read as a coordination tool, not as an EHR, insurance portal, case-management bureaucracy, or generic SaaS dashboard.
4. **Fast role recognition.** Veteran, Responder/QRF, and Admin surfaces must be visibly distinct while sharing a common product system.
5. **Resource visibility.** Food, transportation, temporary shelter, and peer/human support must remain easy to discover.
6. **Operational immediacy.** Responder availability, alerts, cases/needs, communication, and quick resource access must remain prominent.
7. **Mobile-first use.** The primary veteran and responder paths must work comfortably on a phone without horizontal scrolling or precision interactions.
8. **No unnecessary visual complexity.** Production hardening must not turn the MVP into a dense enterprise console.

---

## 4. Canonical surface inventory

At minimum, production implementation must provide and visually validate the following surfaces.

| Surface | Primary role | Conformance |
|---|---|---|
| Action / landing surface | Veteran / visitor | `MUST_MATCH` product hierarchy; primary support and service choices remain immediately visible |
| Veteran enrollment / first-run | Veteran | `MUST_PRESERVE_BEHAVIOR`; production auth requirements may change exact fields/copy |
| Request-support flow | Veteran | `MUST_PRESERVE_BEHAVIOR` |
| Resource categories | Veteran / Responder | `MUST_MATCH` hierarchy; canonical category mapping applies |
| Resource list/detail | Veteran / Responder | `MUST_PRESERVE_BEHAVIOR` |
| Responder/QRF home | Responder | `MUST_MATCH` operating emphasis: availability, current needs/cases, fast actions |
| Responder availability | Responder | `MUST_PRESERVE_BEHAVIOR` |
| Alerts / active needs | Responder | `MUST_PRESERVE_BEHAVIOR`; canonical Support Case / Service Request semantics apply |
| Chat / communication | Veteran / Responder where authorized | `MUST_PRESERVE_BEHAVIOR`; consent and visibility rules apply |
| Admin overview | Organization Admin / SUAS Admin | `MAY_EVOLVE`; role scope must remain explicit |
| Persistent mobile navigation | Authenticated mobile users | `MUST_MATCH` simplicity and low navigation depth |

Exact screen names in implementation may differ only where necessary to preserve canonical terminology.

---

## 5. Product copy and domain mapping

The reference MVP may use user-facing labels that are broader than canonical domain categories. Production UI may use approachable display labels, but stored/API values MUST use canonical codes.

| Canonical domain code | Allowed MVP-style display concept | Production boundary |
|---|---|---|
| `FOOD` | Food | Food access coordination |
| `TRANSPORTATION` | Transportation / Ride | Ride or fare support to a stated destination/purpose |
| `SHELTER` | Housing / Room / Shelter | MVP means temporary shelter/accommodation coordination, not permanent housing placement |
| `PEER_SUPPORT` | Peer Support / QRF / Human Support | Human/peer support; not therapy or clinical counseling |
| `HOUSING` | Housing services | `FUTURE`; do not implement permanent-housing workflow under `SHELTER` |
| `HEALTHCARE_NAVIGATION` | Counseling / Health navigation | `FUTURE` unless explicitly promoted |
| `COMMUNITY` | Activities / Community | `FUTURE` unless explicitly promoted |

If a future category is shown for continuity with the reference MVP, it must be visibly non-operational (`COMING_SOON` or equivalent) and must not create hidden production workflow.

---

## 6. Known production divergences

### 6.1 Enrollment and identity

The reference MVP may imply enrollment without email or other identity channels. The production specification currently defines the MVP operational default as self-attestation plus a working email and/or phone through passwordless authentication while D-016 remains open.

Therefore:

- preserve the MVP's short, low-friction enrollment feel;
- do not preserve copy that contradicts the production auth contract;
- do not add DD-214 upload, VA identity API, or in-person proofing unless later specified;
- label the divergence `MUST_CHANGE_FOR_PRODUCTION`.

### 6.2 Safety and crisis copy

Reference safety wording is not automatically approved production safety copy. D-012 remains `DECISION_PENDING`. Visual placement and immediacy may be preserved, but production text must come from the accepted safety-copy artifact.

### 6.3 Case terminology

User-facing copy may say “need,” “request,” or “alert” where that improves comprehension, but API/database/state-machine concepts remain Support Case, Service Request, Support Signal, Referral, Assignment, Fulfillment, Follow-Up, and Settlement. UI copy must not collapse these into one technical object.

---

## 7. Responsive and accessibility requirements

Production implementation MUST:

- meet WCAG 2.2 AA unless a later accepted accessibility spec strengthens the requirement;
- preserve logical reading and focus order;
- support keyboard operation for responder/admin desktop use;
- provide visible focus states;
- avoid color as the only carrier of Support Signal meaning;
- maintain large touch targets on veteran/responder mobile actions;
- preserve text zoom/reflow without loss of action controls;
- provide accessible names for icons and navigation actions;
- avoid motion that blocks urgent operation.

Accessibility fixes are not visual drift.

---

## 8. Visual regression contract

Before a release candidate is production-ready, automated or repeatable screenshot-based comparison MUST cover at least:

1. action / landing surface;
2. veteran enrollment;
3. request-support flow;
4. resource categories;
5. resource list/detail;
6. responder/QRF home;
7. responder availability;
8. active needs/alerts;
9. chat/communication;
10. admin overview;
11. mobile navigation.

Each fixture records viewport, role, deterministic fixture data, reference revision/date, and conformance class.

Pixel equality is not the goal. Review must detect hierarchy drift, missing controls, changed navigation depth, unreadable density, broken responsive behavior, and unauthorized product redesign.

---

## 9. Acceptance gate: UI CONFORMANCE

`UI_CONFORMANCE = READY` only when:

- every required surface in §4 exists;
- each surface has a conformance classification;
- visual-regression fixtures exist for §8;
- no unresolved `MUST_MATCH` or `MUST_PRESERVE_BEHAVIOR` divergence remains;
- production-only divergences are documented and trace to canonical specs;
- canonical service categories are not silently redefined by UI copy;
- accessibility checks pass;
- veteran and responder critical paths remain low-friction on mobile.

Current status: `NOT_READY`.

---

## 10. Non-goals

- Pixel-perfect cloning of a prototype
- Freezing implementation technology or CSS framework
- Allowing prototype copy to override safety/auth/privacy rules
- Turning visual similarity into permission to copy incorrect domain semantics
- Redesigning SUAS into a generic enterprise dashboard during production hardening

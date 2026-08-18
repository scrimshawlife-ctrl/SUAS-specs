# SURFACES.md — Anonymous public front door vs identified opt-in platform

**Related:** [README.md](README.md), [PRODUCT.md](PRODUCT.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [ISLANDS.md](ISLANDS.md), [FENCE_POSTS.md](FENCE_POSTS.md)  
**Status:** `draft` / unreleased / not implementation authority

---

## 1. Purpose

Define the two SUAS surfaces introduced by Rev 3 build direction.

These are **two surfaces, not two modes**:

1. an **anonymous public front door**
2. the existing **identified opt-in platform**

The front door does not redefine the product. Santa Clara County v0.1 remains the identified coordination platform already defined in [PRODUCT.md](PRODUCT.md).

---

## 2. Surface definitions

| Surface | Identity | Allowed functions | Forbidden functions |
|---|---|---|---|
| **Anonymous public front door** | Public / anonymous | Show public-facing island identity, public resource list, geographic/service bounds, and crisis-resource information. Offer an affirmative path into the identified platform. | Create or update a Support Case, Service Request, Consent Grant, Trusted Contact, Settlement, or any other identified platform record. |
| **Identified opt-in platform** | Authenticated / identified when a record is created or updated | Enrollment, Check-In, consent management, Support Case and Service Request coordination, trusted-circle management, fulfillment, follow-up, settlement. | Silent use of public-front-door activity as if it were identified consent or an identified case record. |

---

## 3. Anonymous public front door

The anonymous public front door is a **public information surface**.

Rules:

1. No authentication is required to view the front door.
2. The front door may present:
   - island display identity
   - public crisis numbers
   - public resource list
   - served-population and geography text
   - an affirmative link or command to continue into the identified platform
3. The front door **never** creates or updates an identified platform record.
4. Temporary inputs on the front door are ephemeral until the person affirmatively crosses into the identified platform and confirms any record-writing action there.
5. Declining the crossing costs nothing. Public information remains available.

---

## 4. Identified opt-in platform

The identified opt-in platform is the existing SUAS v0.1 coordination system.

Rules:

1. All record-changing actions happen here, not on the anonymous front door.
2. Existing contracts still govern identified workflows:
   - [CHECKINS.md](CHECKINS.md)
   - [CONSENT.md](CONSENT.md)
   - [CASES.md](CASES.md)
   - [DISPATCH.md](DISPATCH.md)
   - [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md)
3. The platform must not treat front-door activity as a substitute for explicit consent, enrollment, or case creation.

---

## 5. Crossing contract

Crossing from the front door into the identified platform must be an **affirmative act by the person**.

Required behavior:

1. Crossing is triggered by an explicit user action.
2. The act of viewing the front door is not enrollment.
3. The act of searching public resources is not enrollment.
4. The act of seeing crisis information is not enrollment.
5. If the identified platform reuses any front-door input after crossing, the person must confirm it before SUAS writes a record.

---

## 6. Crisis-path rule

Both surfaces may show crisis-resource information.

- Showing crisis resources is not emergency dispatch.
- The front door and the platform both depend on [ISLANDS.md](ISLANDS.md) for resolved crisis numbers.
- If island configuration is missing or incomplete, both surfaces still show the hardcoded national 988 / Veterans Crisis Line fallback.

See [SAFETY.md](SAFETY.md).

---

## 7. Testability

| Check | Pass condition |
|---|---|
| Two surfaces | UI/spec structure distinguishes front door from identified platform without calling them "modes". |
| No silent crossing | Public browsing alone creates no Support Case, Service Request, Consent Grant, or Trusted Contact row. |
| No-cost decline | Declining enrollment/continue leaves public information available. |
| Confirm-before-write | Any front-door data reused after crossing still requires an identified-platform confirmation before write. |
| Crisis fallback | Both surfaces still show national 988 / Veterans Crisis Line fallback when island config resolution fails. |

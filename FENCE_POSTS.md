# FENCE_POSTS.md — Rev 3 fence-post outcomes (`G1`–`G14`)

**Related:** [README.md](README.md), [SURFACES.md](SURFACES.md), [ISLANDS.md](ISLANDS.md), [RIDES.md](RIDES.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SAFETY.md](SAFETY.md), [COMPLIANCE.md](COMPLIANCE.md), [DECISIONS.md](DECISIONS.md)  
**Status:** `draft` / unreleased / not implementation authority

---

## 1. Purpose

Record the missing Rev 3 fence-post contracts as **testable outcomes**, not as marketing copy and not as a second product definition.

Rev 3 is weekend build direction. This file is the draft contract surface for the parts that may be built against in `SUAS`. Conflicts return to [DECISIONS.md](DECISIONS.md) as `DECISION_PENDING`.

`G11` through `G14` are labeled **`NOT LEGAL ADVICE`**. They describe fail-closed contract boundaries and open questions, not legal conclusions.

---

## 2. Fence-post outcomes

| ID | Statement | Why | Which surface | Tests |
|---|---|---|---|---|
| `G1` | SUAS has **two surfaces**, not two modes: an anonymous public front door and the identified opt-in platform. | Keep Santa Clara v0.1 product identity while allowing a public entry surface. | Cross-surface | Verify [SURFACES.md](SURFACES.md) defines both surfaces and that only the identified platform can create operational records. |
| `G2` | Crossing from the public front door into the identified platform is an **affirmative act by the person**. Declining the crossing costs nothing. | Avoid coercing enrollment or silent record creation. | Cross-surface | Verify the crossing requires an explicit continue/enroll action; verify declining still leaves access to public information and crisis resources. |
| `G3` | The anonymous public front door never creates or updates a Support Case, Service Request, Consent Grant, Trusted Contact, or other identified platform record. | Preserve the public/identified boundary fail-closed. | Anonymous public front door | Verify front-door-only activity creates no platform row and emits no case/request/consent event. |
| `G4` | Ride-provider payloads contain only ride-operational fields. Provider payloads never include the veteran's reason for travel, Check-In answers, Support Signal basis, or distress framing. | Minimize disclosure to third-party transportation providers. | Identified opt-in platform | Verify the adapter contract in [RIDES.md](RIDES.md) excludes reason, Check-In, and distress fields from provider-bound payloads. |
| `G5` | Ride booking remains human-dispatched. Ride-adapter status is `booked` → `picked_up` → `completed` and does not replace the existing Service Request state machine. | Preserve human coordination and existing dispatch contracts. | Identified opt-in platform | Verify ride booking requires a human dispatcher/responder action; verify Service Request states still come from [DISPATCH.md](DISPATCH.md), not adapter statuses. |
| `G6` | Cost guardrails fail to a human. They never auto-deny a ride and never block the crisis-resource / 988 path. | Prevent opaque cost logic from becoming an emergency gate. | Identified opt-in platform | Verify cost guardrails route to human review; verify no automatic denial path is specified; verify crisis-resource surfacing remains available. |
| `G7` | Island configuration is **resolve-before-consume**: callers use a resolved island config snapshot, not partial ad-hoc values. | Prevent mixed or half-configured routing behavior. | Both surfaces | Verify unresolved required island fields block non-crisis operational use; verify config consumers read a resolved snapshot. |
| `G8` | Crisis-path fallback always includes hardcoded national **988** and **Veterans Crisis Line** entries even if island configuration is missing, stale, or unavailable. | Fail closed to a known national crisis path instead of an empty surface. | Both surfaces | Verify crisis-resource presentation never depends solely on island config and still shows national fallback entries when config resolution fails. |
| `G9` | Island isolation is enforced. Storage and reads are namespaced by `island_id`, and cross-island access is forbidden except by a separately audited administrative path. | Prevent data leakage across islands. | Identified opt-in platform | Verify [ISLANDS.md](ISLANDS.md) requires `island_id` scoping on reads/writes; verify [DECISIONS.md](DECISIONS.md) keeps `tenant_id` vs `island_id` ambiguity open. |
| `G10` | Funding rails and routing metadata may be configured per island, but they are descriptive fields only until released funding policy exists. | Prevent island config from silently becoming an eligibility engine. | Identified opt-in platform | Verify island funding fields are descriptive only and that [RIDES.md](RIDES.md) keeps [D-010](DECISIONS.md) open. |
| `G11` **`NOT LEGAL ADVICE`** | Trusted-circle graduated activation is driven only by what the person tells SUAS and deterministic consented rules. SUAS must never infer emotion, tone, or suicidality to expand notify scope. | Keep trusted-contact behavior inside explicit consent and deterministic safety rules. | Identified opt-in platform | Verify [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) forbids inferred-emotion expansion and requires consent evaluation at use time. |
| `G12` **`NOT LEGAL ADVICE`** | Peer-support / SB 903 questions remain register material and open decisions. The specs do not claim the statute is law, that SUAS is inside or outside it, or that SUAS is compliant. | Keep legal status out of product claims. | Cross-surface | Verify [COMPLIANCE.md](COMPLIANCE.md) adds a register row without a compliance claim. |
| `G13` **`NOT LEGAL ADVICE`** | Institutional-reporting, dual-enrollment, and minor-handling questions remain `DECISION_PENDING`. Until closed, collection stays at the minimum needed for the specified coordination workflow. | Avoid backfilling legal/policy assumptions into product data collection. | Identified opt-in platform | Verify [DECISIONS.md](DECISIONS.md) records these as open decisions; verify no new mandatory reporting flow is specified here. |
| `G14` **`NOT LEGAL ADVICE`** | Contracting-entity selection and volunteer-driver insurance/screening remain human governance decisions. SUAS must not encode them as settled fact. | Prevent the specs from guessing legal or operational authority. | Identified opt-in platform | Verify [DECISIONS.md](DECISIONS.md) records these as open decisions and [RIDES.md](RIDES.md) treats volunteer options as stub adapters only. |

---

## 3. Use

- Treat these as **draft** fence posts for implementation review during the event.
- Do not mark any of them `accepted` or `released` from this file.
- If later specs change a fence post, update the underlying contract file and then update this summary.

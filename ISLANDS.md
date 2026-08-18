# ISLANDS.md — Island configuration, fallback, and isolation

**Related:** [README.md](README.md), [SURFACES.md](SURFACES.md), [RIDES.md](RIDES.md), [ARCHITECTURE.md](ARCHITECTURE.md), [SECURITY.md](SECURITY.md), [DECISIONS.md](DECISIONS.md), [FENCE_POSTS.md](FENCE_POSTS.md)  
**Status:** `draft` / unreleased / not implementation authority

---

## 1. Purpose

Define the draft contract for an **island**: the configuration envelope used to present a public front door and route identified coordination behavior without creating a second product.

This file specifies:

- the minimum island config schema
- resolve-before-consume behavior
- crisis-path fallback
- storage and read isolation by `island_id`

Whether `island_id` is the same concept as the existing `tenant_id`, a parent of it, or orthogonal to it is **not decided**. See [DECISIONS.md](DECISIONS.md) `D-026`.

---

## 2. Minimum config schema

Every island config snapshot must include these contract fields:

| Field | Required | Meaning | Fail-closed rule |
|---|---|---|---|
| `island_id` | yes | Stable island identifier used for resolution and namespacing. | Missing `island_id` blocks normal island resolution. |
| `org_name` | yes | Operating organization name for the island. | Missing value blocks non-crisis display and routing. |
| `display_identity` | yes | Public-facing display identity shown on the surface. | Missing value blocks non-crisis display and routing. |
| `crisis_numbers` | yes | Island-specific crisis numbers and public crisis destinations. | Missing or incomplete values still merge in the hardcoded national 988 / Veterans Crisis Line fallback. |
| `resource_list` | yes | Curated public/resource-routing list used by the island. | Missing value blocks resource presentation that claims island specificity. |
| `geographic_bounds` | yes | Geographic area served by the island. Exact geometry format remains open. | Missing value blocks claims of island-specific geographic coverage. |
| `dispatcher_routing` | yes | Human routing target for identified coordination actions that require dispatch. | Missing value blocks non-crisis dispatch actions. |
| `funding_rails` | yes | Descriptive funding metadata only. Not an eligibility or settlement engine. | Missing value blocks island-specific funding display, not crisis fallback. |
| `served_population` | yes | Text describing who the island is intended to serve. | Missing value blocks island-specific service claims. |

This file does **not** decide the external storage shape, vendor, or geometry provider.

---

## 3. Resolve-before-consume

Consumers must not mix ad-hoc per-field reads.

Required behavior:

1. A caller resolves an island config snapshot for one `island_id`.
2. The caller consumes that resolved snapshot for the current operation.
3. If required non-crisis fields are unresolved, the operation fails closed to a human/configuration path instead of guessing.
4. Crisis-resource presentation still succeeds via the hardcoded national fallback even when other island fields are unresolved.

This prevents half-configured behavior such as:

- routing to one dispatcher while showing another identity
- claiming island-specific resources without a resolved resource list
- using funding text as if it were an automated eligibility rule

---

## 4. Crisis-path fallback

The crisis path must always include hardcoded national fallback entries for:

- **988**
- **Veterans Crisis Line**

Rules:

1. These fallback entries are part of the crisis path even when island config is unavailable.
2. Island-specific crisis numbers may be added on top of the fallback when resolved.
3. Missing island config must not yield an empty crisis-resource surface.
4. This is display/routing support only. It does not authorize automated emergency dispatch.

See [SAFETY.md](SAFETY.md).

---

## 5. Isolation and namespacing

`G9` requires island isolation.

Minimum contract:

1. Island-scoped reads and writes are namespaced by `island_id`.
2. Cross-island access is forbidden except by a separately audited administrative path.
3. Existing tenant isolation rules remain in force.
4. Until `D-026` is closed, implementations must not assume `tenant_id == island_id`.

This file does not replace existing `tenant_id` contracts in [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), or [SECURITY.md](SECURITY.md). It adds an additional draft boundary that must be made explicit before implementation relies on it.

---

## 6. Funding-rail boundary

Island `funding_rails` are **fields only** in this draft.

- They may describe available rails or contacts.
- They do not settle [D-010](DECISIONS.md).
- They do not auto-approve, auto-deny, or auto-route a veteran.

See [RIDES.md](RIDES.md) and [SETTLEMENT.md](SETTLEMENT.md).

---

## 7. Testability

| Check | Pass condition |
|---|---|
| Resolve-before-consume | Consumers use one resolved island snapshot per operation; no mixed partial reads. |
| Crisis fallback | Missing config still presents 988 and Veterans Crisis Line fallback entries. |
| Dispatch fail-closed | Missing `dispatcher_routing` blocks non-crisis dispatch actions instead of guessing a route. |
| Isolation | Reads and writes are scoped by `island_id`; cross-island access requires a separately audited path. |
| Funding boundary | `funding_rails` presence does not auto-decide eligibility, denial, or settlement. |

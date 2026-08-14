# DEPLOYMENT.md — Environments (SUAS v0.1)

**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [SECURITY.md](SECURITY.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

Define environment separation. Cloud/platform is `DECISION_PENDING` (D-001, D-005). This file does not lock a vendor.

---

## 2. Environments

| Environment | Purpose | Prod data? |
|---|---|---|
| `LOCAL` | Developer machines | No |
| `TEST` | Automated tests / CI | No (fixtures only) |
| `STAGING` | Pre-prod integration, synthetic users | No |
| `PRODUCTION` | Pilot operation | Yes |

Each environment has **separate**:

- Databases
- Secrets
- Notification providers / credentials (or a sink in LOCAL/TEST)
- Telemetry
- User data

---

## 3. Rules

1. No production data in LOCAL, TEST, or STAGING.
2. Notification providers in non-prod must not message real veterans. Use a sink or allow-listed lab addresses.
3. Telemetry in non-prod must not point at production datasets.
4. Schema migrations run per environment; PRODUCTION migrations require a released spec citation when they change contracts.
5. Backup PRODUCTION on a documented schedule (`DECISION_PENDING` interval). Restore testing is required and recorded.
6. Deploy does not redefine specification ([AGENTS.md](AGENTS.md)).

---

## 4. Unsettled

| Topic | Status |
|---|---|
| Cloud provider | D-001 `DECISION_PENDING` |
| Database hosting | D-005 `DECISION_PENDING` |
| Auth / SMS / email providers | D-002, D-003, D-004 |
| Exact backup interval | D-007 adjacent `DECISION_PENDING` |

---

## 5. Non-goals

- Multi-region active-active
- Naming a vendor as "the" platform
- Shared DB between STAGING and PRODUCTION

---

## 6. Testability

- Config review: four environment names exist; secrets do not overlap.
- Restore test procedure documented and last-run recorded before SPEC-014.

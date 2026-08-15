# AUTH.md — Authentication and session authorization (SUAS v0.1)

**Related:** [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md), [PRODUCT.md](PRODUCT.md), [API.md](API.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [ONBOARDING.md](ONBOARDING.md), [APIS.md](APIS.md)

**Actors:** Veteran, Responder, Organization Administrator, SUAS System Administrator, Trusted Contact (if enrolled), Service Provider user.

---

## 1. Purpose

Identify users, issue sessions, enforce MFA where required, invalidate sessions, and recover access without weakening least privilege.

Authentication is not authorization. Authorization is role + tenant + row + consent ([SECURITY.md](SECURITY.md), [CONSENT.md](CONSENT.md)).

First-run: MFA must be enrolled before any other SUAS-admin, org-admin, or responder privileged action. See [ONBOARDING.md](ONBOARDING.md).

---

## 2. Veteran authentication

Veterans use **passwordless** methods:

| Method | MVP |
|---|---|
| Magic link (email) | Required to support |
| Email OTP | Required to support |
| Phone OTP | Required where phone is present and an SMS provider exists |

Phone OTP depends on D-003 (SMS provider) = `DECISION_PENDING`. Until D-003 is closed, phone OTP is specified but may be unavailable in an environment. Email methods depend on D-004.

**Passwords:** not used for Veterans unless a later spec justifies them. Do not add a password field "just in case."

**Social login:** not assumed. Do not implement OAuth social providers unless a later spec adds them.

### 2.2 Enrollment identity (MVP, `INFERRED`; D-016 remains open)

Enrollment identity-proofing for the 25–50 Santa Clara County pilot is:

- self-attested veteran status, and
- a working email and/or phone completed via the passwordless methods in §2.

Not required for MVP enrollment: VA identity API, DD-214 upload, in-person proofing. Do not invent a VA partnership. Whether a later proofing step is required is D-016 `DECISION_PENDING`. See [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md).

This section is **enrollment**, not recovery. Lost-channel recovery in §7 remains a separate `DECISION_PENDING` process.

### 2.1 Challenge rules

- A challenge (magic-link token or OTP) is single-use, time-bounded (exact TTL `DECISION_PENDING`; implement as a documented constant, recommended starting point 15 minutes — `INFERRED` operational default, not a legal requirement).
- Challenges are stored hashed, not in plaintext.
- Rate-limit challenge issuance and verification per address and per IP ([SECURITY.md](SECURITY.md)).
- Successful verify issues a session and emits an Audit Event.
- Failed verify increments a counter; lockout threshold `DECISION_PENDING` (document the constant).

---

## 3. Responder and administrator authentication

Responders, Organization Administrators, and SUAS System Administrators:

1. Identify via email (magic link or email OTP). Passwords only if later justified; default is passwordless plus MFA.
2. **MFA is required** before a privileged session is fully elevated.
3. MFA factor type is `DECISION_PENDING` (TOTP is an acceptable default to specify without selecting a vendor).
4. Recovery: org-admin or SUAS-admin issues a one-time recovery path with audit. Self-service recovery must not bypass MFA without a second channel.

---

## 4. Sessions

- Server-side sessions (or equivalent revocable tokens). Opaque tokens. No long-lived bearer tokens in localStorage without a revocation story.
- Bind session to `user_id`, `tenant_id` context, issued_at, last_seen_at.
- Invalidation on: logout, password/MFA reset (if any), user `SUSPENDED`/`REVOKED`, membership `REVOKED` for org-scoped sessions, admin force-logout, idle timeout (`DECISION_PENDING` exact minutes).
- A revoked user cannot refresh or act. In-flight requests after revocation fail authorization.

---

## 5. Organization membership and roles

Authorization inputs:

- `User.status` must be `ACTIVE`.
- For org-scoped actions: an `OrganizationMembership` with `status=ACTIVE` and a role.
- Roles: `RESPONDER`, `ORG_ADMIN`, `SERVICE_PROVIDER_USER`, plus global `SUAS_ADMIN` (not an org membership).
- Least privilege: grant only the role needed. SUAS_ADMIN is not implicit for org-admins.

See [ADMIN.md](ADMIN.md).

---

## 6. Revoked-user behavior

When `User.status` becomes `REVOKED` or a membership becomes `REVOKED`:

1. All affected sessions are invalidated immediately.
2. Subsequent API calls return unauthenticated or forbidden (no data leakage in the error body).
3. The user cannot be assigned new cases.
4. Historical `actor_id` on events remains.
5. An Audit Event records the revocation.

---

## 7. Recovery

| Actor | Recovery path |
|---|---|
| Veteran | New magic link / OTP to the enrolled email or phone. If both channels are lost: SUAS-admin identity-proofing process (`DECISION_PENDING` exact steps; must be audited). |
| Responder / Org-admin | Org-admin or SUAS-admin reset with MFA re-enrollment. |
| SUAS-admin | Dual-control recovery `DECISION_PENDING`; at minimum, audited break-glass with post-review. |

---

## 8. Events

- Audit: login success/fail, logout, challenge issued, MFA enrolled/removed, session invalidated, recovery issued, user revoked.
- Domain: `VETERAN_ENROLLED` is emitted from enrollment, not from every login.

---

## 9. Non-goals

- Social login
- Veteran passwords (unless later justified)
- Shared responder logins
- Impersonation without an audited break-glass spec (not in MVP)

---

## 10. Testability

See [TESTING.md](TESTING.md) AUTH gate:

- Veteran can complete magic-link and email OTP.
- Responder cannot obtain an elevated session without MFA.
- Revoked user sessions die.
- Org-admin cannot authenticate into another org.
- Rate limits reject tight challenge loops.
- `complete-enrollment` does not require a VA identity API, DD-214, or in-person proofing step (D-016 MVP default).

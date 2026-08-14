# TRUSTED_CIRCLE.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Purpose
The trusted circle is an explicitly permissioned set of people connected to a veteran for support coordination purposes. It is not an open social graph.

## Membership model
Each trusted-circle membership should record:
- veteran owner
- member identity and relationship label
- approved contact methods
- consent-backed permissions
- activation and expiration timestamps
- notes about language or availability when observed

## Permission examples
- receive check-in reminders
- receive follow-up updates
- provide availability information
- receive limited service-request notifications
- participate in peer-support coordination

## Consent relationship
Trusted-circle access is subordinate to granular consent grants in [CONSENT.md](CONSENT.md). A veteran may authorize one person to receive follow-up notices while preventing broader case visibility.

## Notification behavior
Notifications to trusted-circle members must be channel-specific, purpose-limited, and revocable. If consent is revoked, future notifications and disclosures must stop.

## Membership changes
- add member with explicit authorization
- pause member access
- revoke member access
- expire access by policy or time window
- audit all changes

## Constraints
- No inferred family or friend relationships without explicit input.
- No bulk notifications outside allowed grant scope.
- No emergency dispatch behavior.

Related specs: [CONSENT.md](CONSENT.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [PRIVACY.md](PRIVACY.md), [FOLLOWUP.md](FOLLOWUP.md).

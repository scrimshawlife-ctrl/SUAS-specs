# DATA_MODEL.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Data model posture
[OBSERVED] PostgreSQL is the canonical store. The schema sketches below are normative shapes, not production migrations.

## Core tables
### veterans
- `id UUID PRIMARY KEY`
- `external_reference TEXT NULL` `[DECISION_PENDING via ADR-006]`
- `display_name TEXT` `[OBSERVED when user supplied]`
- `preferred_contact_channel TEXT NULL`
- `county TEXT` `[OBSERVED]`
- `created_at TIMESTAMPTZ`
- `updated_at TIMESTAMPTZ`

### check_ins
- `id UUID PRIMARY KEY`
- `veteran_id UUID NOT NULL`
- `submitted_by_user_id UUID NULL`
- `submitted_at TIMESTAMPTZ NOT NULL`
- `channel TEXT NOT NULL` `[OBSERVED]`
- `mood_self_report TEXT NULL` `[OBSERVED]`
- `needs_text TEXT NULL` `[OBSERVED]`
- `location_text TEXT NULL` `[OBSERVED, sensitive]`
- `trusted_circle_contact_requested BOOLEAN NULL` `[OBSERVED]`
- `raw_payload JSONB NOT NULL`

### support_signals
- `id UUID PRIMARY KEY`
- `check_in_id UUID NULL`
- `support_case_id UUID NULL`
- `level TEXT NOT NULL CHECK (level IN ('GREEN','YELLOW','ORANGE','RED'))`
- `computed_reason_codes TEXT[] NOT NULL` `[INFERRED]`
- `human_override_level TEXT NULL` `[OBSERVED by authorized human]`
- `computed_at TIMESTAMPTZ NOT NULL`

### support_cases
- `id UUID PRIMARY KEY`
- `veteran_id UUID NOT NULL`
- `state TEXT NOT NULL CHECK (state IN ('OPEN','TRIAGED','ASSIGNED','ACTIVE','FOLLOWUP','RESOLVED','CLOSED'))`
- `primary_signal_id UUID NULL`
- `owner_user_id UUID NULL`
- `opened_at TIMESTAMPTZ NOT NULL`
- `closed_at TIMESTAMPTZ NULL`

### service_requests
- `id UUID PRIMARY KEY`
- `support_case_id UUID NOT NULL`
- `category TEXT NOT NULL`
- `state TEXT NOT NULL CHECK (state IN ('CREATED','SUBMITTED','TRIAGED','MATCHING','ASSIGNED','ACCEPTED','IN_PROGRESS','FULFILLED','CONFIRMED','CLOSED','CANCELLED','DECLINED','EXPIRED','UNFULFILLABLE','ESCALATED'))`
- `requested_start_at TIMESTAMPTZ NULL`
- `urgency_code TEXT NOT NULL`
- `details JSONB NOT NULL`

## Additional tables
- `consent_grants`
- `trusted_circle_members`
- `referrals`
- `assignments`
- `fulfillments`
- `follow_ups`
- `settlements`
- `notifications`
- `audit_events`

## Decision-pending items
- `ADR-006`: external identity/reference strategy.
- `ADR-007`: enum tables versus database check constraints.
- `ADR-008`: JSONB boundaries versus fully normalized detail tables.
- `ADR-009`: soft delete policy versus immutable closure with archival markers.

## Data handling rules
Field values that come directly from veterans, responders, or coordinators should retain provenance so downstream logic can distinguish `[OBSERVED]` facts from `[INFERRED]` system derivations. Sensitive location or contact detail access must remain consent-gated and role-gated.

Related specs: [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [CONSENT.md](CONSENT.md), [EVENT_MODEL.md](EVENT_MODEL.md), [PRIVACY.md](PRIVACY.md), [DECISIONS.md](DECISIONS.md).

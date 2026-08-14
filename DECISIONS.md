# DECISIONS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## ADR index
All unresolved decisions are tracked here. Status remains `DECISION_PENDING` unless explicitly updated.

### ADR-001 - Application framework and language stack
Status: DECISION_PENDING
Decision: Select the primary implementation language and application framework for the modular monolith.
Rationale: Architecture, hiring, deployment, and test tooling depend on this choice.
Affected specs: [ARCHITECTURE.md](ARCHITECTURE.md), [API.md](API.md), [DEPLOYMENT.md](DEPLOYMENT.md).

### ADR-002 - Background job runner and scheduler choice
Status: DECISION_PENDING
Decision: Choose the mechanism for reminders, notifications, follow-up scheduling, and background workflows.
Rationale: Notification and follow-up flows rely on durable scheduled execution.
Affected specs: [ARCHITECTURE.md](ARCHITECTURE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [FOLLOWUP.md](FOLLOWUP.md).

### ADR-003 - Deployment topology and hosting boundary
Status: DECISION_PENDING
Decision: Choose hosting model and operational boundary for the modular monolith and PostgreSQL.
Rationale: Deployment, security, and operations depend on this choice.
Affected specs: [ARCHITECTURE.md](ARCHITECTURE.md), [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md).

### ADR-004 - Event bus or in-process eventing approach
Status: DECISION_PENDING
Decision: Determine whether domain events remain in-process or require a dedicated event transport.
Rationale: Event delivery guarantees and integration boundaries depend on this choice.
Affected specs: [ARCHITECTURE.md](ARCHITECTURE.md), [EVENT_MODEL.md](EVENT_MODEL.md).

### ADR-005 - Evidence artifact storage approach
Status: DECISION_PENDING
Decision: Choose how fulfillment evidence or related files are stored and referenced.
Rationale: Security, privacy, and deployment design depend on artifact handling.
Affected specs: [ARCHITECTURE.md](ARCHITECTURE.md), [SECURITY.md](SECURITY.md), [FULFILLMENT.md](FULFILLMENT.md).

### ADR-006 - External identity/reference strategy
Status: DECISION_PENDING
Decision: Determine how external identifiers map to veterans and partner-facing records.
Rationale: Data import, interoperability, and privacy depend on a stable reference model.
Affected specs: [DATA_MODEL.md](DATA_MODEL.md), [API.md](API.md).

### ADR-007 - Enum tables versus database check constraints
Status: DECISION_PENDING
Decision: Decide how canonical states and taxonomies are enforced in PostgreSQL.
Rationale: Schema evolution and integrity enforcement depend on this choice.
Affected specs: [DATA_MODEL.md](DATA_MODEL.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md).

### ADR-008 - JSONB boundaries versus normalization
Status: DECISION_PENDING
Decision: Decide which service-request details remain flexible JSONB and which require relational tables.
Rationale: Reporting, validation, and migration complexity depend on this choice.
Affected specs: [DATA_MODEL.md](DATA_MODEL.md), [ANALYTICS.md](ANALYTICS.md).

### ADR-009 - Soft delete versus immutable closure markers
Status: DECISION_PENDING
Decision: Determine whether records are soft-deleted or only state-closed and archived.
Rationale: Audit integrity and privacy operations are affected.
Affected specs: [DATA_MODEL.md](DATA_MODEL.md), [PRIVACY.md](PRIVACY.md).

### ADR-010 - MFA requirement and enrollment policy
Status: DECISION_PENDING
Decision: Determine MFA scope by role and environment.
Rationale: Authentication assurance and usability depend on this choice.
Affected specs: [AUTH.md](AUTH.md), [SECURITY.md](SECURITY.md).

### ADR-011 - SSO versus local identity provider
Status: DECISION_PENDING
Decision: Choose whether SUAS authenticates through a third-party provider or native accounts.
Rationale: Admin overhead, security posture, and user onboarding depend on this choice.
Affected specs: [AUTH.md](AUTH.md), [DEPLOYMENT.md](DEPLOYMENT.md).

### ADR-012 - RBAC-only versus RBAC plus attribute constraints
Status: DECISION_PENDING
Decision: Choose the authorization enforcement model.
Rationale: Consent and case scope may require more than coarse roles.
Affected specs: [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [API.md](API.md).

### ADR-013 - Session token format and lifetime
Status: DECISION_PENDING
Decision: Decide token/session model, lifetimes, and revocation semantics.
Rationale: Security, mobile access, and auditability depend on this choice.
Affected specs: [AUTH.md](AUTH.md), [API.md](API.md).

### ADR-014 - Canonical consent taxonomy and grant naming
Status: DECISION_PENDING
Decision: Define the authoritative list of consent grant types and scopes.
Rationale: Consent UX, enforcement, and audit depend on consistent taxonomy.
Affected specs: [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### ADR-015 - Evidence format for verbal consent capture
Status: DECISION_PENDING
Decision: Determine how verbal consent is represented and verified.
Rationale: Some pilot interactions may not be digital-first, but auditability is still required.
Affected specs: [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md).

### ADR-016 - Consent expiration defaults and renewal prompts
Status: DECISION_PENDING
Decision: Define default expiration windows and renewal behavior for grants.
Rationale: Overly broad or stale grants create risk.
Affected specs: [CONSENT.md](CONSENT.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### ADR-017 - Key management and encryption architecture
Status: DECISION_PENDING
Decision: Choose encryption key strategy and secret-management integration.
Rationale: Protection of sensitive data depends on it.
Affected specs: [SECURITY.md](SECURITY.md), [DEPLOYMENT.md](DEPLOYMENT.md).

### ADR-018 - Audit log immutability mechanism
Status: DECISION_PENDING
Decision: Decide how audit history resists tampering.
Rationale: Trust in administrative and safety workflows depends on auditable integrity.
Affected specs: [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md), [EVENT_MODEL.md](EVENT_MODEL.md).

### ADR-019 - Vulnerability management cadence and tooling
Status: DECISION_PENDING
Decision: Establish scanning, patching, and review cadence.
Rationale: Ongoing security hygiene needs explicit ownership.
Affected specs: [SECURITY.md](SECURITY.md), [OPERATIONS.md](OPERATIONS.md).

### ADR-020 - Secure evidence artifact storage model
Status: DECISION_PENDING
Decision: Define access-controlled storage and retention for evidence artifacts.
Rationale: Evidence can contain sensitive details.
Affected specs: [SECURITY.md](SECURITY.md), [FULFILLMENT.md](FULFILLMENT.md), [PRIVACY.md](PRIVACY.md).

### ADR-021 - Closed-record retention period
Status: DECISION_PENDING
Decision: Set retention window for closed case data.
Rationale: Privacy and operational review needs must be balanced.
Affected specs: [PRIVACY.md](PRIVACY.md), [SETTLEMENT.md](SETTLEMENT.md).

### ADR-022 - Audit retention period
Status: DECISION_PENDING
Decision: Set retention window for audit and access records.
Rationale: Audit data often needs longer retention than operational views.
Affected specs: [PRIVACY.md](PRIVACY.md), [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md).

### ADR-023 - HIPAA applicability assessment process
Status: DECISION_PENDING
Decision: Define how HIPAA applicability will be assessed and recorded.
Rationale: The bootstrap spec must not imply a settled regulatory position without review.
Affected specs: [PRIVACY.md](PRIVACY.md), [STATUS.md](STATUS.md), [SPEC_AUDIT.md](SPEC_AUDIT.md).

### ADR-024 - De-identification rules for analytics exports
Status: DECISION_PENDING
Decision: Define suppression, aggregation, and release thresholds for analytics outputs.
Rationale: Aggregate reporting must not re-identify veterans.
Affected specs: [PRIVACY.md](PRIVACY.md), [ANALYTICS.md](ANALYTICS.md).

### ADR-025 - Support signal rule governance
Status: DECISION_PENDING
Decision: Define change control, review, and deployment process for signal rules.
Rationale: Deterministic safety logic needs explicit governance.
Affected specs: [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [ADMIN.md](ADMIN.md).

### ADR-026 - Responder capacity and ranking policy
Status: DECISION_PENDING
Decision: Determine how responder capacity and matching priority are calculated.
Rationale: Fairness, speed, and transparency depend on this policy.
Affected specs: [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md).

### ADR-027 - Follow-up default cadence policy
Status: DECISION_PENDING
Decision: Set default timing rules for follow-up by service category.
Rationale: Follow-up cadence affects workload and veteran experience.
Affected specs: [FOLLOWUP.md](FOLLOWUP.md), [CHECKINS.md](CHECKINS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### ADR-028 - Veteran confirmation requirement for settlement
Status: DECISION_PENDING
Decision: Determine when veteran confirmation is required before final closure.
Rationale: Closure quality and practicality depend on this rule.
Affected specs: [SETTLEMENT.md](SETTLEMENT.md), [FOLLOWUP.md](FOLLOWUP.md).

### ADR-029 - Notification channel scope
Status: DECISION_PENDING
Decision: Determine whether additional channels beyond SMS, push, and in-app are in MVP scope.
Rationale: Channel surface area affects complexity and consent burden.
Affected specs: [NOTIFICATIONS.md](NOTIFICATIONS.md), [PILOT.md](PILOT.md).

### ADR-030 - Quiet-hours and suppression policy
Status: DECISION_PENDING
Decision: Define time-of-day and frequency limits for outbound notifications.
Rationale: Notification safety and respectfulness depend on these rules.
Affected specs: [NOTIFICATIONS.md](NOTIFICATIONS.md), [CONSENT.md](CONSENT.md).

### ADR-031 - Notification provider selection
Status: DECISION_PENDING
Decision: Choose delivery providers for SMS, push, and in-app messaging.
Rationale: Reliability, compliance posture, and cost depend on this choice.
Affected specs: [NOTIFICATIONS.md](NOTIFICATIONS.md), [DEPLOYMENT.md](DEPLOYMENT.md).

### ADR-032 - Admin approval workflow for sensitive policy changes
Status: DECISION_PENDING
Decision: Define review and approval requirements for changing signal, consent, or retention policies.
Rationale: High-impact settings need controlled governance.
Affected specs: [ADMIN.md](ADMIN.md), [SAFETY.md](SAFETY.md), [PRIVACY.md](PRIVACY.md).

### ADR-033 - Break-glass access policy
Status: DECISION_PENDING
Decision: Determine whether break-glass access exists and how it is controlled.
Rationale: Emergencies may pressure access controls, but auditability must remain intact.
Affected specs: [ADMIN.md](ADMIN.md), [AUTH.md](AUTH.md), [SECURITY.md](SECURITY.md).

### ADR-034 - API framework and routing stack
Status: DECISION_PENDING
Decision: Select framework and routing model for `/v1/` endpoints.
Rationale: Consistency with the application stack and testing strategy is required.
Affected specs: [API.md](API.md), [ARCHITECTURE.md](ARCHITECTURE.md).

### ADR-035 - REST-only versus mixed API style
Status: DECISION_PENDING
Decision: Determine whether the implementation uses REST only or supplements it with other interfaces.
Rationale: Client simplicity and long-term flexibility depend on the choice.
Affected specs: [API.md](API.md), [TESTING.md](TESTING.md).

### ADR-036 - Optimistic concurrency and versioning strategy
Status: DECISION_PENDING
Decision: Determine how concurrent edits to cases, requests, and consent grants are protected.
Rationale: Multi-actor coordination requires conflict control.
Affected specs: [API.md](API.md), [DATA_MODEL.md](DATA_MODEL.md).

### ADR-037 - Event sourcing versus audit-log-first architecture
Status: DECISION_PENDING
Decision: Determine whether events are the source of truth or a derived audit stream.
Rationale: Storage, replay, and analytics architectures depend on this choice.
Affected specs: [EVENT_MODEL.md](EVENT_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [ANALYTICS.md](ANALYTICS.md).

### ADR-038 - Event retention and replay policy
Status: DECISION_PENDING
Decision: Determine how long events are kept and whether replay is supported.
Rationale: Analytics and recovery behavior depend on retention.
Affected specs: [EVENT_MODEL.md](EVENT_MODEL.md), [OPERATIONS.md](OPERATIONS.md).

### ADR-039 - Containerization and packaging approach
Status: DECISION_PENDING
Decision: Determine packaging and release artifact strategy.
Rationale: Deployment reproducibility and operations simplicity depend on it.
Affected specs: [DEPLOYMENT.md](DEPLOYMENT.md), [ARCHITECTURE.md](ARCHITECTURE.md).

### ADR-040 - Infrastructure as code strategy
Status: DECISION_PENDING
Decision: Choose how environments are provisioned and tracked.
Rationale: Repeatable deployment and auditability require explicit infrastructure management.
Affected specs: [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md).

### ADR-041 - Observability stack
Status: DECISION_PENDING
Decision: Select logging, metrics, and tracing approach.
Rationale: Operations and incident response need dependable visibility.
Affected specs: [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

### ADR-042 - On-call ownership and escalation model
Status: DECISION_PENDING
Decision: Define who is on call and how operational escalation works.
Rationale: A pilot cannot rely on ad hoc response.
Affected specs: [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [PILOT.md](PILOT.md).

### ADR-043 - Incident ticketing and runbook platform
Status: DECISION_PENDING
Decision: Choose operational systems for incident tracking and runbook management.
Rationale: Repeatability and accountability depend on it.
Affected specs: [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

### ADR-044 - Severity matrix ownership
Status: DECISION_PENDING
Decision: Define who maintains the incident severity matrix and when it changes.
Rationale: Consistent classification requires governance.
Affected specs: [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [OPERATIONS.md](OPERATIONS.md).

### ADR-045 - Incident communication and paging approach
Status: DECISION_PENDING
Decision: Choose channels and procedures for urgent incident communication.
Rationale: Human response depends on reliable communication.
Affected specs: [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### ADR-046 - Approved AI tooling boundaries
Status: DECISION_PENDING
Decision: Define which AI-assisted features, if any, are permitted for non-safety-critical use.
Rationale: Automation needs explicit policy boundaries to avoid scope creep.
Affected specs: [AGENTS.md](AGENTS.md), [SAFETY.md](SAFETY.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### ADR-047 - Machine-generated summary retention and disclosure policy
Status: DECISION_PENDING
Decision: Define how generated summaries are labeled, stored, and disclosed.
Rationale: Transparency and privacy obligations require explicit handling.
Affected specs: [AGENTS.md](AGENTS.md), [PRIVACY.md](PRIVACY.md), [EVENT_MODEL.md](EVENT_MODEL.md).

Related specs: [README.md](README.md), [ARCHITECTURE.md](ARCHITECTURE.md), [PRIVACY.md](PRIVACY.md), [ROADMAP.md](ROADMAP.md).

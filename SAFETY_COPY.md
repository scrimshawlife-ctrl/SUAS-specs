# SAFETY_COPY.md — Approved crisis/safety copy (SUAS v0.1.5, D-012)

**Lifecycle:** `released` via [RELEASE_MANIFEST-0.1.5.md](RELEASE_MANIFEST-0.1.5.md)
**Authority:** closes D-012 ([RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md)); governed by [SAFETY.md](SAFETY.md)
**Related:** [SAFETY.md](SAFETY.md), [MVP_REFERENCE.md](MVP_REFERENCE.md) §7.3, [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [PRODUCT.md](PRODUCT.md), [COMPLIANCE.md](COMPLIANCE.md)

---

## 0. Status and boundaries

This artifact is the **approved on-screen safety/crisis copy** for SUAS, closing D-012. It is the wording implementations render where [SAFETY.md](SAFETY.md) requires approved copy; implementations MUST NOT invent alternative crisis wording presented as official.

Boundaries that remain in force (do not weaken by copy):

- SUAS is a coordination platform and is **not an emergency-response service**. It performs no automated emergency dispatch, diagnosis, or suicidality determination ([SAFETY.md](SAFETY.md) §2, rule 14 of the implementation authority).
- The approved crisis destinations are **911** (immediate danger / medical emergency) and the **988 Suicide & Crisis Lifeline** (call or text; Veterans reach the Veterans Crisis Line through 988). These are the only destinations this release authorizes; no other hotline number, URL, or agency contact may be shipped as official without a further released decision.
- Approving this copy is **not** production-operating approval. Real operation with real veteran data remains gated by SPEC-018 and the environment rules in [ENVIRONMENT.md](ENVIRONMENT.md); LOCAL/TEST/STAGING render this copy against synthetic data only.
- Dialable destinations render as `911` and `988` (e.g. `tel:911`, `tel:988` / SMS to `988`). No tracking parameters are appended to a crisis destination.

The copy blocks below are the released text. Minor layout/markup may adapt to the surface, but wording, destinations, actions, and status labels are authoritative.

---

## 1. Veteran-facing copy

### 1.1 Crisis Entry

**Need help right now?**

If you are in immediate danger, have a medical emergency, or believe someone may be seriously harmed, **call 911 or go to the nearest emergency department.**

SUAS coordinates practical support. It is **not an emergency service** and cannot replace police, fire, EMS, or emergency medical care.

If you are experiencing suicidal thoughts, severe emotional distress, or a mental health crisis, call or text the **988 Suicide & Crisis Lifeline at 988**.

Primary actions:

- **Call 911**
- **Call or text 988**
- **Continue with SUAS support**

### 1.2 Veteran Crisis Option

**You do not have to handle this alone.**

If you are a Veteran in crisis or concerned about a Veteran, call or text **988** for immediate crisis support.

SUAS can still help coordinate practical needs such as transportation, temporary lodging, food, and connection to participating organizations when those services are available.

Primary actions:

- **Contact crisis support**
- **Request practical support**

### 1.3 Crisis Triage Question

**Is anyone in immediate danger?**

Choose the answer that best describes the situation.

- **Yes — immediate danger** — Someone may be seriously injured, is experiencing a medical emergency, or may harm themselves or someone else.
- **No — urgent support needed** — The situation is serious, but there is no immediate threat to life or safety.
- **Not sure** — I cannot determine whether the situation is immediately dangerous.

### 1.4 Immediate Danger State

**Emergency services should handle this first.**

SUAS is not equipped to dispatch emergency responders.

Call **911** now if there is an immediate threat to life or safety.

For suicidal thoughts or severe emotional distress, call or text **988**.

You can return to SUAS afterward for practical support.

- Primary action: **Call 911**
- Secondary action: **Return to SUAS**

### 1.5 Unsure State

**If you are unsure, treat immediate safety first.**

If you believe there may be an immediate threat to life or safety, call 911.

For suicidal thoughts, emotional distress, or a mental health crisis, call or text **988**.

If there is no immediate danger, SUAS can help identify the practical support you need.

Primary actions:

- **Call 911**
- **Call or text 988**
- **Continue with SUAS**

### 1.6 Urgent but Non-Emergency State

**Tell us what you need.**

SUAS can help coordinate urgent practical support when participating resources are available.

Select one or more needs:

- Transportation
- Temporary lodging
- Food
- Clothing or essential supplies
- Medical transportation
- Benefits or service navigation
- Connection to a local organization
- Contact with a trusted person
- Other urgent need

Action: **Find support**

### 1.7 Request Submitted

**Your request has been received.**

SUAS will use the information you provided to identify available support.

**This does not mean that assistance has been confirmed.**

If your situation becomes an emergency while you are waiting, call 911.

For a suicide or emotional crisis, call or text **988**.

- Status: `REQUEST RECEIVED`
- Next: We will show updates here as participating responders or organizations become available.

### 1.8 No Provider Found

**We could not confirm support yet.**

No participating provider has accepted this request.

This does **not** mean that help is unavailable outside SUAS.

If the situation has become dangerous or life-threatening, call 911.

For suicidal thoughts or emotional distress, call or text **988**.

You can also:

- Expand the search area
- Try another support category
- Contact a local organization directly
- Update your request

### 1.9 Provider Located

**A support provider is available.**

A participating provider has responded to your request.

Review the details before continuing.

**Important:** A provider response is not the same as emergency dispatch. If the situation becomes life-threatening, call 911.

Actions:

- **View provider**
- **Contact provider**
- **Cancel request**

### 1.10 Ride / Transport Crisis Copy

**Need transportation urgently?**

SUAS may be able to connect you with participating transportation resources.

Do **not** use this service instead of an ambulance when emergency medical treatment may be required.

If you have severe injuries, difficulty breathing, loss of consciousness, significant bleeding, or another medical emergency, call 911.

Actions:

- **Request transportation**
- **Call 911 instead**

### 1.11 Emergency Lodging Copy

**Need a safe place tonight?**

SUAS can search participating lodging resources for temporary availability.

Availability is not guaranteed until a provider confirms the placement.

If you are currently in immediate physical danger, call 911 before arranging lodging.

Action: **Find emergency lodging**

### 1.12 Food / Essentials Copy

**Need food or essential supplies?**

Tell us what is needed and where support is required.

SUAS will search available participating resources.

A submitted request does not guarantee fulfillment.

Action: **Request essentials**

### 1.13 Trusted Contact Copy

**Contact someone you trust?**

You can choose to contact a trusted person and tell them that you need support.

SUAS will not describe your situation beyond the information you choose to share.

Actions:

- **Choose contact**
- **Not now**

### 1.14 Cancel Request

**Cancel this request?**

Canceling stops SUAS from seeking additional support for this request.

It does not cancel services that you have already arranged directly with another provider.

Actions:

- **Keep request active**
- **Cancel request**

---

## 2. Banners and footer

### 2.1 Crisis Banner — Compact

**Immediate danger? Call 911.**
For suicide or emotional crisis support, call or text **988**.
SUAS provides practical support coordination and is not an emergency-response service.

### 2.2 Crisis Banner — Minimal

- **Emergency:** Call 911
- **Crisis support:** Call or text 988
- **Practical support:** Continue with SUAS

### 2.3 Persistent Footer

SUAS coordinates community support. It does not provide emergency medical care or emergency dispatch.

For immediate danger, call 911. For suicide or emotional crisis support, call or text **988**.

---

## 3. Operator-side copy

### 3.1 High-risk request

**HIGH-RISK REQUEST**

This request contains information that may indicate an immediate safety risk.

**Do not represent SUAS as an emergency-response service.**

If the requester reports immediate danger, direct them to emergency services according to the approved escalation procedure.

Do not mark the request `RESOLVED` solely because an emergency referral was provided. Record the disposition accurately.

### 3.2 Crisis escalation recorded

- Status: `ESCALATION RECORDED`

The crisis escalation has been documented.

This status records an action taken by the operator. It does not confirm that emergency services responded or that the requester is safe.

### 3.3 Unable to verify safety

- Status: `SAFETY STATUS UNKNOWN`

SUAS does not have enough verified information to determine the requester's current safety status.

Do not infer resolution.

---

## 4. Language rules for the crisis surface

Use:

- "Request received"
- "Provider responded"
- "Support available"
- "Safety status unknown"
- "We could not confirm support"
- "Call emergency services"
- "Practical support"

Do not use:

- "Help is on the way" unless dispatch is verified
- "You are safe now"
- "We have you"
- "Rescue is coming"
- "Emergency team dispatched" unless SUAS has authoritative dispatch confirmation
- "Everything will be okay"
- "Your request is guaranteed"
- "We notified the authorities" unless that action actually occurred

---

## 5. Core product principle — state truthfulness

**Never allow interface language to imply a stronger real-world intervention than the system can prove occurred.**

For crisis and practical-support workflows the following conditions are distinct and MUST be represented separately, never conflated, and the surface MUST NOT display a later state until the evidence for it exists:

`REQUESTED ≠ ACCEPTED ≠ DISPATCHED ≠ ARRIVED ≠ RESOLVED`

- `REQUESTED` — the veteran submitted a request; SUAS has recorded it. Nothing about provider action is implied.
- `ACCEPTED` — a participating provider has accepted the request (recorded provider acceptance evidence).
- `DISPATCHED` — the accepted **practical-support** provider (e.g. a ride or lodging placement) reports it is en route/actioned. This is **never** emergency-services dispatch: SUAS performs no 911/PSAP dispatch (rule 14), so this state is reachable only from verified support-provider evidence and never implies emergency response.
- `ARRIVED` — verified evidence that the support provider/resource reached the veteran.
- `RESOLVED` — the coordination is complete on recorded evidence; an emergency referral alone does not make a request `RESOLVED`, and safety is not inferred (see §3).

These conditions must be part of the crisis/support domain model, not copy alone, so the UI cannot tell a veteran that help is coming when the system only knows a request was submitted. Mapping onto the canonical Service Request / Fulfillment state machines ([DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md)) and the QRF label→fact truthfulness table ([MVP_REFERENCE.md](MVP_REFERENCE.md) §7.2) is the implementation's responsibility; each condition maps to a recorded fact and is surfaced only when that fact exists.

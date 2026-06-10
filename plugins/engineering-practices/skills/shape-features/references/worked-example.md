<objective>
A fully worked example of the feature requirements template, showing the quality bar for every section. Read this when producing output in the `shape-new-feature` workflow, or when reviewing requirements and needing a reference for what "good" looks like.
</objective>

# Feature: Self-service order refund

## Participants & Decisions

| Participant | Role | Present for | What they pushed back on | What was deferred |
|---|---|---|---|---|
| P. Alvarez | Product owner | All sessions | Treating "user requests a refund" as a one-shot transaction - pushed to model it as a request that can be auto-approved, manually reviewed, or rejected, with each state explicit | Refunds initiated from the order-history page on mobile (web first) |
| J. Okafor | Lead developer | Sessions 1-2 | Synchronous call to the payment provider in the submission flow - flagged latency risk when the provider is slow | Automatic retries on payment-provider failures (deferred to follow-up) |
| M. Tanaka | QA / tester | Session 2 | Original happy-path AC silent on what the user sees while the refund is being processed | Load-testing concurrent refund requests during peak-sale events |
| A. Becker | Support team lead | Session 1 | The original draft routed all refunds through manual review; argued for an auto-approval lane for low-risk cases to reduce queue pressure | Auto-approval for refunds over the configured threshold (will be re-evaluated after launch metrics) |
| E. Ramos | Fraud / risk lead | Session 2 | "Eligible order" must be checked against fraud signals before any auto-approval, not just against order age | Behavioural fraud scoring (out of scope for the first version - relies on rules-based signals for now) |

**Date of conversation:** 2026-03-09 (session 1, product + support), 2026-03-12 (session 2, fraud joining)
**Author (who drafted this document):** P. Alvarez
**Stakeholders consulted (not in the room, but whose views are reflected):** Payments engineering (on payment-provider refund API behaviour), Security (on authorisation boundaries), Privacy (on what refund-related data is retained and for how long).

## Problem Statement

**Who** has this problem: Users who have placed an order and want to request a refund, currently the only route is to contact support.

**What** problem they face: Today, a user must email support or open a chat, describe the order and the reason, and wait for an agent to evaluate the request and process it. First-contact-to-refund-issued averages 36 hours, and the support team handles approximately 1,200 refund tickets per week - the vast majority of which are simple cases (recent orders, common reasons) that follow a predictable resolution path.

**Why** it matters (impact if unsolved): Support cost on refund tickets is approximately 380 agent-hours per week. User satisfaction at the refund step is low - the top verbatim complaint in post-refund surveys is "how long it took to get my money back." Internal data suggests that ~60% of refund requests could be auto-approved against existing eligibility rules without manual review.

## Success Criteria

**Measurable outcome:** Within 8 weeks of launch, at least 50% of refund requests are submitted through the self-service flow (vs. via support), and of those, at least 80% are auto-approved on first attempt. Median time-to-refund-issued for auto-approved cases drops below 5 minutes. Support refund-ticket volume drops by at least 30% over the same window.

## User Stories

### Story 1: User submits a self-service refund request

**As a** user with an eligible recent order **I want to** request a refund through the order-history page **so that** I can resolve the issue without contacting support and waiting for a reply.

**Estimated size:** 2 days.

#### Acceptance Criteria

```gherkin
Scenario: User submits a refund request for an auto-approval-eligible order
  Given I have an order placed within the last 30 days
  And the order is in a refundable state (paid, not yet shipped, or delivered within return window)
  And no fraud signals are present on my account
  When I submit a refund request with a stated reason
  Then my refund request is created and assigned a reference
  And the request is auto-approved
  And the refund is queued for processing against the original payment method
  And I am shown an acknowledgement with the expected timing
  And the refund event is written to the order journal with actor, timestamp, and reason

Scenario: User submits a refund request that requires manual review
  Given I have an order placed within the last 30 days
  And the order is in a refundable state
  And one or more fraud signals are present on my account, OR the requested amount exceeds the auto-approval threshold
  When I submit a refund request with a stated reason
  Then my refund request is created and assigned a reference
  And the request is queued for manual review
  And I am shown an acknowledgement that a support agent will follow up within 24 hours
  And the request appears in the support queue with the signals that flagged it

Scenario: User submits a refund request without selecting a reason
  When I attempt to submit a refund request without selecting a reason
  Then my submission is not accepted
  And I am shown which fields are missing
  And my partial progress is preserved so I can complete the submission later

Scenario: User tries to refund an order that is not eligible
  Given I have an order that is not eligible (already refunded, outside the return window, or in a non-refundable state)
  When I attempt to submit a refund request
  Then my submission is blocked
  And I am shown the reason
  And I am offered the option to contact support if I disagree

Scenario: Payment provider rejects the refund during processing
  Given my refund request has been approved
  When the payment provider rejects the refund (closed card, frozen account, provider outage)
  Then the refund request is moved to a "needs intervention" state, not auto-failed
  And a support agent is notified
  And I am notified that there is a delay and that an agent will be in touch
  And the provider error is logged for operational review

Scenario: User submits multiple refund requests for the same order
  Given I have an open refund request for an order
  When I attempt to submit a second refund request for the same order
  Then my second submission is blocked
  And I am shown the status of my existing request
  And I am offered the option to add a comment to the existing request instead
```

#### Non-Functional Requirements

- **Performance:** Refund submission (from final-confirmation click to acknowledgement displayed) completes within 3 seconds at the 95th percentile. Auto-approval evaluation completes within 5 seconds at the 95th percentile; if exceeded, the request falls through to the manual-review queue rather than blocking the user.
- **Security:** Only the authenticated account owner may submit a refund request for an order belonging to that account. All refund events (submission, approval, rejection, payment-provider response) are audit-logged with actor, timestamp, request reference, and source channel. The fraud-signals evaluation happens server-side; the client receives only the outcome, never the signals themselves.
- **Accessibility:** Flow meets WCAG 2.1 AA. All steps are reachable via keyboard navigation; error messages are announced to screen readers; colour is not the sole carrier of meaning for field-level errors.
- **Observability:** Emit metrics for submissions-per-hour, auto-approval rate, manual-review queue depth, median and p95 time-to-acknowledgement, and drop-off rate at each step of the flow. Alert on auto-approval rate below 50% sustained over an hour (suggests rule miscalibration), payment-provider rejection rate above 5% in any 15-minute window, and any p95 latency above the stated targets sustained over 15 minutes.
- **Data handling:** Refund-request data is retained for 7 years from request closure (matches existing order-record retention). The stated reason is treated as user-provided free text - not displayed to other users, not used for ML training without separate consent.

---

### Story 2: Support agent reviews a flagged refund request

**As a** support agent **I want to** review refund requests that were flagged for manual review **so that** I can resolve them quickly with full context instead of opening a ticket from scratch.

**Estimated size:** 1.5 days (after Story 1 has shipped, since this story consumes the queue Story 1 produces).

#### Acceptance Criteria

```gherkin
Scenario: Agent reviews a refund request flagged for fraud signals
  Given I am an authenticated support agent with refund-review permission
  And there is a refund request in the manual-review queue
  When I open the request
  Then I see the order details, the user's stated reason, and the signals that flagged it
  And I can approve, reject, or request more information from the user
  And whatever I do is written to the request journal with my agent ID and timestamp

Scenario: Agent approves a flagged refund request
  Given I am reviewing a flagged request
  When I approve the request with a note explaining why
  Then the refund is queued for processing against the original payment method
  And the user is notified that their refund has been approved
  And my approval (with the note) is written to the request journal

Scenario: Agent rejects a refund request
  Given I am reviewing a flagged request
  When I reject the request with a stated reason
  Then no refund is issued
  And the user is notified, with my stated reason, that the request has been declined
  And the user is offered the option to escalate the decision
  And my rejection (with the reason) is written to the request journal

Scenario: Agent requests more information from the user
  Given I am reviewing a flagged request
  When I request more information with a stated question
  Then the request is moved to a "waiting on user" state
  And the user is notified, with my question, and given a way to respond
  And the request appears on the user's order page as "awaiting your response"
  And if the user does not respond within 14 days, the request is auto-closed and the user is notified
```

#### Non-Functional Requirements

- **Performance:** Loading a flagged request, including its signals and order context, completes within 2 seconds at the 95th percentile.
- **Security:** Only support agents with the refund-review permission can view or act on flagged requests. Every agent action is audit-logged with agent ID, timestamp, action, and any stated reason or note. Agents cannot self-approve refunds on orders belonging to their own account (anti-fraud control).
- **Accessibility:** Agent console meets WCAG 2.1 AA; all decision actions are reachable via keyboard.
- **Observability:** Emit metrics for queue depth by signal type, median and p95 time-to-first-agent-action, agent approval/rejection ratios, and "waiting on user" timeout rates.
- **Data handling:** Agent notes and stated reasons are retained for 7 years from request closure (same as the request itself). Agent notes are not visible to the user; agent-stated rejection reasons are.

---

## Risks & Unknowns

| Risk / Unknown | Impact | Mitigation |
|---|---|---|
| The fraud-signals rules are based on historical assumptions; they may flag too aggressively in production and starve the auto-approval lane | Auto-approval rate falls below target; support queue does not shrink as projected | Run a shadow evaluation against the last 90 days of refund tickets before launch; tune thresholds based on observed false-flag rate; build the alert on auto-approval rate so the calibration problem surfaces fast in production |
| The payment provider's refund API behaves differently for orders paid via gift cards or store credit vs. cards; this has not been fully characterised | Refund failures or partial refunds users do not understand | Run the payment-provider spike below before build starts |
| Users may submit a request and then place a new order before the refund is processed, leading to balance/timing edge cases the design did not consider | User confusion; possible double-charge or double-refund scenarios | Capture and surface this as an open question in the build kickoff; the payment team to confirm whether order placement is blocked while a refund is processing, or whether both can be in flight |
| Self-review trust level: this spec was primarily drafted by P. Alvarez; Security and Privacy were consulted asynchronously and did not attend either session | A security or privacy concern may surface late | Before build starts, schedule a 30-minute review with Security and Privacy on the data-handling and audit-logging sections specifically |

## Assumptions

| Assumption | Needs Verification By | Status |
|---|---|---|
| The payment provider's refund API supports partial refunds and exposes a stable error taxonomy that lets us distinguish "transient" from "permanent" failures | Before Story 1 build starts | Unverified - payments team to confirm |
| The current fraud-signals service can be called inline from the refund flow without exceeding the latency target | Before Story 1 build starts | Unverified - fraud team to confirm |
| Order-state machine already exposes the "in a refundable state" check as a queryable property, not just internal logic | Sprint planning | Unverified - order-platform team to confirm |
| Support agents already have a permission model that can carry the refund-review permission without new infrastructure | Before Story 2 build starts | Unverified - identity team to confirm |

## Spikes Needed

| Question to Answer | Time-box | Acceptance Criteria for Spike |
|---|---|---|
| How does the payment provider's refund API behave across the payment methods our orders actually use (cards, gift cards, store credit, third-party wallets), and what is its error taxonomy? | 2 days | Written matrix of payment method × refund-API behaviour × error codes we can rely on, plus a verdict on whether the "needs intervention" scenario in Story 1 can be triggered correctly for each failure mode. |
| Can the fraud-signals service be called inline at the latency we need, or does this story need a cached / async path? | 1 day | Latency measurement under representative load, with a verdict and (if cached / async is needed) a sketch of the cache invalidation rules. |

## Dependencies

| Dependency | Type (hard/soft) | Owner | Status |
|---|---|---|---|
| Payment provider refund API | Hard | Payments engineering | Available; behaviour under investigation (see spike above) |
| Fraud-signals service | Hard | Fraud team | Available; latency under investigation (see spike above) |
| Order-state queryable "refundable" property | Hard | Order-platform team | Not confirmed |
| Notification service (in-app + email) for user-facing refund status updates | Hard | Platform team | Existing; no new contract change expected |
| Support agent console (extends existing console with the flagged-refund queue) | Hard | Support tools team | Existing; new view to be built as part of Story 2 |
| Audit-logging service | Hard | Platform team | Existing; no new contract change expected |

## Multi-audience sign-off

**The core four:**

- [x] **Product owner** (P. Alvarez) - The value statement matches intent; behaviours described for self-service submission and agent review are the behaviours agreed across both sessions.
- [x] **Developer** (J. Okafor) - Work can be started for Story 1 without further clarification once the two spikes have answers; NFR latency targets and observability expectations are explicit; the "needs intervention" state is a first-class part of the design, not an afterthought.
- [x] **Tester** (M. Tanaka) - Test cases can be derived from the ACs alone; the happy path, the manual-review path, the eligibility-blocked path, the payment-provider-failure path, and the duplicate-request path are all present; Story 2 covers all four agent actions (approve, reject, request info, timeout).
- [x] **AI agent** - Preconditions (order state, fraud signals, request state), triggers (submission and agent actions), and outcomes (refund issued, queued, blocked, or pending) are unambiguous. The places we don't know yet (payment-provider error taxonomy, fraud-signals latency) are captured as named spikes, not left as assumptions for the AI to invent.

**The people who pay for our mistakes:**

- [x] **Operator / support** - The manual-review queue is a first-class deliverable, not a side effect; agent actions and their downstream notifications are all audit-logged; the "needs intervention" state explicitly notifies an agent rather than silently failing. Support team lead (A. Becker) was in session 1 and is named on the auto-approval threshold decision.
- [x] **Security / privacy** - Authorisation boundaries are explicit (user can only refund their own orders, agent cannot self-approve their own); audit logging covers every state transition; data retention is stated. Security and Privacy were consulted asynchronously and a pre-build review is scheduled (see Risks).
- [x] **End-user (not the buyer)** - The user is the buyer in this feature, so this audience collapses into the product-owner perspective. The "user has placed a new order while a refund is in flight" scenario is flagged as an open question rather than silently designed one way.
- [x] **Downstream teams / integrators** - Payments, fraud, order-platform, identity, support-tools, and notification teams are all named as dependencies with current status. The two spikes target the two teams whose contract is least understood.

**Sign-off mode:**

- [x] **Conversation sign-off** - the boxes above were confirmed by the named individuals across sessions 1 and 2. The requirement is trusted for Story 1 build once the two spikes have answers and the Security/Privacy pre-build review has been held.
- [ ] Self-reviewed

<what-to-notice>

**Why this example works as a reference:**

- **The story is sliced into two distinct deliverables**, not one mega-feature. Story 1 (user submission + auto-approval + manual-review queue) ships first and produces the queue that Story 2 consumes. Each story is small enough to fit the 2-day rule.
- **The acceptance criteria cover the happy path AND the genuine edge cases** - duplicate submissions, ineligible orders, payment-provider failures, missing reasons. Each one is its own scenario, not an inverted happy path.
- **The "needs intervention" state for payment-provider failures is a first-class scenario**, not a footnote. This is the kind of edge case that, left implicit, becomes a production incident on day three.
- **The NFRs are concrete and measurable**: "3 seconds at the 95th percentile" not "should be fast." The alert thresholds are named, so the support runbook can be written without guessing.
- **Risks, assumptions, spikes, and dependencies are all separate sections** doing different work. Risks describe what could go wrong; assumptions name what we are treating as true; spikes are time-boxed investigations to reduce uncertainty; dependencies name the contracts we rely on.
- **The participants table captures who pushed back on what**, so six months from now a reader can trace why the auto-approval lane exists (support team's push) and why fraud signals are checked inline (risk team's push) instead of guessing.
- **The "needs intervention" state is named in plain language** before the implementer has to invent a state machine. This is the difference between a requirement and a wishlist.
- **The end-user audience is honestly handled**: in this feature, the end-user IS the buyer, so the audience collapses. The template explicitly asks for this kind of decision rather than letting "End-user" be a blank box.

**What to notice about what is *not* here:**

- No claim that the system is "generic" or "future-proof." Each behaviour is written for the case in scope, not for hypothetical extensions.
- No vague "N/A" on NFRs. Where something does not apply, the reason is stated; where it does, the requirement is measurable.
- No silently deferred edge cases. The "user places a new order during a pending refund" scenario is flagged as an open question rather than designed one way without a conversation.

</what-to-notice>
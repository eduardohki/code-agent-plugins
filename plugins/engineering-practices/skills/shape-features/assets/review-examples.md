<objective>
Concrete examples for the review workflow: what findings look like, what rewrites look like, and what AI-authorship failure modes look like in real requirements. Read this file when performing the review workflow to calibrate your output quality.
</objective>

<sample-findings>

## Example: Producing findings (Steps 4-5)

Below is a requirement as submitted, followed by the findings and rewrite the review workflow should produce.

### Submitted requirement

```
Story: Partner Self-Service Portal Registration

As a partner, I want to register for the portal.

Acceptance criteria:
- Partner can create an account
- Email validation
- Password must be strong
- Error handling for duplicate registrations
```

### Findings

**Finding 1 (Critical): No value statement.**

- **What:** "As a partner, I want to register for the portal" - missing the "so that" clause.
- **Why it matters:** Without a value statement, nobody can evaluate whether self-service registration is the right solution. Is the goal to reduce partner onboarding time? To gate access to an API key? To comply with a procurement policy? Each answer leads to different design decisions. Without it, the developer will build generic registration, the tester won't know what success looks like beyond "form works," and an AI agent will implement something plausible that may not match the actual business need.
- **What to do instead:** Add the value: "As a partner, I want to register for the portal so that I can manage my integrations and view usage reports without contacting the operations team."

**Finding 2 (Critical): Acceptance criteria are not testable.**

- **What:** "Email validation" and "Password must be strong" - these are labels, not scenarios.
- **Why it matters:** "Email validation" could mean checking format, verifying the domain is in an approved list, or sending a confirmation link. "Strong" could mean 8 characters, 12 characters, requiring symbols, or checking against breach databases. Every implementer - human or AI - will interpret these differently. The tester has no basis for writing test cases.
- **What to do instead:** Write concrete scenarios:

```gherkin
Scenario: Registration with a valid email and password
  Given I am an unregistered partner
  When I submit a registration with a valid work email and a password of at least 12 characters
  Then my account is created in a pending-approval state
  And I receive a verification email

Scenario: Registration with an already-registered email
  Given an account with email "partner@example-corp.com" already exists
  When I submit a registration with email "partner@example-corp.com"
  Then I am informed that an account with this email already exists
  And no duplicate account is created

Scenario: Registration with a weak password
  Given I am an unregistered partner
  When I submit a registration with a password shorter than 12 characters
  Then I am informed that the password does not meet the minimum length requirement
  And no account is created
```

**Finding 3 (Major): No edge cases or unhappy paths.**

- **What:** Only 4 bullet points, all describing the happy path or basic validation. No scenarios for: verification link expiration, concurrent registration from the same organization, registration from a domain not in the approved partner list, rate limiting.
- **Why it matters:** Edge cases discovered during implementation become ad-hoc decisions. The developer picks whatever seems reasonable. The AI agent invents behavior. Nobody validates these choices because they were never specified. The result is inconsistent, untested behavior in production.
- **What to do instead:** Add scenarios for at least: verification link expiration, duplicate concurrent submissions, and unrecognised partner domain.

**Finding 4 (Major): No non-functional requirements.**

- **What:** No mention of performance, security, accessibility, observability, or data handling.
- **Why it matters:** Partner registration is a security-critical flow - only authorised partners should access the system. Without explicit NFRs, there's no guidance on: password hashing, rate limiting, audit logging of registration attempts, WCAG compliance, or how long unverified accounts persist before cleanup.
- **What to do instead:** Add NFRs:
  - Performance: Registration completes within 3 seconds. Verification email sent within 30 seconds.
  - Security: Passwords hashed with bcrypt (cost factor 12+). Registration rate-limited to 5 attempts per IP per hour. Failed attempts logged. New accounts held in pending-approval state until manually approved by operations.
  - Accessibility: Registration form meets WCAG 2.1 AA.
  - Data handling: Unverified accounts purged after 72 hours. Partner email is PII - handle per privacy policy.

### Rewritten requirement

```
Story: Partner Portal Registration

As a partner, I want to register for the portal so that I can manage my
integrations and view usage reports without contacting the operations team.

Estimated size: 1.5 days
```

```gherkin
Scenario: Successful registration
  Given I am an unregistered partner
  When I submit a valid work email and a password of at least 12 characters
  Then my account is created in a pending-approval state
  And I receive a verification email with a link valid for 24 hours

Scenario: Registration with an already-registered email
  Given an account with email "partner@example-corp.com" already exists
  When I submit a registration with email "partner@example-corp.com"
  Then I am informed that an account with this email already exists
  And no duplicate account is created
  And the response time is indistinguishable from a successful registration
    (to prevent email enumeration)

Scenario: Registration with a weak password
  Given I am an unregistered partner
  When I submit a password shorter than 12 characters
  Then I am informed of the minimum length requirement
  And no account is created

Scenario: Verification link has expired
  Given I registered but did not verify within 24 hours
  When I click the verification link
  Then I am informed the link has expired
  And I am offered the option to resend a new verification email

Scenario: Rapid registration attempts from the same source
  Given 5 registration attempts have been made from my IP in the last hour
  When I attempt another registration
  Then I am informed that registration is temporarily unavailable
  And the rate-limited attempt is logged
```

Non-functional requirements:
- **Performance:** Registration completes within 3 seconds. Verification email sent within 30 seconds.
- **Security:** Passwords hashed with bcrypt (cost factor 12+). Rate-limited to 5 attempts per IP per hour. Failed and successful attempts audit-logged. New accounts held in pending-approval state until operations approves.
- **Accessibility:** Registration form meets WCAG 2.1 AA. All form fields have associated labels. Errors announced to screen readers.
- **Observability:** Emit metrics for registrations per hour, verification completion rate, and rate-limit triggers. Alert if verification email delivery failure rate exceeds 5%.
- **Data handling:** Unverified accounts purged after 72 hours. Partner email is PII - handle per privacy policy.

ASSUMPTION: Email delivery service can handle current registration volume without a dedicated queue - needs verification.

</sample-findings>

<ai-authorship-examples>

## Example: AI-authorship failure modes (Step 3)

Below is a requirement that an AI agent generated from the prompt "Write requirements for a support ticket document upload feature." Each annotation shows a specific AI failure mode.

### AI-generated requirement (with annotations)

```
Story: Support Ticket Document Upload

As a user, I want to upload documents so that I can attach them to my
support ticket.

Acceptance criteria:

Scenario: Successful document upload
  Given I am logged in
  When I upload a valid document
  Then the document is stored successfully
  And I see a success message

Scenario: Failed document upload                      ← FORMULAIC EDGE CASE
  Given I am logged in                                  (mechanical inversion of
  When I upload an invalid document                     the happy path - what is
  Then the upload fails                                 an "invalid document"?
  And I see an error message                            Too large? Wrong type?
                                                        Password-protected PDF?
                                                        Each is a different
                                                        scenario.)

Scenario: Upload with no file selected                 ← FORMULAIC EDGE CASE
  Given I am logged in                                  (another inversion - real
  When I attempt to upload without selecting a file     edge cases would be:
  Then I see a validation message                       network drops mid-upload,
                                                        ticket already closed,
                                                        concurrent uploads to
                                                        the same ticket,
                                                        malicious file content)

Non-functional requirements:
- Performance: The system should handle document      ← PLAUSIBLE-SOUNDING VAGUENESS
  uploads efficiently.                                  ("efficiently" is not a number)
- Security: Uploaded documents should be stored       ← PLAUSIBLE-SOUNDING VAGUENESS
  securely.                                             ("securely" - encrypted at rest?
                                                        virus-scanned? subject to
                                                        a retention schedule?)
- The system supports files up to 25MB.               ← ASSUMPTION BACKFILL
                                                        (where did 25MB come from?
                                                        Nobody specified this. The AI
                                                        picked a plausible number.)
- Accessibility: The upload feature should be         ← SYMMETRICAL COMPLETENESS
  accessible to all users.                              (generic boilerplate - doesn't
                                                        commit to WCAG level, doesn't
                                                        mention alternatives for
                                                        users using assistive
                                                        technology)
```

**What's missing entirely (the "Missing I don't know" failure mode):**
- What document types are allowed? PDF, JPEG, TIFF? The AI didn't ask - it assumed "valid document" is self-evident.
- Who can view uploaded documents? The assigned agent only, or the user too?
- Must documents be linked to a specific ticket? The AI didn't flag this as an unknown.
- How long are documents retained? No mention of a document retention schedule.
- What happens if a document is uploaded to a ticket that is already closed?

A human writing this would likely say "I'm not sure what the size limit should be" or "we need to decide on retention." The AI presented everything as settled.

</ai-authorship-examples>

<human-authorship-examples>

## Example: Human-authorship failure modes (Step 3)

AI is not the only author whose output needs suspicion. Humans have their own pattern of failures - different from the AI ones, but no less common. When a human-authored requirement arrives for review, check for these.

### Human-authored requirement (with annotations)

Below is a requirement produced by a product owner after a meeting with an operations manager who wants "a way to see how the team is handling support tickets." Each annotation shows a specific human failure mode.

```
Story: Support Agent Performance Dashboard

As a support manager, I want to see how the team is handling     ← STAKEHOLDER ECHO
tickets, so that I can manage them better.                         (verbatim quote from
                                                                    the stakeholder; no
                                                                    translation into
                                                                    observable behavior.
                                                                    "How the team is
                                                                    handling tickets" is
                                                                    not a requirement - it
                                                                    is a feeling the
                                                                    stakeholder wants to
                                                                    have. What does
                                                                    "handling" mean?
                                                                    Cycle time? First-
                                                                    response time?
                                                                    Customer
                                                                    satisfaction?
                                                                    Pick one - or ask.)

Estimated size: 5 days (per tech lead's estimate)                ← SENIORITY-AS-EVIDENCE
                                                                    ("per tech lead" is not
                                                                     an argument - it is a
                                                                     deflection of scrutiny.
                                                                     What was the reasoning?
                                                                     Is the estimate for the
                                                                     same scope the PO is
                                                                     describing? Often it is
                                                                     not.)

Acceptance criteria:

Scenario: Viewing the dashboard
  Given I am logged in as a support manager
  When I navigate to /dashboard/agent-performance               ← SNEAKED IMPLEMENTATION
  Then I see a bar chart of open tickets per agent                (the URL path, the widget
  And I see a line chart of average cycle time per agent          type, and the data series
  And the data is loaded from the support_analytics_cube          source are all
                                                                   implementation decisions
                                                                   leaking into the AC.
                                                                   What if we render on
                                                                   mobile? What if we move
                                                                   to a different data
                                                                   warehouse? The behavior
                                                                   should be "the manager
                                                                   can compare agents on
                                                                   workload and cycle time"
                                                                   - HOW is for the builder.)

Scenario: Filtering by date range
  Given I am on the dashboard
  When I select "last 7 days"
  Then the charts update

Scenario: Filtering by ticket type
  Given I am on the dashboard
  When I select a ticket type from the dropdown
  Then the charts update                                        ← FALSE COMPLETENESS
                                                                   (these are the same
                                                                    scenario with different
                                                                    nouns - they test the
                                                                    same behavior: "filters
                                                                    cause a refetch."
                                                                    The missing scenarios
                                                                    ask: what if the filter
                                                                    combination returns no
                                                                    data? What if the
                                                                    manager has no agents
                                                                    assigned? What if the
                                                                    date range crosses a
                                                                    reporting-period
                                                                    boundary?)

Scenario: No data                                              ← ABSENT USER
  Given there are no open tickets                                 (written from the system's
  When I view the dashboard                                       perspective. There are
  Then I see an empty state                                       almost always tickets - the
                                                                   question is whether the
                                                                   manager might misread an
                                                                   empty dashboard as
                                                                   "all handled" vs.
                                                                   "data not loaded." That
                                                                   interpretation is absent.)

Out of scope:
- Individual ticket drill-down (out of scope because we don't  ← ANCHORING ON FIRST IDEA
  want managers looking at individual tickets)                    (the original stakeholder
                                                                   said "team" so every
                                                                   scenario is team-level.
                                                                   But "why is agent X's
                                                                   cycle time so high?" is
                                                                   the first question a
                                                                   manager will ask once
                                                                   they see the chart.
                                                                   Dismissing drill-down is
                                                                   a reason to have the
                                                                   conversation again, not
                                                                   a reason to skip it.)
```

**What's missing entirely (the patterns you detect by what is NOT there):**
- No scenario asks "what does the manager do with this information?" If the answer is "nothing concrete," the feature is a vanity surface.
- No mention of who else sees the dashboard. Agents themselves? HR? Compliance? Each has different data-sensitivity implications.
- No NFRs. Not because they don't apply, but because the author didn't think to consider them - agent performance data is sensitive and may be subject to labour-law constraints on monitoring.
- No acknowledgement that "how the team is handling tickets" was the stakeholder's phrasing. The translation step is missing - the author wrote down what they heard, not what it meant.

### The human failure modes, named

- **Stakeholder echo** - The AC repeats what the stakeholder said, in their words, including their solution. The author has not translated the request into a behavior.
  *Detection:* the value statement and the stakeholder's verbatim quote are indistinguishable.

- **Seniority-as-evidence** - "The architect said X." "The tech lead estimated Y." Cited authority replaces verified reasoning.
  *Detection:* claims that cannot be checked without asking the cited person.

- **Sneaked implementation** - URL paths, table names, widget types, library names, specific UI text in the acceptance criteria.
  *Detection:* replace any noun in the AC with a synonym; if the meaning changes, the AC is imperative, not declarative.

- **False completeness** - Three scenarios that are the same scenario with different nouns. Looks thorough. Tests one thing.
  *Detection:* ask "what behavior does each scenario verify that the others do not?" If the answer is "the filter value is different," merge them and spend the scenario count on something real.

- **Anchoring on the first idea** - Every story descends from the first framing. The author never asked "what if the opposite?"
  *Detection:* look for the negative-space questions. If the first stakeholder said "team," is there any rep-level thinking? If the first idea was a dashboard, did anyone ask "what if this is a report instead?"

- **Absent user** - The feature is described from the system's perspective. The human's motivation, confusion, and likely misuse are inferred or missing.
  *Detection:* no scenario contains a human interpretation or judgment; every Then is a UI state or a data change.

**Where AI fails by inventing, humans fail by inheriting.** AI backfills unknowns with plausible defaults. Humans carry forward whatever they were given - stakeholder words, prior architecture, the tech lead's estimate - without testing it. Both produce requirements that *look* reasonable and collapse on contact with building.

</human-authorship-examples>

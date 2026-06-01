<!-- For a fully worked example of this template, read [worked-example.md](worked-example.md) -->

# Feature: [Name]

## Participants & Decisions

**Why this section exists:** requirements age, but the conversation that produced them ages faster. Six months from now, when someone reads this spec and wonders why a specific edge case was chosen, this table is the thread to pull. Fill it in even if you are the only participant - especially if you are the only participant.

| Participant | Role | Present for | What they pushed back on | What was deferred |
|---|---|---|---|---|
| | | | | |

**Date of conversation:**
**Author (who drafted this document):**
**Stakeholders consulted (not in the room, but whose views are reflected):**

If this row has one entry and it is you, mark the sign-off at the bottom as `Self-reviewed` and flag the trust level explicitly in Risks & Unknowns.

## Problem Statement

**Who** has this problem:

**What** problem they face:

**Why** it matters (impact if unsolved):

## Success Criteria

**Measurable outcome:**

## User Stories

### Story 1: [Name]

**As a** [actor] **I want to** [behavior] **so that** [value]

**Estimated size:** [Should be <=2 days from started to production]

#### Acceptance Criteria

```gherkin
Scenario: [Happy path description]
  Given [precondition]
  When [action]
  Then [expected outcome]

Scenario: [Edge case / unhappy path description]
  Given [precondition]
  When [action]
  Then [expected outcome]
```

#### Non-Functional Requirements

- **Performance:**
- **Security:**
- **Accessibility:**
- **Observability:**
- **Data handling:**

---

### Story 2: [Name]

**As a** [actor] **I want to** [behavior] **so that** [value]

**Estimated size:** [Should be <=2 days from started to production]

#### Acceptance Criteria

```gherkin
Scenario: [Happy path description]
  Given [precondition]
  When [action]
  Then [expected outcome]

Scenario: [Edge case / unhappy path description]
  Given [precondition]
  When [action]
  Then [expected outcome]
```

#### Non-Functional Requirements

- **Performance:**
- **Security:**
- **Accessibility:**
- **Observability:**
- **Data handling:**

---

## Risks & Unknowns

| Risk / Unknown | Impact | Mitigation |
|---|---|---|
| | | |

## Assumptions

| Assumption | Needs Verification By | Status |
|---|---|---|
| | | |

## Spikes Needed

| Question to Answer | Time-box | Acceptance Criteria for Spike |
|---|---|---|
| | | |

## Dependencies

| Dependency | Type (hard/soft) | Owner | Status |
|---|---|---|---|
| | | | |

## Multi-audience sign-off

This is THE test for requirements quality. For every story above, confirm each audience can do the following **from these requirements alone**, without a follow-up conversation.

**The core four - always required:**

- [ ] **Product owner** - The value statement matches intent; the described behavior is the behavior that was agreed.
- [ ] **Developer** - Work can be started without asking clarifying questions; scope boundaries and non-functional requirements are explicit.
- [ ] **Tester** - Test cases can be derived from the acceptance criteria alone; edge cases and unhappy paths are covered.
- [ ] **AI agent** (if in the loop as a consumer) - Preconditions, triggers, and outcomes are unambiguous; no assumptions need to be filled in to produce correct code.

**The people who pay for our mistakes - check the ones that apply to this feature:**

These audiences are rarely in the room when requirements are written, but they discover the consequences. You do not need a signature from them on every story. You need the *question they would ask* to have been asked by someone, and the answer to be visible here. For each, confirm that someone on the authoring team has genuinely considered the perspective - not just noted it as boilerplate.

- [ ] **Operator / support** - When this feature misbehaves in production, can the person on-call diagnose it from what this feature emits (logs, metrics, error messages)? Is there a runbook entry or does this create a new "page an engineer" case?
- [ ] **Security / privacy** - Is the data flow here one that security and privacy partners would sign off on if asked? Are there new categories of sensitive data, new authorisation boundaries, new audit requirements, new attack surface?
- [ ] **End-user (not the buyer)** - If the person actually using this feature is different from the person who asked for it, would they describe it the same way the PO does? What would they misuse or misread? What do they gain vs. lose vs. the current state?
- [ ] **Downstream teams / integrators** - If this feature's output feeds another system (analytics, billing, reporting, another team's service), does that system's owner know the contract is changing? Have they agreed to the change?

If any of these four do not apply to this feature (e.g. a purely internal refactor with no new behavior), strike it and note *why* in a sentence. "Not applicable" without a reason is the symptom this section exists to catch.

**Sign-off mode:** Tick one.

- [ ] **Conversation sign-off** - each box above was confirmed by the real person for that audience. The requirement is trusted.
- [ ] **Self-reviewed** - the boxes were confirmed as a thought experiment because the real audiences were not reachable. The requirement is **not** trusted yet; get at least one real audience (other than the author) to review before build starts, and flag the trust level in the Risks & Unknowns section above. See `references/adapting-to-your-context.md` for when and how to operate in this mode.

If any consumer box cannot be checked in either mode, the requirement needs more work - not more building. Send it back to refinement, not forward to delivery.

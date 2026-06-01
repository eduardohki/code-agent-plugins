<objective>
Define the characteristics of effective software requirements so that any workflow in this skill can check work against a clear quality bar.
</objective>

<characteristics>

**1. They express value, not tasks.**

A good requirement answers "why does this matter?" before "what should happen." Without the value statement, nobody can make trade-off decisions. When priorities shift or scope needs cutting, the team has no basis for deciding what stays and what goes.

A requirement without a value statement is just a to-do item. To-do items get done or they don't. Requirements with clear value can be negotiated, deferred, split, or replaced with cheaper alternatives that achieve the same outcome.

**2. They describe observable behavior.**

A good requirement describes something a person or system can observe happening. "The system should be fast" is not observable. "Search results appear within 500ms for queries returning fewer than 100 items" is observable. "The user experience should be intuitive" is not observable. "A new user can complete sign-up without contacting support" is observable.

Observable means: two people watching the same system would agree on whether the behavior occurred. If reasonable people could disagree, the requirement is too vague.

**3. They have testable acceptance criteria.**

Every requirement needs concrete scenarios that define when it's done. These scenarios use the Given/When/Then structure:

- **Given** a specific starting condition
- **When** a specific action occurs
- **Then** a specific observable outcome results

If you can't write at least one concrete scenario, you don't yet understand the requirement well enough to build it. The acceptance criteria ARE the Definition of Done for that piece of work.

**4. They are declarative, not imperative.**

Good acceptance criteria describe WHAT should happen, not HOW it should happen.

- **Imperative (bad):** "Click the Place Order button, wait for the spinner to disappear, check that the confirmation banner shows the order number"
- **Declarative (good):** "Given a valid order has been submitted, then the user is shown a confirmation with their order reference number"

Imperative criteria embed implementation decisions into requirements. They constrain the solution space unnecessarily and become brittle when the implementation changes.

**5. They are small enough to deliver in under 2 days.**

A requirement that takes longer than 2 days from "started" to "in production" is hiding something: unclear scope, unidentified dependencies, or unrecognized complexity. The 2-day constraint is a forcing function that reveals these problems early, when they're cheap to address.

If a requirement can't meet this bar, it needs to be decomposed further. This isn't a failure - it's the process working correctly. The decomposition IS the discovery.

**6. They are vertically sliced.**

Each requirement delivers a complete, end-to-end slice of functionality. A user can observe the behavior working. This means the requirement touches whatever layers it needs to (UI, API, database, etc.) rather than being confined to a single layer.

A "database story" and an "API story" and a "UI story" that must all be deployed together to produce any observable behavior are not three requirements. They're one requirement that was incorrectly sliced.

**7. They cover edge cases and unhappy paths.**

The happy path is usually obvious. The quality of requirements shows in how well they handle:

- Invalid or unexpected inputs
- Missing or unavailable dependencies
- Concurrent or conflicting operations
- Boundary conditions (empty lists, maximum values, zero quantities)
- Permission and authorization failures
- Network and infrastructure failures

Each of these deserves its own acceptance scenario. Leaving them implicit means each implementer (human or AI) will make their own assumptions about what should happen - and those assumptions will differ.

**8. They include non-functional requirements.**

Behavior is not just "what it does" but also "how well it does it." Good requirements explicitly state expectations for:

- **Performance:** response times, throughput, resource usage
- **Security:** authentication, authorization, data protection, input validation
- **Accessibility:** standards compliance, assistive technology support
- **Observability:** logging, monitoring, alerting needs
- **Data handling:** retention, privacy, consistency, backup/recovery

Unstated non-functional requirements get whatever the implementer's defaults are. Those defaults may not match what the product needs.

**9. They surface unknowns instead of hiding them.**

Good requirements distinguish between:
- **Known knowns:** behaviors we understand and have specified
- **Known unknowns:** things we know we don't know, captured as risks or spikes
- **Assumptions:** things we're treating as true but haven't verified

Hiding uncertainty behind confident-sounding language is one of the most common failure modes. "The system handles high load gracefully" sounds like a requirement but it's an assumption dressed up as a specification. What is "high load"? What does "gracefully" mean? These need to be explicit.

**10. They pass the multi-audience test.**

A requirement is well-written when ALL of these people can read it and independently understand what "done" looks like:

- **Product owner:** "Yes, this is the behavior I need"
- **Developer:** "I know exactly what to build and where the boundaries are"
- **Tester:** "I can write tests for this right now without asking more questions"
- **AI agent:** "The acceptance criteria are unambiguous enough to generate correct code and tests"

If any one of those fails, the requirement needs more work.

This test applies **regardless of who authored the requirement.** Whether a product owner drafted it, a business analyst refined it, or an AI agent generated it from meeting notes - the quality bar is the same. Well-formatted is not the same as well-understood.

**11. They reduce the need for feedback by being self-evident.**

In practice, the person who can validate a requirement - the product owner, the stakeholder, the domain expert - is not always available when the implementer needs them. Feedback gets delayed by hours, days, or weeks.

Well-written requirements mitigate this directly. If the acceptance criteria are concrete and unambiguous, the developer can determine "done" without waiting for the PO. The tester can validate without a walkthrough. The AI agent can verify its output against the criteria. Good requirements don't eliminate the need for feedback, but they drastically reduce how often someone has to interrupt someone else for clarification.

This is one of the strongest practical arguments for investing in requirements quality: every hour spent making acceptance criteria concrete and testable saves multiple hours of blocked work, context-switching, and back-and-forth later.

**12. They state what is out of scope.**

Good requirements don't just define what the feature does - they explicitly state what it does NOT do. Without explicit scope boundaries, every stakeholder assumes their adjacent concern is included. The developer builds more than intended. The tester tests features that don't exist yet. The AI agent generates code for behaviors that were never requested.

Stating what is deferred or excluded prevents scope creep and makes prioritization visible. "This story covers password reset via email. Password reset via SMS is deferred to a future story" is clearer than leaving SMS unmentioned and hoping everyone assumed it was out.

If nothing is explicitly excluded, the scope is effectively unbounded - which means it's uncontrolled.

</characteristics>

<quality-gate>

Use this checklist to evaluate any requirement:

- [ ] Value statement present (why this matters)
- [ ] Behavior is observable (two people would agree it happened)
- [ ] Acceptance criteria use concrete Given/When/Then scenarios
- [ ] Criteria are declarative (what, not how)
- [ ] Deliverable to production in under 2 days
- [ ] Vertically sliced (end-to-end, not by layer)
- [ ] Edge cases and unhappy paths have explicit scenarios
- [ ] Non-functional requirements stated (performance, security, accessibility, observability)
- [ ] Unknowns and assumptions are surfaced, not hidden
- [ ] Scope boundaries stated (what is explicitly out of scope or deferred)
- [ ] Passes the multi-audience test (PO, dev, tester, AI can all determine "done")
- [ ] Self-evident enough that implementers rarely need to ask for clarification

</quality-gate>

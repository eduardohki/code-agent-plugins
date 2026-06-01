# Workflow: Shape a New Feature

## Required Reading

**Read these reference files NOW before proceeding:**
1. [essential-principles.md](essential-principles.md) - the five non-negotiable principles for this skill
2. [what-good-looks-like.md](what-good-looks-like.md)
3. [bdd-and-acceptance-criteria.md](bdd-and-acceptance-criteria.md)
4. [story-slicing-and-sizing.md](story-slicing-and-sizing.md)

**Read when you reach the relevant step:**
5. [worked-example.md](../assets/worked-example.md) - Read before Step 6 (slicing) or at the latest before Step 9 (producing output). Shows the expected quality bar for every section of the output template.

**Read situationally:**
6. [adapting-to-your-context.md](adapting-to-your-context.md) - Read when the user signals their org does not practice these ceremonies (no three amigos, no DoR, no 2-day delivery, no Gherkin tooling), or is working alone in a less-mature context. Explains how to adapt notation, sign-off, and hand-offs without losing the underlying principles.
7. [known-limitations.md](known-limitations.md) - Read when the feature is clearly in a domain where the skill's defaults may not fit: regulated/contractually-fixed requirements, platform/infrastructure work, discovery phases where the problem itself is unclear, or environments where the PO has no negotiating authority. Tells you where to push back on the skill's own advice.

## Context

The user wants to produce well-formed requirements for a new feature. They may arrive with:
- A rough feature idea ("we need a search function")
- Meeting notes or transcripts
- A product brief or PRD
- External documentation or competitor analysis
- Input from stakeholders that needs structuring

All starting points are valid. This workflow steers toward quality requirements regardless of where the input came from. The user may be a product owner, business analyst, developer, tester, or an AI agent operating on behalf of one of these roles.

## Process

## Step 1: Triage the input material

*Why this step matters: weak input produces weak requirements. Running the full process on a vague brief creates a filled template with nothing behind it - structured fiction (principle #1).*

If the user has provided input material (meeting notes, briefs, descriptions, backlog items), assess it against six dimensions before proceeding. This catches weak input early - before investing effort in the full process.

**Staleness check:** If the input is from weeks or months ago, flag it immediately. Requirements age quickly. Assumptions made months ago may no longer hold. Ask: "This material appears to be from [timeframe]. Has anything changed since then?"

**Score each dimension:**

| # | Dimension | What to look for | Red flags |
|---|-----------|-----------------|-----------|
| 1 | **Why** | Concrete business motivation - a metric, a cost, a customer need, a deadline | "we need to", "it would be nice", no reason given |
| 2 | **Who** | Named personas or roles with a specific context or need | "a user", "the system", unnamed stakeholders |
| 3 | **Behavioral language** | Outcomes - what users can *do* or *experience* | Mixed with implementation: endpoints, file names, component names, service names |
| 4 | **Implementation contamination** | Technical specifics that belong in design, not requirements | Button placement, class names, "not wired up yet", architectural patterns, database schemas |
| 5 | **Edge cases** | At least one unhappy path, error state, or boundary condition | Only the happy path described |
| 6 | **Scope clarity** | What is explicitly out of scope or deferred | Nothing deferred, every direction left open |

Score each: **Clear**, **Partial**, or **Missing**.

**Thresholds:**
- **3 or more Missing:** Present the assessment and strongly recommend improving the input before continuing. Weak input produces weak requirements.
- **1-2 Missing or any Partial:** Present the assessment with specific suggestions, but offer to proceed with the current input.
- **All Clear:** Proceed to Step 2 without presenting the assessment.

**Assessment format when shown:**

```
## Input quality

| Dimension                | Score    | Note |
|--------------------------|----------|------|
| Why                      | [score]  | [specific observation] |
| Who                      | [score]  | [specific observation] |
| Behavioral language      | [score]  | [specific observation] |
| Implementation details   | [score]  | [specific observation] |
| Edge cases               | [score]  | [specific observation] |
| Scope clarity            | [score]  | [specific observation] |

**Suggestions:**
- [specific, actionable improvements with before/after examples]

Stronger input produces stronger requirements - the richer the context
here, the fewer gaps to fill later.

Proceed with current input, or provide improved material?
```

If the user provides improved material, re-assess once. Do not loop more than once - proceed with whatever the user provides after the second pass.

If the user arrived with just a feature name or rough idea (no material to triage), skip the assessment and proceed directly to Step 2. The conversation itself will fill the gaps.

Do NOT jump to writing scenarios yet. The conversation comes first.

## Step 2: Establish the "why"

*Why this step matters: a requirement without a stated value is indistinguishable from noise. You cannot negotiate scope, defer pieces, or measure success without knowing who cares and why.*

Ask the user to clarify (or extract from their input material):

- **Who has this problem?** Which users, roles, or systems are affected?
- **What problem do they face?** What can't they do, or what is painful/inefficient today?
- **What happens if we don't solve it?** What is the cost of inaction?
- **What does success look like?** What measurable change will indicate the feature is working?

If the input material already answers some of these clearly, confirm the understanding rather than re-asking. Only ask about genuine gaps.

These answers form the **Problem Statement** and **Success Criteria** in the output.

## Step 3: Identify the actors and behaviors

*Why this step matters: naming actors and framing their behaviors is how you describe WHAT the system should do without slipping into HOW it should do it (principle #3).*

Ask (or extract):
- Who interacts with this feature? (Users, other systems, administrators, background processes)
- What should each actor be able to do that they can't today?
- What existing behaviors change?

Frame each answer as a behavior: "[Actor] can [do something] so that [value]."

If behaviors are unclear or the user is describing implementation ("add a dropdown"), redirect to the behavior: "What does the user need to accomplish? The dropdown is one possible solution - let's first capture what they need."

## Step 4: Write acceptance criteria as concrete scenarios (BDD)

*Why this step matters: scenarios are the only form of acceptance criteria that every consumer - PO, dev, tester, AI agent - can independently verify (principle #2, the multi-audience test). Vague criteria mean each consumer fills the gaps differently, and the gaps show up as bugs or rework.*

**Notation is flexible, concreteness is not.** The examples in this skill use Gherkin (`Given / When / Then`) because it is unambiguous and readable by all four audiences. If the user's team writes acceptance criteria as Jira bullet lists, free-text paragraphs, or any other format, adapt the notation to match - what matters is that each scenario is specific and verifiable, not the syntax. See [adapting-to-your-context.md](adapting-to-your-context.md) for an example of BDD-in-bullets that carries the same substance.

For each behavior identified in Step 3, write concrete scenarios using Given/When/Then (or the equivalent structure in whatever notation the team uses).

**Start with the happy path** - the most common, expected flow.

**Then systematically explore:**
- What happens when inputs are invalid or missing?
- What happens when a dependency is unavailable or slow?
- What happens when the user does something unexpected (back button, double-click, timeout)?
- What happens at boundary conditions (empty list, maximum value, first use)?
- What happens with concurrent access or conflicting operations?

Each answer becomes its own scenario. Refer to the edge case mapping in [story-slicing-and-sizing.md](story-slicing-and-sizing.md) for the full checklist.

**Keep criteria declarative.** Describe WHAT should happen, not HOW. If you find yourself writing UI interaction steps ("click the button, wait for the spinner"), step back and describe the outcome instead.

**Guideline:** If a behavior accumulates more than 6 acceptance scenarios, it can probably be split into separate stories. Flag this for Step 6.

## Step 5: Identify non-functional requirements

*Why this step matters: functional correctness alone is not "good enough to build from." A feature that works but is 10× too slow, leaks data, or cannot be operated is not done. NFRs must be stated as measurable criteria, not aspirations.*

For the feature as a whole and for individual stories where relevant, ask about:

- **Performance:** What response times or throughput are expected? What happens under load?
- **Security:** Who can access this? What data needs protection? What needs audit logging?
- **Accessibility:** What standards apply? Any specific assistive technology requirements?
- **Observability:** What should be logged? What metrics matter? What alerts are needed?
- **Data handling:** Retention, privacy, consistency, backup/recovery considerations?

State these as verifiable criteria, not vague aspirations. "Under 500ms for 95th percentile" not "should be fast." "Data retained for 30 days then permanently deleted" not "retained per policy."

If the user doesn't know the answer, that's fine - capture it as an unknown in Step 7 rather than skipping it silently. "N/A" is a valid answer, but only when you can say *why* - "N/A: no personal data is involved" is fine; "N/A" alone is the symptom the step exists to catch.

## Step 6: Slice into deliverable stories

*Why this step matters: large stories hide complexity, assumptions, and risk (principle #4). The 2-day constraint forces you to find a smaller slice that still delivers end-to-end value - and it is the single lever that makes change-failure rate and cycle time tractable.*

Apply the 2-day constraint: can each story go from "started" to "in production" in under 2 days?

If not, decompose further:
- Look at the acceptance criteria - can some be deferred to a later story?
- Look for natural seams - separate the core behavior from enhancements
- Ensure each resulting story is a vertical slice (delivers observable end-to-end behavior)

**Never slice by technical layer** (separate DB/API/UI stories).
**Never slice by activity** (separate coding/testing/review stories).

Each story after slicing must still have:
- A value statement (why)
- Its own acceptance criteria (when is it done)
- Observable behavior (a user or system can see it working)
- Explicit scope boundaries (what is deliberately excluded or deferred to later stories)

For the economics behind the 2-day rule, worked examples of vertical vs. horizontal slicing, INVEST, and the dependency math (1 in 2^n), see [story-slicing-and-sizing.md](story-slicing-and-sizing.md).

## Step 7: Surface risks, unknowns, and assumptions

*Why this step matters: assumptions presented as facts are the most common cause of late rework. Naming them converts hidden risk into tracked risk - and some assumptions, once stated aloud, turn out to be wrong.*

Review everything collected so far and explicitly identify:

- **Assumptions:** Things being treated as true but not verified. Mark each one: "ASSUMPTION: [statement] - needs verification."
- **Unknowns:** Things the team doesn't know and needs to investigate. Consider whether these warrant spikes (time-boxed 1-3 day explorations).
- **Dependencies:** What does this feature depend on? Which dependencies are hard (must exist before we can deliver) vs. soft (would be nice but we can work around)?
- **Risks:** What could go wrong? What's the impact if an assumption is wrong?
- **External gate-keeper consultation (if applicable):** for this feature, which of the following need to have been surfaced before build? Name them even if they live outside your team's process:
  - Security / information security
  - Privacy / data protection - especially for personal data, new data flows, new processing purposes
  - Compliance / legal - especially for customer-facing communications, contractual changes, regulated disclosures
  - Operations / on-call - especially when the feature changes observable production behaviour

  Recording "Security reviewed on 2026-03-10: no new findings" is worth more than a checklist box. If no gate applies, say so explicitly.

## Step 8: Check against quality gate

*Why this step matters: this is the final forcing function before the requirement becomes "buildable." A story that fails any item here will fail in delivery, just more expensively and later.*

Before producing output, verify every story against the quality gate from [what-good-looks-like.md](what-good-looks-like.md):

- [ ] Value statement present
- [ ] Behavior is observable
- [ ] Acceptance criteria use concrete Given/When/Then scenarios
- [ ] Criteria are declarative (what, not how)
- [ ] Deliverable to production in under 2 days
- [ ] Vertically sliced
- [ ] Edge cases and unhappy paths have explicit scenarios
- [ ] Non-functional requirements stated
- [ ] Unknowns and assumptions surfaced
- [ ] Scope boundaries stated (what is out of scope or deferred)

**Multi-audience test (the skill's load-bearing check).** From these requirements *alone* - without a follow-up conversation - can each consumer do the following?

- [ ] **Product owner** can confirm the value statement matches intent and the described behavior is what was agreed
- [ ] **Developer** can start building without asking clarifying questions; scope boundaries and NFRs are explicit
- [ ] **Tester** can derive test cases from the acceptance criteria alone, including edge cases and unhappy paths
- [ ] **AI agent** (if in the loop as consumer) has unambiguous preconditions, triggers, and outcomes - no gaps it needs to fill with assumptions

**How to run the check.** Preferred: confirm each box with the real person for that audience - this is *conversation sign-off*, and the requirement is trusted. When that is not possible (a common reality - see [adapting-to-your-context.md](adapting-to-your-context.md)), run the check as a thought experiment by mentally playing each audience and asking "could this person do their job from this alone?" That is *self-reviewed sign-off*: mark it as such in the template, and flag the requirement's trust level in Risks & Unknowns. Before build starts, get at least one real audience other than the author to push back. A self-reviewed requirement is a best-effort draft, not a trusted spec.

If any item fails in either mode, loop back to the relevant step and address it. Failing the multi-audience test means the requirement needs more refinement, not more building.

## Step 9: Produce output

*Why this step matters: the output is the visible artefact of the conversation (principle #1). Producing it too early - before discovery - creates structured fiction and false confidence; producing it after discovery captures the agreement so it can outlive memory.*

**SAFEGUARD: Only reach this step after genuine discovery has happened.**

If the user provided only a vague feature name and skipped through the conversation steps without substantive answers, do NOT fill the template. Loop back to the first unanswered question. A filled template without understood behaviors behind it is worse than no template - it creates false confidence that requirements are "done" when they haven't been explored.

If genuine discovery has happened, produce the output using [feature-requirements.md](../assets/feature-requirements.md). The template includes two load-bearing sections that are easy to skip and expensive to miss:

- The **Participants & Decisions** table at the top - who was in the conversation, what they pushed back on, what was deferred. Fill this in even if the user is the only participant (in that case, the table has one row and the sign-off mode is `Self-reviewed`). Six months from now, this table is the only thread into why specific decisions were made.
- The **Multi-audience sign-off** at the bottom - fill the checkboxes honestly. The core four (PO, dev, tester, AI agent) are always required; the second group (operator/support, security/privacy, end-user, downstream teams) must be checked *or* explicitly struck-through with a reason.

Read [worked-example.md](../assets/worked-example.md) for a fully filled example showing the expected quality bar for every section.

Present the completed requirements to the user and ask:
- "Does this capture the behaviors you described?"
- "Are there scenarios we missed?"
- "Are there any assumptions here that you know the answer to?"

## Step 10: Close the loop - suggest next steps

*Why this step matters: the skill's purpose is closing the gap between "we know what we want" and "we have requirements good enough to build from." The output is the hand-off, not the destination.*

Once the user confirms the requirements, suggest concrete next steps based on their situation:

- **Want a second pass for quality?** Offer to run the output through the `review-existing` workflow - fresh eyes catch things the shaping conversation missed, and the AI-authorship checks apply if an AI did the writing.
- **Ready to implement?** Point toward the downstream activities the requirements enable - system or architecture design for non-trivial design choices, a testing-strategy pass to plan verification, code review once implementation starts. If the user has dedicated skills for any of those in their environment, suggest them by name; otherwise describe the activity. Either way, make clear that the requirements are input to that next step, not a replacement for it.
- **Unknowns remaining?** For each open spike in the output, suggest running it as its own small investigation before the dependent stories are scheduled.
- **Stakeholders to involve?** Remind the user that the scenarios are only valuable if the right people have actually read and agreed to them (principle #1). If any audience in the sign-off wasn't present during discovery, schedule the conversation before build starts.

Keep the closer specific: name the skill or activity, don't hand-wave.

## Success Criteria

This workflow is complete when:
- [ ] The problem and its value are clearly stated
- [ ] All identified behaviors have concrete acceptance scenarios
- [ ] Edge cases and unhappy paths are covered (not just happy path)
- [ ] Non-functional requirements are explicitly stated
- [ ] Stories are small enough to deliver in under 2 days
- [ ] Stories are vertically sliced
- [ ] Risks, unknowns, and assumptions are surfaced
- [ ] Every story passes the multi-audience test
- [ ] Output uses the feature requirements template
- [ ] User has reviewed and confirmed the requirements

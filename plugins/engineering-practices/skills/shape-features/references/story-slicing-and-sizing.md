<objective>
Provide practical guidance on making requirements small enough to deliver rapidly, slicing them correctly, handling uncertainty, and covering the dimensions that feature-level requirements commonly miss.
</objective>

<table-of-contents>
1. two_day_target - The 2-day delivery constraint and why it matters
2. vertical_slicing - Vertical vs. horizontal slicing with worked examples
3. invest_criteria - INVEST checklist for well-formed stories
4. edge_case_mapping - Systematic edge case and unhappy path discovery
5. non_functional_requirements - Performance, security, accessibility, observability, data handling
6. spikes - Time-boxed exploration for high-uncertainty work
7. contract_driven_development - Defining contracts between services and teams
</table-of-contents>

<two-day-target>

**The rule is not "2 days." The rule is "be wrong cheaply." 2 days is the form the rule takes in a team that deploys daily.**

Start with the economics, because the number without the reasoning turns into ritual within a week:

> The cost of being wrong grows with the size of the bet.
>
> - A 2-day story that ships wrong costs 2 days to discover (the feedback loop is short) and 2 days to fix (the context and the people are still fresh).
> - A 2-month story that ships wrong costs 2 months to discover (nobody uses an unfinished thing, so you only learn on release) and substantially more to fix - by then the reasoning has left the building with the people who held it in their heads, and the code is entangled with other changes.
>
> Every increase in story size is an increase in the bet you are making without feedback. The smallest increment that *teaches you something* is cheaper than the largest increment you can coordinate.

This is why smaller is always better. Not because smaller is virtuous, but because being wrong at 2 days costs roughly 2 days and being wrong at 2 months costs roughly a quarter.

**The number is not sacred; the feedback cycle is.**

- In a team that deploys daily with full automation, 2 days is usually the right target.
- In a team that deploys weekly, the equivalent might be a story that is code-complete and reviewed in 2 days even if production comes later - you still want the learning loop inside the team to close quickly.
- In a team that deploys monthly, 2 days still applies to story *size*: the work itself should be small, even if the pipeline is slow. Do not use a slow pipeline as a reason to write larger requirements.
- In contractually-fixed or waterfall environments where release cadence is truly outside your control, apply the rule as a *thought experiment* during requirements: "If I had to put this into production in 2 days, what would that be?" You will find the minimum viable slice, the actual unknowns, and the places you can drop scope under pressure. Even if you ship the full thing, knowing the 2-day slice changed how you wrote the requirement.

**What "2 days" actually measures.** It is the time the work itself takes - coding, testing, code review, and merging. It is about story *size*, not pipeline *cycle time*. A story can be code-complete and reviewed in a day but not reach production for another week because of organisational lead time; that is a delivery concern, not a requirements concern.

**How to check.** Ask "Could a single developer, or the team swarming together, take this from started to merged-and-ready in 2 days?" If the answer is "probably, if nothing goes wrong," decompose further - "if nothing goes wrong" means you have not yet surfaced what goes wrong, and stories sized by the happy path become 3x longer in practice.

**What the 2-day target forces (the second-order effects).** These are not the reason for the rule. They are the downstream benefits that appear once you obey the rule long enough to get the feedback cycle moving:

- Clearer, testable acceptance criteria (vague criteria make work unbounded and unbounded work cannot fit in 2 days).
- Better decomposition (you find hidden complexity by trying to make things smaller, not by thinking harder about them at their current size).
- Faster feedback loops (production validates assumptions quickly, and quickly-validated assumptions are the only kind worth making).
- Lower risk (less investment before course-correction is possible).

If you ever find yourself defending a 5-day story on the grounds that "it has to be that size," come back to the economics. You are proposing to make a 5-day bet without feedback. Unless there is a feedback mechanism *before* day 5, you are betting blind. Reframe the story so the first 2 days produce something you can learn from - or accept in writing that the whole 5-day thing is a gamble and the team is taking it eyes-open.

</two-day-target>

<vertical-slicing>

Stories are sliced vertically: each story delivers a complete, end-to-end behavior that a user or system can observe.

**Vertical slice:** A story that touches the UI, API, and database as needed to produce one observable behavior. All three layers ship together in one delivery.

**Horizontal slice (avoid):** Separate stories for "build the database table," "build the API endpoint," "build the UI form." None of these delivers observable behavior alone.

**Why vertical slicing matters:**
- Each delivery is independently valuable and verifiable
- Feedback comes from real usage, not component-level testing
- Integration risk is addressed with every delivery, not deferred to the end
- Priorities can shift without wasted work on unused components

**How to slice vertically:**

1. Start with the full feature behavior
2. Identify the acceptance criteria
3. Ask: which criteria can be delivered independently?
4. Each independent criterion (or small group) becomes a story
5. Each story must produce an observable behavior

**Example:**

Feature: Order search

Full behavior: "Users can search orders by customer name, order status, and total range, with results sorted by relevance, paginated, and filterable."

Vertical slices:
1. "Users can search orders by customer name and see results" (simplest useful search)
2. "Users can filter search results by order status" (adds status dimension)
3. "Users can filter search results by total range" (adds total dimension)
4. "Search results are paginated" (handles volume)
5. "Search results are sorted by relevance" (improves quality)

Each slice works independently. Each can be deployed and used in production. Each provides feedback on whether the next slice is actually needed.

**Worked example: backend/frontend/testing split vs. vertical slices**

A team is building a "manage contact preferences" feature for an account settings page. Here's how it often gets split:

*Horizontal (the common approach):*
- Story A: "Backend - Create PUT /users/:id/preferences endpoint"
- Story B: "Frontend - Build contact preferences form with validation"
- Story C: "QA - Test contact preferences end-to-end"

Three stories, three assignees, looks like parallel progress. But: Story B can't be verified without Story A. Story C can't start until both A and B are deployed. If the backend developer's response format doesn't match what the frontend expected, nobody discovers this until integration - at the very end. Three stories "in progress," zero value delivered until all three are done.

*Vertical (the alternative):*
- Story 1: "User can update their preferred contact channel (email / SMS / push)" (touches API + UI + validation for one preference, deployable, observable)
- Story 2: "User can update their marketing opt-in status with confirmation" (adds the consent-change flow, independently deployable)
- Story 3: "User can update their preferred language" (adds the language preference behavior, independently deployable)

**Why the vertical approach is fundamentally better:**

*1. Integration problems surface immediately, not at the end.*
In the horizontal approach, the backend and frontend are built in isolation and integrated at the end. Mismatches between what the API returns and what the UI expects only appear during integration. In the vertical approach, integration happens within each story. Story 1 proves the API and UI work together on day one. If there's a mismatch, it's caught immediately while both developers still have full context. By Story 2, the integration pattern is established and tested.

*2. Value is delivered incrementally, not all-or-nothing.*
With horizontal slicing, the feature delivers zero value until all three layer stories are complete. If the sprint ends with Story A done but Story B half-finished, nothing ships. With vertical slicing, Story 1 can ship to production on its own. Customers can update their contact channel while Stories 2 and 3 are still in progress. If priorities shift after Story 1, the team has already delivered something instead of having three half-done layer stories.

*3. Feedback comes from real usage, not from assumptions.*
Story 1 in production tells you whether users actually use the contact preferences feature, whether the UI flow makes sense, and whether the API performs under real conditions. This feedback informs how you build Stories 2 and 3. With horizontal slicing, you get zero feedback until everything is assembled - by which point changing direction is expensive.

*4. Scope can be negotiated at any point.*
After shipping Story 1, the product owner might say: "Actually, the language preference isn't needed right now - let's work on something else instead." With vertical slices, Story 3 simply gets deprioritized. No work is wasted. With horizontal slices, you can't drop the language preference without abandoning partial backend, frontend, and testing work that was done assuming the full feature would ship.

*5. Specialists still specialize - they just collaborate differently.*
The backend developer still writes the API. The frontend developer still builds the UI. The tester still tests. The difference is that they collaborate on the same story instead of working on isolated layer stories and hoping they'll integrate cleanly at the end. Multiple developers can work on a single vertical story as parallel tasks: one building the endpoint, the other building the form, both working against shared acceptance criteria that describe the end-to-end behavior they're jointly delivering.

*6. Testing is continuous, not a phase.*
In the horizontal approach, testing is either a separate story (Pitfall 5) or something that happens after all layers are "done." Either way, defects are found late. In the vertical approach, each story includes its own testing as part of the Definition of Done. The tester collaborates with the developers during the story, writing acceptance tests while the code is being written. Defects are found in hours, not days or weeks.

**Anti-pattern detection:**
- If you must deploy two stories together for anything to work -> you sliced horizontally
- If a story only affects one technical layer -> you sliced horizontally
- If a story has no observable behavior for a user or consuming system -> it's not a story
- If a "testing story" or "QA story" exists separately from the feature story -> you sliced by activity

**When backend and frontend are different teams.**

In many organizations, the people who build the API and the people who build the UI are on separate teams with separate backlogs and separate priorities. This is a real constraint. The skill's job is not to reorganize the company - it's to help write requirements that produce good outcomes within this structure.

The requirement should still describe the vertical, end-to-end behavior: "User can update their preferred contact channel." This is the source of truth for what "done" looks like. What changes is how the work is coordinated, not how the requirement is written.

*What goes wrong without a vertical requirement:*
Each team writes their own scope. The backend team creates "PUT /users/:id/preferences endpoint" with their own assumptions about the payload, validation rules, and error responses. The frontend team creates "contact preferences form" with their own assumptions about what the API will accept and return. The two sets of assumptions diverge silently. Integration happens late and reveals mismatches.

*What to do instead:*

1. **Write the requirement as a vertical slice with acceptance criteria that describe end-to-end behavior.** "User can update their preferred contact channel" with Given/When/Then scenarios that specify the observable result. This is the shared understanding both teams work from.

2. **Define the contract between the teams during refinement, not during integration.** The acceptance criteria inform the API contract: what the frontend will send, what the backend will return, what happens on error. Write this down as part of the requirement. Both teams build against this contract - the backend team implements it, the frontend team mocks it until the real API is ready.

3. **Capture the cross-team dependency explicitly.** In the requirement's Dependencies section, state: "Requires [backend team] to deliver the PUT /users/:id/preferences endpoint. Lead time: [their estimate]. Contract agreed: [yes/no]." This makes the coordination visible instead of hoping both teams will finish at the same time.

4. **Accept that cycle time will be longer, but don't let that make requirements worse.** When two teams must coordinate, the calendar time to deliver will increase. That's a delivery reality, not a reason to write vague requirements or skip acceptance criteria. If anything, cross-team work needs *more* precise requirements because the people building each half don't sit together and can't resolve ambiguity with a quick conversation.

The vertical requirement is the contract that keeps both teams aligned. Without it, each team optimizes for their own layer and the integration becomes the riskiest moment in the delivery.

</vertical-slicing>

<invest-criteria>

Use INVEST to evaluate whether a story is well-formed:

- **Independent:** Can be delivered without requiring another story to deploy at the same time. Minimizes coordination overhead and dependency chains.

- **Negotiable:** The specific solution can be discussed and adjusted. Stories describe desired behavior, not prescribed implementation. If the implementation is locked in, there's nothing to negotiate.

- **Valuable:** Delivers something a user, customer, or business cares about. Technical tasks that don't connect to user value are tasks within stories, not stories themselves.

- **Estimable:** The team can reasonably predict the effort. If they can't, it means the story isn't understood well enough - consider a spike to reduce uncertainty.

- **Small:** Can be delivered in under 2 days. If it can't, it needs decomposition.

- **Testable:** Has concrete acceptance criteria that determine when it's done. If you can't test it, you can't verify it, and you don't understand it.

**The dependency math:** The odds of delivering on time for any activity are 1 in 2^n where n is the number of hard dependencies. A story with 3 hard dependencies has a 1-in-8 chance of smooth delivery. Minimize hard dependencies ruthlessly.

</invest-criteria>

<edge-case-mapping>

Good requirements explicitly handle what happens when things go wrong. For each happy-path scenario, systematically consider:

**Input boundaries:**
- Empty or null values
- Maximum length or value inputs
- Special characters, Unicode, injection attempts
- Zero quantities, negative numbers
- Duplicate submissions

**State conflicts:**
- Concurrent modifications by multiple users
- Operations on stale data
- Race conditions between dependent operations
- Interrupted operations (browser close, network drop, timeout)

**Dependency failures:**
- External service unavailable
- External service slow (degraded but not down)
- External service returns unexpected data
- Database connection lost mid-operation

**Authorization and access:**
- User lacks permission for the operation
- User's session expires during the operation
- User accesses resources belonging to another user
- Role changes while an operation is in progress

**Data edge cases:**
- First-time use (no existing data)
- Large datasets (pagination, performance)
- Data migration / backwards compatibility
- Timezone, locale, and currency differences

Not every feature will have all of these. But walking through the list for each story surfaces the scenarios that need explicit acceptance criteria. The scenarios you skip should be deliberate decisions, not oversights.

</edge-case-mapping>

<non-functional-requirements>

Features don't just need to work - they need to work well enough. For each feature, explicitly address:

**Performance:**
- Expected response time for typical operations
- Expected throughput (concurrent users, requests per second)
- Resource constraints (memory, CPU, bandwidth)
- Degradation behavior under load (graceful degradation vs. hard failure)

**Security:**
- Authentication requirements (who can access this?)
- Authorization requirements (what can each role do?)
- Data protection (encryption in transit, at rest)
- Input validation (what constitutes valid input?)
- Audit logging (what operations need to be tracked?)

**Accessibility:**
- Standards compliance (WCAG 2.1 level AA, etc.)
- Keyboard navigation support
- Screen reader compatibility
- Color contrast and visual requirements

**Observability:**
- What should be logged?
- What metrics should be emitted?
- What alerts should fire and under what conditions?
- What dashboards or reports are needed?

**Data handling:**
- Data retention period
- Privacy requirements (PII, etc.)
- Consistency requirements (eventual vs. strong)
- Backup and recovery expectations

State these as concrete, verifiable criteria: "Search results return in under 500ms for the 95th percentile" not "search should be fast." Vague NFRs are the same as no NFRs - the implementer will use their own defaults.

</non-functional-requirements>

<spikes>

When a requirement involves high uncertainty - the team genuinely doesn't know how to approach it or whether it's feasible - use a spike.

**What a spike is:** A time-boxed exploration (1-3 days) to reduce uncertainty enough to write proper requirements.

**What a spike is NOT:** An open-ended research project. A spike has a fixed timebox, acceptance criteria for what knowledge it will produce, and a deliverable (usually: enough understanding to write stories).

**When to use spikes:**
- The team cannot estimate the work because the approach is unknown
- A critical assumption needs validation before committing to a direction
- A new technology or integration needs proof-of-concept

**When NOT to use spikes:**
- As a hedge against normal uncertainty (all work has some uncertainty)
- As a way to avoid committing to an estimate
- As ongoing research without a clear question to answer

**Spike structure:**
- **Question:** What specific thing do we need to learn?
- **Timebox:** 1-3 days maximum
- **Acceptance criteria:** What deliverable proves we've learned enough?
- **Output:** Stories that can now be written with proper acceptance criteria

**Key principle:** The best way to learn is to learn using the problem in front of you right now. Batching learning is worse than batching other kinds of work because effective learning requires applying the learning immediately or it's lost. Prefer the smallest spike that answers the immediate question.

</spikes>

<contract-driven-development>

When a feature involves dependencies between services or teams, define the contracts between them during design - before construction begins.

**What this means:** The consumer and provider agree on the expected inputs, outputs, and behaviors of their interface. This agreement is expressed as contract tests that both sides can run independently.

**Why it matters for requirements:**
- Dependencies are a primary source of blocked work
- Undefined contracts force sequential development (provider must finish before consumer can start)
- Defined contracts enable parallel development (both sides build against the agreed interface)

**For requirements shaping:**
When a feature depends on another service or team, the requirement should include:
- What data/behavior is expected from the dependency
- What the contract looks like (request/response format, error codes)
- Whether the dependency already exists or needs to be built
- Who owns the dependency and what their timeline is

**Organizational dependencies are dependencies too.**
Not all dependencies are technical. In many organizations, the blocker is another team's capacity and priorities, not a missing API. The security team has a 3-week review queue. The platform team provisions infrastructure on a different cadence. The compliance team signs off monthly. These are real constraints that affect when work can reach production.

For requirements, this means:
- Identify organizational dependencies early - which teams or departments must act for this feature to ship?
- Capture their lead times in the Dependencies section (e.g., "Security review: 2-3 week lead time")
- Slice stories so that requests to external teams are made as early as possible, while the team works on parts they control autonomously
- Don't embed an assumption that other teams will act on your timeline - state the dependency and its expected lead time as a known constraint

**Postel's Law for contracts:** "Be conservative in what you do, be liberal in what you accept from others." Design contracts that are strict in what they produce but tolerant in what they consume. This reduces integration failures.

</contract-driven-development>

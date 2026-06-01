<objective>
Explain Behavior Driven Development as a conversation practice for defining behaviors, and provide practical guidance on writing effective acceptance criteria. This is not a testing reference - it is a requirements quality reference.
</objective>

<core-concept>

BDD is a collaborative process where the people involved in defining a feature discuss its intended behaviors and document their shared understanding as concrete, testable scenarios. It is NOT a testing framework, NOT a tool, and NOT something you "hand off to testers."

The value of BDD is the conversation, not the artifact. The scenarios that emerge from the discussion are proof that the conversation happened - and they serve as the Definition of Done for the work.

The three core activities:
1. **Discovery** - structured conversation to explore the feature's behaviors
2. **Formulation** - expressing the understood behaviors as concrete scenarios
3. **Automation** - turning those scenarios into executable tests (this step is about testing, not requirements)

This skill focuses on Discovery and Formulation. Automation is a downstream concern.

</core-concept>

<who-participates>

The conversation should involve at minimum:
- Someone who understands the **business need** (product owner, business analyst, stakeholder)
- Someone who will **build it** (developer)
- Someone who will **verify it** (tester)

This "three amigos" pattern ensures that requirements are understood from all angles before work starts. If any of these perspectives is missing, blind spots will emerge during implementation.

When an AI agent is involved - either as the implementer or as a participant in refinement - the written scenarios become even more critical, because the AI can't participate in the live conversation. The scenarios ARE the AI's understanding of the requirement.

</who-participates>

<three-questions>

**The value of BDD is not the syntax. It is the conversation that exposes the Given.**

Every scenario answers three questions. If you only ever remember this section of the reference, remember these:

1. **Given - "What has to be true before this matters?"**
   State, role, history, environment, time, data, permissions. The conditions the world must be in for the When to be meaningful. This is the question most teams get wrong, because happy-path state is easy to assume and boring to write down.

2. **When - "What triggers it?"**
   An event, not a button click. *"A payment settles,"* not *"the user clicks Pay."* If the When changes when the UI changes, it is not a behavior - it is an interaction. Behaviors survive redesigns.

3. **Then - "How will we know?"**
   Observable outcome, not internal state. *"The customer's order status changes to 'paid' and they receive a receipt,"* not *"a row is written to the payments table with status=1."* If nothing observable changes, the behavior is not testable from outside the system - and probably shouldn't be a user-level acceptance criterion at all.

**Most teams get When/Then right and fake the Given.** They assume the happy state, skip the preconditions, and discover on day three of implementation that the real question was "what if the user has no billing address yet?" That question was always there. The Given is where you find it.

</three-questions>

<the-given-is-the-story>

Changing *only* the Given, keeping the same When and Then, produces different scenarios - and often different stories. This is the single most useful trick in BDD.

**One When/Then, three Givens:**

```gherkin
Feature: Submit a support ticket

  # Happy path - the Given most people write first
  Scenario: User opens a ticket on an active account
    Given I am a user with an active account
    And I have no open tickets
    When I submit a new support ticket
    Then the ticket is registered with a unique reference
    And I receive a confirmation with the next steps

  # Different Given, same When, different Then
  Scenario: User opens a ticket while one is already open
    Given I am a user with an active account
    And I already have an open ticket
    When I submit a new support ticket
    Then my new ticket is registered as a separate ticket
    And I am shown both tickets in my tickets overview

  # Different Given again, previously invisible
  Scenario: User opens a ticket on a suspended account
    Given my account has been suspended
    And the suspension was effective before the reported issue date
    When I submit a new support ticket
    Then the ticket is recorded but held for manual review
    And I am told an agent will contact me to verify eligibility
```

Notice: none of these three scenarios would have been discovered by writing down the happy path and then "thinking about edge cases." They are discovered by asking, for the same trigger, *what other states could the world be in?* That is the BDD conversation. The scenarios are just the receipt.

**This is also how you split stories.** Three Givens that matter = three stories. If two of them collapse into the same behavior, you have one story with two scenarios. If one of them opens up a whole new area (e.g. "what if the account had been closed by the user rather than suspended by us?"), you have a new feature, not a new scenario.

</the-given-is-the-story>

<given-when-then>

The Given/When/Then structure is the written form the three questions take when you record them. The syntax comes from Gherkin - one specific notation popularised by Cucumber - but BDD is the underlying practice. You can do BDD in bullets, in prose, on a whiteboard, in a Notion table. The notation is interchangeable. The three questions are not.

- **Given** (Arrange): The starting conditions. What state must exist before the behavior occurs?
- **When** (Act): The action or event that triggers the behavior. What happens?
- **Then** (Assert): The expected outcome. What observable result should occur?

Additional keywords:
- **And** - adds another condition at the same level (Given... And..., Then... And...)
- **But** - adds a negative condition (But I do NOT see an error message)

**Example:**

```gherkin
Feature: Subscription renewal

  Scenario: User renews before expiry
    Given I have an active subscription expiring in 14 days
    And I have not yet renewed
    When I accept the renewal offer
    Then my subscription is renewed for another billing period
    And I receive a renewal confirmation with the new expiry date

  Scenario: User renews after a short lapse
    Given my subscription expired within the last 30 days
    And I have no outstanding balance
    When I accept the renewal offer
    Then my subscription is reinstated with the original terms
    And the lapse period is noted on the account record

  Scenario: User attempts renewal after extended lapse
    Given my subscription expired more than 30 days ago
    When I request a renewal
    Then I am informed that renewal is no longer available
    And I am offered a new subscription instead
```

</given-when-then>

<declarative-vs-imperative>

This distinction is critical for requirements quality.

**Declarative criteria (preferred):** Describe WHAT should happen without prescribing HOW.

```gherkin
Scenario: User views their account after sign-in
  Given I am a registered user with an active account
  When I authenticate with valid credentials
  Then I am taken to my account dashboard
```

**Imperative criteria (avoid):** Describe the specific steps and UI interactions.

```gherkin
Scenario: User logs in to view their account
  Given I am on the login page
  When I type "maria.garcia@example.com" into the email field
  And I type "MyP@ssword1" into the password field
  And I click the "Sign In" button
  And I wait for the spinner to disappear
  Then I see the text "Welcome back, Maria" in the header
  And I see the "Account" tile on the dashboard
```

**Why declarative is better:**
- It survives implementation changes (UI redesign doesn't break the requirement)
- It works across interfaces (web, mobile, API, CLI)
- It focuses on the behavior that matters, not the mechanics
- It gives implementers (human and AI) freedom to choose the right solution
- It's readable by all audiences - product owners aren't forced to think in UI terms

**When imperative is acceptable:**
- When the specific interaction IS the requirement (e.g., accessibility requirements that mandate specific keyboard navigation)
- When writing detailed test scripts for a specific interface (but these are tests, not requirements)

</declarative-vs-imperative>

<using-criteria-to-negotiate-and-split>

Acceptance criteria are not just a Definition of Done - they're a tool for negotiating scope and splitting stories.

**Example: the subscription renewal feature**

Initial set of acceptance criteria:
1. User can view their renewal offer
2. User can accept the renewal offer
3. User receives a renewal confirmation
4. System prevents renewal after extended lapse (> 30 days)

Looking at this list, you can ask: "Do we need ALL of these for the first delivery?" Maybe the lapse-period validation is a nice-to-have. Deliver criteria 1, 2, and 3 first. Put the feature into production. If users actually attempt to renew after an extended lapse, add the validation as a follow-up story.

This is how acceptance criteria enable smaller deliveries:
- Each criterion is a candidate for a separate story
- The team can negotiate which criteria are essential vs. deferrable
- Deferred criteria become their own stories with their own acceptance tests
- In production, you may learn that deferred criteria aren't needed at all - saving the cost of building them

**Splitting guideline:** If a story has more than 6 acceptance criteria, it can almost certainly be split. If any single scenario has more than 10 conditions, it's trying to describe too many behaviors at once.

</using-criteria-to-negotiate-and-split>

<example-mapping>

Example mapping is a structured conversation technique for exploring a feature's behaviors before writing scenarios. It uses four types of cards:

- **Story** (yellow): the feature or behavior being discussed
- **Rules** (blue): general rules or business logic that govern the behavior
- **Examples** (green): concrete scenarios that illustrate a rule
- **Questions** (red): unknowns or disagreements that need resolution

**How it works:**
1. Start with the story card
2. Discuss what rules govern this behavior
3. For each rule, generate concrete examples (these become your scenarios)
4. When you hit something uncertain, capture it as a question
5. Questions may become spikes or require further research

**When to use it:**
- During refinement, before writing formal acceptance criteria
- When a feature feels complex and you're not sure how to decompose it
- When different people have different mental models of how the feature should work

The questions (red cards) are often the most valuable output. They reveal what the team doesn't know - which is exactly what needs to be resolved before work starts.

</example-mapping>

<common-mistakes>

**Writing scenarios after development instead of before.**
The scenario should exist BEFORE code is written. It defines what "done" means. Writing scenarios retroactively to match what was built defeats the entire purpose.

**One scenario trying to cover multiple behaviors.**
Each scenario should test one specific behavior. If a scenario has many When/Then pairs or complex branching logic, it needs to be split into multiple scenarios.

**Using BDD vocabulary without the conversation.**
Writing Given/When/Then syntax without actually discussing the scenarios with the relevant people. The format is just notation. The value is the shared understanding that produces it.

**Overly generic scenarios.**
"Given a user, When they do something, Then it works." Scenarios need specific, concrete conditions. Use real examples with realistic data. "Given an associate with ID 12345 who clocked in at 08:00" is more useful than "Given a clocked-in associate."

**Forgetting the negative scenarios.**
For every "when it works" scenario, there should be at least one "when it doesn't" scenario. What happens with invalid input? Missing data? Expired sessions? Service outages?

**Skipping the Given interrogation.**
Writing scenarios that share a single assumed precondition ("Given a valid user") and vary only the When and Then. The most common bugs live in the Givens you didn't think to challenge. For any scenario you write, ask: "what other states could the world be in when this When fires?" If you don't get at least one uncomfortable answer, you haven't had the conversation yet.

</common-mistakes>

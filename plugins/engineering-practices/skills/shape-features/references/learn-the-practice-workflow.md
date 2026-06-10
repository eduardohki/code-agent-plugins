# Workflow: Learn Requirements Practices

## Required Reading

**Read this reference file NOW before proceeding:**
1. [essential-principles.md](essential-principles.md) - the five non-negotiable principles for this skill

**Read when you reach the relevant topic in Step 2:**
2. [what-good-looks-like.md](what-good-looks-like.md) - Read before Topic A (what good requirements look like).
3. [common-pitfalls.md](common-pitfalls.md) - Read before Topic B (common pitfalls). Contains the pitfalls the teaching paths route through.
4. [bdd-and-acceptance-criteria.md](bdd-and-acceptance-criteria.md) - Read before Topic C (writing acceptance criteria with BDD).
5. [story-slicing-and-sizing.md](story-slicing-and-sizing.md) - Read before Topic D (slicing and sizing).

For the "full tour" path, all topic references will be needed - read each as you reach its topic.

**Read situationally:**
6. [adapting-to-your-context.md](adapting-to-your-context.md) - Read when the user asks how to apply these practices in a team or org that does not already follow them, or when teaching someone who is operating alone inside a less-mature organisation. Provides the lone-practitioner framing for every principle.
7. [known-limitations.md](known-limitations.md) - Read when the user asks where this skill does NOT apply, or when teaching should include the honest caveats alongside the advice. Prevents the learner from walking away thinking these practices apply universally.

## Context

The user wants to understand what makes good requirements and what common mistakes to avoid. They may be:
- New to writing requirements and want to learn the fundamentals
- Experienced but want to check their understanding against best practices
- Looking for specific guidance on a particular aspect (BDD, slicing, pitfalls)
- Preparing to teach or coach their team on requirements practices
- An AI agent building context on how to produce or evaluate requirements

This workflow is educational, not productive. The output is understanding, not a requirements document. But it should offer to transition into the `shape-new-feature` or `review-existing` workflow if the user wants to apply what they learned.

## Process

## Step 1: Understand what the user wants to learn

*Why this step matters: dumping every pitfall and every characteristic at once is overwhelming and forgettable. Targeting the user's actual situation is what turns a reference tour into useful learning.*

Ask the user:

"What would you like to focus on?"

1. **The full picture** - Walk through everything: what good looks like, common pitfalls, BDD, slicing, and the multi-audience test
2. **Common pitfalls** - What goes wrong with requirements and why
3. **Writing acceptance criteria** - How to use BDD and Given/When/Then effectively
4. **Slicing and sizing** - How to make stories small enough to deliver rapidly
5. **How different audiences read a requirement** - The multi-audience perspective
6. **Something specific** - Ask me a question

If the user already stated a specific interest (e.g., "teach me about BDD"), skip this question and go to the relevant section.

## Step 2: Deliver the content

**Pacing (non-negotiable in this workflow):** deliver one characteristic, pitfall, or sub-topic per message - at most a cluster of two or three when they are closely related. End each chunk with a brief check-in ("Clear so far?" / "Want to see this one in your own domain?") before continuing. Depth is pull-based: give the short version, offer the expansion. A full topic delivered in one message is not teaching; it is scrolling.

For each topic, follow this teaching structure:

1. **What good looks like** - The positive pattern with a concrete example
2. **What people commonly get wrong** - The specific pitfall (from [common-pitfalls.md](common-pitfalls.md))
3. **Why the pitfall actually hurts** - The real-world consequence, not just "it's bad practice"
4. **What to do instead** - The correction with a concrete example
5. **The same requirement through different eyes** - Show how a product owner, developer, tester, and AI agent each read the same example differently

The "why" is the most important part. Anyone can memorize a list of do's and don'ts. Understanding the reasoning is what changes behavior.

### Topic A: What good requirements look like

Walk through the 12 characteristics from [what-good-looks-like.md](what-good-looks-like.md), in clusters of two or three per message with a check-in between clusters. For each one, give a brief concrete example.

Anchor with the multi-audience test: "A requirement is well-written when any consumer - product owner, developer, tester, AI agent - can independently determine whether the work is complete."

### Topic B: Common pitfalls

Don't dump all pitfalls at once. First, identify the user's situation by asking (or inferring from their earlier messages):

> "Which best describes where you are?
> **a.** New to writing requirements - I want the fundamentals.
> **b.** Experienced, but I'm struggling to get the team / stakeholders to work this way.
> **c.** I'm working with AI agents (authoring or consuming requirements) and want the AI-specific traps.
> **d.** I want the full tour - cover everything."

Then route to the matching path. Each path references pitfalls from [common-pitfalls.md](common-pitfalls.md):

| User situation | Cover these pitfalls, in order | Why these first |
|----------------|-------------------------------|-----------------|
| **a. New to requirements** | 1 (solutions), 2 (imperative criteria), 3 (stories too large), 6 (missing edge cases), 13 (no value statement) | These are the fundamentals - every new author hits them, and they're visible in output. Teach them first, everything else builds on them. |
| **b. Struggling with team buy-in** | 4 (horizontal slicing), 5 (slicing by activity), 9 (no Definition of Ready), 7 (refining too far ahead), 10 (BDD as testing tool) | These are the process-level traps that make the team reject "good" requirements. You need team agreement on these to practice the rest. |
| **c. Working with AI** | 15 (AI can infer what you meant), 16 (accepting AI-authored requirements without adversarial review), then 2 (imperative criteria) and 12 (hidden assumptions) | AI magnifies imprecision and fills gaps with plausible nonsense. Teach the AI-specific traps first, then the base-level traps AI makes worse. |
| **d. Full tour** | 1, 2, 3, 6, 13 → 4, 5, 9, 10 → 7, 8, 11, 12 → 15, 16 | Progressive difficulty: fundamentals → structural → subtle → AI. |

If the user picks a path but mid-walkthrough raises a concern that lives in a different path (e.g. path-a user asks about AI), jump to the relevant pitfall - don't force them through in order.

### Topic C: Writing acceptance criteria with BDD

Cover from [bdd-and-acceptance-criteria.md](bdd-and-acceptance-criteria.md):

1. BDD is a conversation, not a syntax
2. The Given/When/Then structure and what each part means
3. Declarative vs. imperative - with side-by-side examples
4. Using criteria to negotiate scope and split stories
5. The subscription renewal example showing progressive slicing

**Worked example - the same feature, bad then good:**

Bad:
```
As a user, I want to access my account so that I can see my details.

Acceptance criteria:
- User can log in
- Error shown for wrong password
```

Good:
```gherkin
Feature: Account authentication

  Scenario: Successful authentication with valid credentials
    Given I am a registered user with an active account
    When I authenticate with valid credentials
    Then I am taken to my account dashboard

  Scenario: Authentication fails with invalid credentials
    Given I am a registered user
    When I authenticate with incorrect credentials
    Then I am shown an authentication error
    And I am not granted access
    And the failed attempt is logged

  Scenario: Account locked after repeated failures
    Given I am a registered user
    And I have failed authentication 5 times within the last 15 minutes
    When I attempt to authenticate again
    Then I am informed my account is temporarily locked
    And I am told when I can try again
```

Show what changed and why each change matters.

### Topic D: Slicing and sizing

Cover from [story-slicing-and-sizing.md](story-slicing-and-sizing.md):

1. The 2-day target and why it's a constraint, not a goal
2. Vertical vs. horizontal slicing with the order search example
3. The INVEST criteria
4. The dependency math (1 in 2^n)
5. When and how to use spikes
6. Edge case mapping checklist
7. Non-functional requirements

### Topic E: The multi-audience perspective

Take a single well-written requirement and show how each audience reads it:

```gherkin
Feature: Order cancellation

  Scenario: User cancels an order before fulfilment
    Given I have an open order that has not yet been shipped
    When I request to cancel the order
    Then my order is closed with status "Cancelled by user"
    And any payment authorisation held against the order is released
    And I receive a written confirmation of the cancellation
```

- **Product owner reads:** "Yes - users can cancel pre-shipment orders and we release the payment authorisation. That's the behavior we need."
- **Developer reads:** "I need to check order status, update it to Cancelled, release the payment authorisation, and send a confirmation. Clear boundaries."
- **Tester reads:** "I need to test: cancellation before shipment (happy path), cancellation attempt after shipment has started (should fail or require manual approval), confirmation delivery method, authorisation release timing."
- **AI agent reads:** "The preconditions, actions, and expected outcomes are unambiguous. I can generate implementation and tests that satisfy these criteria."

Then show the SAME feature written poorly and walk through how each audience fails:

```
Order cancellation feature
- Users should be able to cancel orders
- Payment authorisation should be released
```

- **Product owner:** "Which orders? Can they cancel after shipment? Is there a time window?"
- **Developer:** "When is the authorisation released? What if the payment system is unavailable? What's the confirmation channel?"
- **Tester:** "What do I test? What's a valid cancellation? What should fail?"
- **AI agent:** "I'll implement something plausible. It may not be what anyone wanted."

## Step 3: Offer to practice

*Why this step matters: understanding a principle in the abstract is different from applying it to your own work. Transitioning into the `shape-new-feature` or `review-existing` workflow is where the learning converts into a buildable requirement.*

After covering the requested topics, ask:

"Would you like to practice with a real feature? I can help you:
1. **Shape a new feature** you're working on
2. **Review requirements** you've already written

Or if you have questions about anything we covered, ask away."

If the user wants to shape a new feature or review existing requirements, transition to the appropriate workflow.

## Success Criteria

This workflow is complete when:
- [ ] User's learning interest was identified
- [ ] Relevant topics were covered with concrete examples
- [ ] Each pitfall included the reasoning (WHY it hurts), not just the rule
- [ ] Multi-audience perspective was demonstrated
- [ ] User was offered the opportunity to practice with real work

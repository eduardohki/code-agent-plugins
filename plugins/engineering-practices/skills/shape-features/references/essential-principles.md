# Essential Principles

These principles apply to ALL workflows in the `shape-features` skill. They are non-negotiable. Every workflow loads this file as required reading.

## 1. Requirements are shared understanding made visible.

The value is in the conversations that produce shared understanding among everyone involved - product owners, analysts, developers, testers, and AI agents. Writing requirements down is how you verify those conversations happened. A filled template with no real discussion behind it is structured fiction.

## 2. If you can't verify it, you don't understand it.

Every requirement must express expected behavior in a way that can be independently verified by any reader. If you cannot write a concrete scenario for it, the right conversations haven't happened yet. This applies equally to a tester writing test cases, a developer checking their implementation, and an AI agent deciding if its output is correct.

## 3. Describe the behavior change, not the implementation.

Requirements state WHAT behavior is needed and WHY. Never HOW. This prevents product owners from prescribing UI widgets, developers from embedding technical choices into requirements, and AI agents from over-interpreting vague instructions. The "how" is decided by whoever implements the work.

## 4. Smaller is always better - because being wrong cheaply beats being right slowly.

Large requirements hide complexity, assumptions, and risk. The cost of being wrong grows with the size of the bet: a 2-day story that ships wrong costs 2 days to discover and 2 days to fix (the context is fresh and the people are still there); a 2-month story that ships wrong costs a quarter - by then the reasoning has left the building with the people who held it.

Every story should go from "started" to "in production" in under 2 days. The target is not 2 days for its own sake; it is "the smallest increment that teaches you something you didn't know." In a team that deploys daily, that is roughly 2 days. Adapt the number to your cadence, but not to your comfort. See [story-slicing-and-sizing.md](story-slicing-and-sizing.md) for how to apply the rule when your pipeline or your org does not match the ideal.

## 5. Requirements age like milk - but not all at the same rate.

Don't refine work far in advance. Elaborate requirements just in time, when the team is about to start. Priorities shift, context changes, and detailed requirements written months ago will need to be redone. Batch the refinement close to the work.

Two caveats worth holding in mind:

- *Product-derived requirements age fastest.* What users want, how the competitive landscape looks, how your own product priorities sit - these move on a scale of weeks. Treat anything in this bucket as perishable by default.
- *External-constraint requirements age more slowly - but when they move, they move sharply.* Things like third-party API contracts, regulatory constraints (where they apply), platform policies, or integration agreements tend to be stable for months or years, then change abruptly. The risk is not constant drift; it is the step change you miss because you assumed the constraint was still the constraint you last checked. When you revisit a spec that depends on an external contract, don't just ask "is this still current?" - ask whoever owns the relationship with that external party.

## The multi-audience test

A requirement is well-written when every consumer can read it and independently determine whether the work is complete. If any one of them can't, the requirement needs more work. This applies regardless of whether a human or an AI produced the requirement.

**The core four** - always required, because they are in the loop of authoring and delivery:

- **Product owner** - confirms the value and behavior match intent.
- **Developer** - can start work without clarifying questions.
- **Tester** - can derive test cases from the acceptance criteria alone.
- **AI agent** (when in the loop) - can produce correct code without inventing defaults.

**The audiences who pay for our mistakes** - check whichever apply:

- **Operator / support** - can diagnose when the feature misbehaves in production.
- **Security / privacy** - can confirm the data flow, authorisation, and audit stance are acceptable.
- **End-user** (if different from the buyer) - would describe the feature the same way the PO does.
- **Downstream teams / integrators** - know the contract is changing and have agreed to it.

You do not always need signatures from the second group. You do need their *question* to have been asked by someone. "Not applicable" without a reason is the symptom the test exists to catch.

## Common rationalizations - curiosity prompts, not dogma

When you catch yourself - or the user - saying any of these, it is a signal that a principle is under pressure. The rationalization is not automatically wrong; sometimes the shortcut is the right call. But the right call is the one you arrive at by *asking the question*, not the one you take by default.

Each row below gives you the question to ask. If the answer comes back clean in under a minute, the shortcut is fine. If the answer is evasive, or if you find yourself arguing with the question instead of answering it, the principle wins.

| Rationalization | Principle under pressure | Ask yourself instead |
|-----------------|--------------------------|-----------------------|
| *"This one is small enough, we can skip slicing."* | #4 Smaller is always better | "What would the smaller-than-this version be?" Give it 60 seconds. If no smaller version comes to mind, the current size is fine. If one does, you had two stories. |
| *"The stakeholder said it this way, so we should keep the implementation detail in the requirement."* | #3 Describe behavior, not implementation | "If we built a completely different solution that delivered the same value, would the stakeholder accept it?" If yes, the detail is a preference - move it to a "suggested approach" note. If no, the detail *is* the requirement - and you should say so explicitly, not smuggle it in as an AC. |
| *"These requirements are only 3 months old - close enough to current."* | #5 Requirements age like milk | "What has changed in the last 3 months - in priorities, in the team, in the product, in the surrounding constraints?" If the answer is "nothing we care about," proceed. If the answer is "actually, several things," the age matters. |
| *"We don't need a scenario for that edge case - it's obvious."* | #2 If you can't verify it, you don't understand it | "Obvious to whom?" Pick the audience least likely to share your assumptions (a new hire, a tester from another team, an AI agent). If you cannot describe the edge case to *them* in one sentence, it is not obvious. |
| *"We can fill in the acceptance criteria later."* | #1 Requirements are shared understanding | "Which specific conversation are we deferring, and with whom?" If the answer is "I just haven't written it up yet," fine - write it up. If the answer is "we haven't had it," the story is not ready, regardless of what the template looks like. |
| *"Happy path is enough for now - we'll add edge cases if they come up."* | #2 verifiability | "What state could the world be in, other than the one I assumed, when this When fires?" (This is the Given interrogation from the BDD reference.) If you get no uncomfortable answers, the happy path really is enough. If you get one, you have a missing scenario. |
| *"It's a technical story - the value statement doesn't really apply."* | #1 Shared understanding | "Who is worse off if we don't do this, and in what specific way?" If you can answer in a sentence, write *that* as the value statement. If you cannot, the story may be premature - investigate before building. |
| *"The AI will figure out the rest from context."* | #3 + verifiability | "If I handed this to a new team member with no context, what would they misunderstand?" The AI's failure modes are similar but more confident. If a human would misread it, the AI will build it wrong and sound certain about it. |
| *"This is a generic feature - it'll work for everyone."* | #2 verifiability + the multi-audience test | "Which user, environment, or context am I picturing as the default when I read this back, and what would change if I pictured a different one?" If the answer is "nothing," the scope genuinely is universal - say so explicitly. If the answer is "several things," those differences are either in the acceptance criteria or they are missing, and whoever uses the feature in the other context will discover them later. |

**How to use this table.** When the rationalization shows up, stop writing. Ask the question out loud (or write the answer down). You are not trying to win an argument with yourself - you are trying to surface whether the shortcut is justified. If the answer comes easily and survives the next five minutes, take the shortcut. If you find yourself negotiating with the question, the principle was under real pressure and deserves the longer path.

Rules without curiosity produce compliant requirements that are secretly wrong. Curiosity without rules produces endless discussion. This table exists to make the two work together.

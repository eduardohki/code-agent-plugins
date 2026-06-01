# Glossary

Short definitions for terms used throughout this skill. Consult this file if any term in a workflow is unfamiliar. Definitions are plain-language, not formal specifications - use the linked reference files for depth.

## Requirements & artefacts

- **User story / story** - The smallest unit of work that delivers observable value to a specific actor. Usually expressed as "As a [role], I want [behavior], so that [value]."
- **Epic** - A large body of work that cannot be delivered as a single story. Epics are decomposed into stories before they are scheduled for work.
- **Backlog** - The prioritised list of stories, epics, bugs, and other work waiting to be delivered.
- **Refinement** - The ongoing activity of breaking down, clarifying, and sizing backlog items before they are worked on. Also called "grooming" or "discovery."
- **Feature specification / PRD** - A document that describes a feature's intended behavior, often as input to story-level requirements.
- **Non-functional requirement (NFR)** - A quality attribute of the system - performance, security, accessibility, observability, data handling - as opposed to a behavior.
- **Spike** - A time-boxed investigation (typically 1-3 days) used to answer an open question or reduce uncertainty before committing to a story. A spike has its own acceptance criteria: "at the end we will know X."

## Acceptance criteria & BDD

- **BDD (Behavior Driven Development)** - A collaborative practice of exploring a feature's behaviors in conversation and capturing them as concrete, testable scenarios. This skill focuses on BDD as a *requirements* practice, not a testing framework.
- **Acceptance criteria** - The observable conditions that determine whether a story is done. Should be specific and verifiable, not aspirational.
- **Gherkin** - The plain-text syntax commonly used to write BDD scenarios (`Feature:`, `Scenario:`, `Given/When/Then`).
- **Given / When / Then** - The three parts of a scenario. *Given* = the starting state, *When* = the action or event, *Then* = the observable outcome. `And` and `But` extend any of the three.
- **Declarative vs imperative criteria** - Declarative describes *what* should happen ("the user is granted access"). Imperative describes *how* ("user clicks Sign In, waits 2s, sees welcome banner"). Requirements should be declarative; imperative language belongs in UI test scripts.
- **Three amigos** - The shorthand for a refinement conversation that includes a business representative, a developer, and a tester. In this skill, an AI agent consuming the output counts as a fourth amigo - it must be able to read the same scenarios and reach the same understanding.
- **Example mapping** - A structured conversation technique that uses four card types (story, rules, examples, questions) to surface behaviors and unknowns before writing scenarios.
- **Specification by example** - The broader practice of capturing requirements through concrete examples. BDD is one form of it.

## Sizing, slicing & flow

- **Vertical slice** - A story that delivers observable end-to-end behavior - touching every layer it needs to (UI, API, DB, etc.) - instead of being scoped to one layer.
- **Horizontal slice / slicing by layer** - Slicing a feature into separate stories per technical layer (one story for DB, one for API, one for UI). Discouraged: no slice on its own delivers value, and all three must ship together to test anything end-to-end.
- **Slicing by activity** - Splitting work into "coding story / testing story / review story." Discouraged for the same reason.
- **INVEST** - A checklist for a well-formed story: **I**ndependent, **N**egotiable, **V**aluable, **E**stimable, **S**mall, **T**estable.
- **The 2-day rule** - This skill's concrete sizing constraint: every story should go from "started" to "in production" in under two days. If it can't, it needs more decomposition.
- **Dependency math** - If a delivery depends on N parallel stories all landing successfully, the probability of hitting the original date decays geometrically (~1 in 2^N). Small, independent slices dominate big coordinated ones.

## Happy paths, edge cases, unhappy paths

- **Happy path** - The most common, expected flow through the feature.
- **Edge case** - A condition at the boundary of normal usage (empty list, maximum value, concurrent access, first use, last item).
- **Unhappy path** - A flow where something goes wrong (invalid input, a dependency fails, the user abandons midway). Requirements must cover at least one genuine unhappy path per behavior.

## Quality gates

- **Definition of Ready (DoR)** - The team-agreed bar a story must meet before work can start. Typical DoR includes: clear value, acceptance criteria, size ≤ the agreed target, dependencies known.
- **Definition of Done (DoD)** - The team-agreed bar a story must meet before it is considered delivered. Typical DoD includes: acceptance criteria pass, non-functional requirements met, observable in production.
- **Multi-audience test** - This skill's signature quality test. A requirement is well-written when every consumer - product owner, developer, tester, AI agent - can independently determine whether the work is complete. If any one of them can't, the requirement needs more work.

## Authoring & review

- **AI-authored requirement** - A requirement generated or drafted by an AI agent. Always subject to human review per this skill's guidance; see [review-examples.md](../assets/review-examples.md) and the AI-specific checks in the review workflow.
- **Rationalization** - A plausible-sounding excuse for skipping a principle ("this one is small enough", "we can add edge cases later"). See the rationalization table in [essential-principles.md](essential-principles.md).


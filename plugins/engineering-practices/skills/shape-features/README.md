# shape-features

A Claude Code skill that guides anyone involved in defining software features toward producing effective, testable, deliverable requirements.

## Why this skill exists

Most software problems aren't coding problems. They're requirements problems. Work gets built to the wrong spec, reworked after delivery, or abandoned mid-flight because the requirements were vague, too large, untestable, or understood differently by different people.

This skill exists because the gap between "we know what we want" and "we have requirements good enough to build from" is where most waste occurs. That gap affects everyone: product owners who struggle to express intent without prescribing solutions, developers who start work on stories they don't fully understand, testers who discover missing scenarios after the code is written, and AI agents that confidently build the wrong thing from ambiguous instructions.

## Who it's for

- **Product owners** articulating what needs to be built and why
- **Business analysts** decomposing goals into testable behaviors
- **Developers** evaluating whether a story is clear enough to start working on
- **Testers** checking whether acceptance criteria are complete and verifiable
- **AI agents** consuming or producing requirements as part of automated workflows

The skill uses role-neutral language. It doesn't assume the user is a developer. It doesn't assume the user knows what "good" looks like - in fact, teaching the user what "good" looks like *is* one of the skill's primary goals. It meets people where they are.

### Primary usage context

The skill is designed for any team defining software features, from solo developers to large organisations. It is role-neutral - it works for product owners, business analysts, developers, testers, or AI agents drafting or reviewing requirements. It does not assume a specific industry, organisation size, or process maturity. The principles travel; the ceremonies are optional.

Where governance partners (security, privacy, compliance, operations) are part of how an organisation ships software, the skill does not duplicate those gates. What it does do is prompt the author to surface the right questions early enough that those partners can be consulted while the cost of change is still low - rather than discovering the question at rollout, when it isn't.

## What it does

Three workflows:

1. **Shape a new feature** - Guides a conversation to produce well-formed requirements for a new feature, starting from any input (a rough idea, meeting notes, a product brief, a stakeholder request). Triages input quality first, then works through value, behaviors, acceptance criteria, edge cases, non-functional requirements, slicing, and risk - and closes with concrete next-step suggestions (a review pass, relevant downstream engineering skills, or spikes for open unknowns).

2. **Review existing** - Audits existing requirements against a quality standard. Leads with a verdict, then walks through findings one at a time - what's wrong, why it matters, what to do instead - applying accepted rewrites directly to the source document. Includes specific checks for AI-authored requirements, and closes with an explicit recommendation: rework via the `shape-new-feature` workflow, revise inline, or proceed to implementation with the right downstream skill.

3. **Learn the practice** - Educational walkthrough of what good requirements look like, common pitfalls, and the reasoning behind the practices. Uses concrete examples and shows how the same requirement reads to different audiences. Offers to transition into the `shape-new-feature` or `review-existing` workflow once the user wants to apply what they learned.

All three workflows share the same delivery stance: the skill is an invitation to learn, so the reasoning behind each step is shared in small doses, attached to the question or finding it motivates - never as a lecture. Depth is offered rather than imposed, artifacts (specs, full reviews) go to files while the chat stays conversational, and the workflows check in between chunks instead of pushing on. A wall of text is treated as a delivery defect, not as thoroughness.

## The thinking behind it

The practices in this skill are grounded in established software delivery thinking - particularly around Behavior Driven Development, continuous delivery, work decomposition, and flow optimization. Key sources include the Dojo Consortium's published guidance on work decomposition, BDD, story slicing, and delivery metrics, as well as the broader body of work on specification by example, acceptance test driven development, and small-batch delivery.

The core beliefs:

- **Requirements are shared understanding made visible.** The artifact matters less than the conversation that produced it. But without the artifact, you can't verify the conversation happened.

- **If you can't verify it, you don't understand it.** Testable acceptance criteria are not optional. They're how you know whether you're done.

- **Smaller is always better - because being wrong cheaply beats being right slowly.** The cost of being wrong grows with the size of the bet. Stories that can go from started to production in under two days force clarity, reduce risk, and enable course correction. 2 days is not a sacred number - the feedback cycle is. The rule is "wrong cheaply"; 2 days is its typical form in a team that deploys daily.

- **Describe behavior, not implementation.** Requirements say what and why. The how belongs to whoever builds it.

- **Requirements age like milk - but not all at the same rate.** Product-derived requirements age in weeks. External-constraint requirements (third-party APIs, platform policies, integration contracts) age more slowly but move sharply when they move.

The skill doesn't mention specific authors or methodologies by name. The principles are synthesized into its own voice. The emphasis throughout is on **why** - not just "do this" but "because if you don't, this specific thing goes wrong."

## What "good" means in this skill

A requirement is good when every consumer can read it and independently determine whether the work is complete. That's the test. Everything else (BDD, Gherkin, INVEST, vertical slicing) is technique in service of that outcome.

The "consumers" come in two tiers. The core four - product owner, developer, tester, AI agent - are always in the authoring loop and their sign-off is always required. A second tier - operator/support, security/privacy, end-user (when different from the buyer), downstream teams - rarely sit at the table when requirements are written, but they are the people who pay for our mistakes. You do not always need their signature; you do need their *question* to have been asked by someone. The template's multi-audience sign-off section enforces both.

This test is not aspirational. It is a four-item sub-check in the `shape-new-feature` workflow's quality gate and a dedicated sign-off section at the bottom of the output template. A requirement that cannot be signed off by all four audiences is not ready to build - it needs more refinement, not more building.

## When this skill is *not* the right tool

The skill is for producing, auditing, or learning about *requirements*. Skip it when the request is actually about:

- **Code review or refactoring** - a different task; use a dedicated code-review skill if one is available in your environment, or handle inline.
- **Typos, formatting, or phrasing fixes** inside an existing document - edit directly.
- **Testing strategy or test plans** - a different task; use a dedicated testing-strategy skill if available.
- **Architecture or system design** - a different task; use a dedicated architecture or system-design skill if available.
- **A single clarifying question on in-flight work** - just answer it.

`SKILL.md` contains the same boundary in machine-readable form so the router can short-circuit obvious misfires.

## Structure

```
skills/shape-features/
├── README.md                         # This file - intent, audience, design rationale
├── SKILL.md                          # Intake, routing, when-not-to-use, shared-principles pointer
├── references/
│   ├── shape-new-feature-workflow.md          # Workflow: guided conversation to produce well-formed requirements
│   ├── review-existing-workflow.md            # Workflow: structured audit of requirements against quality standards
│   ├── learn-the-practice-workflow.md         # Workflow: educational walkthrough with examples, pitfalls, and reasoning
│   ├── essential-principles.md       # The five non-negotiable principles + rationalization-as-curiosity table
│   ├── glossary.md                   # Plain-language definitions of recurring terms
│   ├── adapting-to-your-context.md   # Lone-practitioner framing + exit clause for unsustainable environments
│   ├── known-limitations.md          # Honest list of contexts where the skill gives bad or incomplete advice
│   ├── what-good-looks-like.md       # Quality characteristics + checklist
│   ├── common-pitfalls.md            # Anti-patterns + red-flag phrases
│   ├── bdd-and-acceptance-criteria.md # Three questions (Given/When/Then) framed as interrogation, not syntax
│   ├── story-slicing-and-sizing.md   # 2-day rule framed as economics (be wrong cheaply); vertical slicing
│   ├── worked-example.md             # Fully filled feature-requirements template, incl. participants & sign-off
│   └── review-examples.md            # Findings, rewrites, AI-authorship AND human-authorship failure modes
└── assets/
    └── feature-requirements.md       # Output structure: Participants, multi-audience sign-off
```

Uses the router pattern: SKILL.md routes to the appropriate workflow by intent (asking only when intent is ambiguous). Each workflow opens with a Required Reading section - always including `references/essential-principles.md`, plus the domain references relevant to that workflow, loaded just-in-time at the step that needs them. This keeps any single file focused, keeps the essential principles load-on-demand rather than on every SKILL.md read, and ensures the right context is loaded for the task at hand.

A `references/glossary.md` is included as a plain-language reference for recurring terms (BDD, Gherkin, INVEST, spike, DoR, vertical slice, etc.). Workflows do not define these terms inline - the glossary is the backstop for anyone who needs one.

`references/adapting-to-your-context.md` is loaded situationally - when a user signals they are operating alone inside an organisation that does not practice the ideal ceremonies (no three amigos, no DoR, no 2-day delivery, no Gherkin tooling). The skill's goal is to help that individual practitioner learn and improve *their* practice, not to reorganise their company. This reference explains how the principles travel without the ceremonies, introduces a `self-reviewed` sign-off mode for when real audiences are unreachable, gives guidance for hand-offs, spikes, and refinement in the real world, and closes with an honest exit clause for when the lone-practitioner mode stops being sustainable. It is referenced from each workflow's Required Reading section as situational reading.

`references/known-limitations.md` is the skill's honesty file - the list of contexts where its own advice is known to be incomplete, wrong, or actively unhelpful (regulated/contractually-fixed domains, platform work, discovery phases, authority mismatches, environments where the skill is being gamed, domains outside the English-speaking tech idiom). It is loaded situationally, when a user's situation looks like it may fall outside what the skill handles well. A skill that knows its own edges is more trustworthy than one that pretends to have none.

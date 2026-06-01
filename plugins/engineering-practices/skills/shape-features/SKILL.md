---
name: shape-features
description: >-
  Use when a user needs to produce, audit, or learn about software
  requirements - writing user stories, defining acceptance criteria,
  creating feature specs, turning meeting notes / PRDs / Jira or Linear
  tickets into actionable stories, auditing or refining a backlog,
  asking about BDD, story slicing, and vertical slicing, or structuring
  requirements as input for AI code generation.
---

# Shape Features

## Overview

Guided workflow for producing, auditing, or learning about software requirements. Routes to one of three sub-workflows based on the user's goal.

## Quick Reference

| Goal | Workflow |
|------|----------|
| Shape a new feature from any input | [shape-new-feature-workflow.md](references/shape-new-feature-workflow.md) |
| Audit or review existing requirements | [review-existing-workflow.md](references/review-existing-workflow.md) |
| Learn what good requirements look like | [learn-the-practice-workflow.md](references/learn-the-practice-workflow.md) |

| Reference | When to load |
|-----------|--------------|
| [essential-principles.md](references/essential-principles.md) | Required for every workflow |
| [worked-example.md](assets/worked-example.md) | Before producing output |
| [adapting-to-your-context.md](references/adapting-to-your-context.md) | User's org lacks the ideal ceremonies |
| [known-limitations.md](references/known-limitations.md) | Feature may fall outside the skill's defaults |

## Intake

What would you like to do?

1. **Shape a new feature** - collect and structure its requirements
2. **Review/audit** existing requirements
3. **Learn** what good requirements look like (and common pitfalls)

The user may arrive with meeting notes, product briefs, external documentation, or a rough feature idea. All starting points are valid - the skill steers toward quality regardless of where the input came from.

*Not sure which applies?* Starting fresh on a new feature → 1. Already have stories / a PRD / backlog items you want checked → 2. No specific feature yet, want to understand the practice first → 3.

**Wait for response before proceeding.**

## Routing

| Response | Next Action | Workflow |
|----------|-------------|----------|
| 1, "shape", "collect", "new", "feature", "write", "create", "define" | Proceed directly | [shape-new-feature-workflow.md](references/shape-new-feature-workflow.md) |
| 2, "review", "audit", "check", "existing", "evaluate", "assess" | Ask user to provide their requirements | [review-existing-workflow.md](references/review-existing-workflow.md) |
| 3, "learn", "understand", "teach", "pitfalls", "good", "practices" | Proceed directly | [learn-the-practice-workflow.md](references/learn-the-practice-workflow.md) |

**Intent-based routing (if user provides clear intent without selecting a number):**
- User pastes requirements or a feature spec → route to review workflow
- User describes a feature they want to build → route to shape-new-feature workflow
- User asks "what makes good requirements" or similar → route to learn workflow
- User provides meeting notes or a product brief → route to shape-new-feature workflow (treat as input material)
- User has a feature in production and wants to refine the next iteration → route to review workflow (audit first, then re-plan)

**After reading the workflow, follow it exactly.**

## When Not to Use

Skip this skill if the request is actually about:

- **Code review or refactoring** - a different task; use a dedicated code-review skill if one is available in your environment, or handle inline.
- **Typos, formatting, or phrasing fixes** inside an existing document - edit directly.
- **Testing strategy or test plans** - a different task; use a dedicated testing-strategy skill if available.
- **Architecture or system design** - a different task; use a dedicated architecture or system-design skill if available.
- **A single clarifying question on in-flight work** - just answer it; don't launch the full flow.

If in doubt, ask the user which of the three workflows they want before routing.

## Shared Principles

**REQUIRED READING for every workflow:** [essential-principles.md](references/essential-principles.md) - five non-negotiable principles that govern all three workflows.

**The load-bearing test (memorize this):** A requirement is well-written when every consumer - product owner, developer, tester, AI agent, plus the people who pay for our mistakes (operators, security, end-users, downstream teams) - can read it and independently determine whether the work is complete. If any one of them can't, the requirement needs more work. See [essential-principles.md](references/essential-principles.md) for the full audience list and when each applies.

## Reference Index

All domain knowledge in `references/`:

**Essential principles:** [essential-principles.md](references/essential-principles.md) - REQUIRED. The five non-negotiable principles that apply to every workflow, plus a curiosity table of common rationalizations.
**Glossary:** [glossary.md](references/glossary.md) - Plain-language definitions of recurring terms (BDD, Gherkin, INVEST, spike, DoR, vertical slice, etc.). Consult when a term is unfamiliar.
**Adapting to your context:** [adapting-to-your-context.md](references/adapting-to-your-context.md) - How a single practitioner applies the principles when their team or organisation does not practice them. Read when the user is operating alone inside a less-mature org, when the ideal ceremonies (three amigos, DoR, 2-day delivery, Gherkin tooling) are not available, or when the user signals the skill's defaults do not fit their reality. Includes an exit clause for when the practice is not sustainable in the current environment.
**Known limitations:** [known-limitations.md](references/known-limitations.md) - Honest list of contexts where this skill gives bad or incomplete advice (regulated/contractual domains, platform work, discovery phases, authority mismatches, gamed environments). Read when the user's situation feels like a mismatch for the skill's defaults and you need to decide whether to adapt or step aside.
**Quality standards:** [what-good-looks-like.md](references/what-good-looks-like.md)
**Anti-patterns:** [common-pitfalls.md](references/common-pitfalls.md)
**Acceptance criteria:** [bdd-and-acceptance-criteria.md](references/bdd-and-acceptance-criteria.md) - Leads with the three questions behind every scenario (Given / When / Then as interrogation, not syntax).
**Sizing and decomposition:** [story-slicing-and-sizing.md](references/story-slicing-and-sizing.md) - The 2-day rule framed as economics (be wrong cheaply), not doctrine.
**Worked example:** [worked-example.md](assets/worked-example.md) - A fully filled feature requirements template. Read when producing output or when you need a reference for what "good" looks like.
**Review examples:** [review-examples.md](assets/review-examples.md) - Sample findings and rewrites, plus annotated AI-authorship AND human-authorship failure modes. Read during the review workflow.

## Workflows

| Workflow | Purpose |
|----------|---------|
| [shape-new-feature-workflow.md](references/shape-new-feature-workflow.md) | Guided conversation to produce well-formed requirements from any input |
| [review-existing-workflow.md](references/review-existing-workflow.md) | Structured audit of requirements against quality standards |
| [learn-the-practice-workflow.md](references/learn-the-practice-workflow.md) | Educational walkthrough with examples, pitfalls, and reasoning |

## Assets

| Asset | Purpose |
|-------|---------|
| [feature-requirements.md](assets/feature-requirements.md) | Output structure for a well-formed feature specification |

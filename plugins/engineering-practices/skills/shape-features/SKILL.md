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

Each workflow opens with its own Required Reading section; the full reference list with loading guidance is in the Reference Index below.

## Intake & Routing

**Route directly when intent is clear from the user's message - do not present the menu first:**

- User pastes requirements or a feature spec, or links to one → [review-existing-workflow.md](references/review-existing-workflow.md)
- User describes a feature they want to build → [shape-new-feature-workflow.md](references/shape-new-feature-workflow.md)
- User asks "what makes good requirements" or similar → [learn-the-practice-workflow.md](references/learn-the-practice-workflow.md)
- User provides meeting notes or a product brief → shape-new-feature workflow (treat as input material)
- User has a feature in production and wants to refine the next iteration → review workflow (audit first, then re-plan)

The user may arrive with meeting notes, product briefs, external documentation, or a rough feature idea. All starting points are valid - the skill steers toward quality regardless of where the input came from.

**Only when intent is genuinely ambiguous**, present the menu and wait for a response before proceeding:

> What would you like to do?
>
> 1. **Shape a new feature** - collect and structure its requirements
> 2. **Review/audit** existing requirements
> 3. **Learn** what good requirements look like (and common pitfalls)
>
> *Not sure which applies?* Starting fresh on a new feature → 1. Already have stories / a PRD / backlog items you want checked → 2. No specific feature yet, want to understand the practice first → 3.

| Response | Next Action | Workflow |
|----------|-------------|----------|
| 1, "shape", "collect", "new", "feature" | Proceed directly | [shape-new-feature-workflow.md](references/shape-new-feature-workflow.md) |
| 2, "review", "audit", "check", "existing", "evaluate", "assess" | Ask user to provide their requirements | [review-existing-workflow.md](references/review-existing-workflow.md) |
| 3, "learn", "understand", "teach", "pitfalls", "practices" | Proceed directly | [learn-the-practice-workflow.md](references/learn-the-practice-workflow.md) |

**Non-interactive use:** if invoked by another agent, or in a context where no one can answer questions, skip the menu entirely, route by intent, and record every assumption you would otherwise have asked about in the output's Assumptions section.

**After reading the workflow, follow it exactly.**

## Conversation Style

This skill is an invitation to learn: the user should come away understanding *why* each practice exists, not just complying with it. Walls of text defeat that goal - a lecture delivered in one message gets skimmed, not learned. These rules apply to every workflow:

- **One step per message.** Each workflow step's *Why this step matters* note is user-facing - share it in one or two sentences at the moment the step begins, attached to the question or action it motivates. Never recite the rationale for several steps in a single message.
- **Ask at most two questions per turn.** The workflows list more questions per step than fit in one message; spread them across turns and skip any the input already answers.
- **Depth is pull-based.** Give the short why always; offer the expanded version ("want the economics behind the 2-day rule?") instead of pre-emptively explaining everything. An invitation can be accepted or declined - that is what makes it an invitation.
- **Artifacts go to files, conversation stays in chat.** Write specs, full reviews, and rewrites to a file; in chat, summarize and ask the next question. Never combine a long artifact with a list of questions in one message.
- **Checkpoint between chunks.** When teaching or reviewing multiple items, pause after each chunk and check in before continuing.

## When Not to Use

Skip this skill if the request is actually about:

- **Code review or refactoring** - a different task; use a dedicated code-review skill if one is available in your environment, or handle inline.
- **Typos, formatting, or phrasing fixes** inside an existing document - edit directly.
- **Testing strategy or test plans** - a different task; use a dedicated testing-strategy skill if available.
- **Architecture or system design** - a different task; use a dedicated architecture or system-design skill if available.
- **A single clarifying question on in-flight work** - just answer it; don't launch the full flow.

If in doubt, ask the user which of the three workflows they want before routing.

## Shared Principles

**REQUIRED READING for every workflow:** [essential-principles.md](references/essential-principles.md) - five non-negotiable principles, the multi-audience test (the skill's load-bearing check), and a curiosity table of common rationalizations.

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
**Worked example:** [worked-example.md](references/worked-example.md) - A fully filled feature requirements template. Read when producing output or when you need a reference for what "good" looks like.
**Review examples:** [review-examples.md](references/review-examples.md) - Sample findings and rewrites, plus annotated AI-authorship AND human-authorship failure modes. Read during the review workflow.

## Assets

| Asset | Purpose |
|-------|---------|
| [feature-requirements.md](assets/feature-requirements.md) | Output structure for a well-formed feature specification |

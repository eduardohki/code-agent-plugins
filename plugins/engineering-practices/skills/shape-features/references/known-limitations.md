# Known Limitations

This skill is opinionated. The opinions are grounded in a specific tradition - continuous delivery, Behavior Driven Development, small-batch flow, example mapping - that has produced good outcomes in many contexts. It has not produced good outcomes everywhere, and the authors do not claim it does. This file is the honest list of places where the skill is known to give advice that is incomplete, wrong, or actively unhelpful. A skill that knows its own edges is more trustworthy than one that pretends to have none.

Read this when:
- The user's context feels like a mismatch for the skill's defaults and you cannot tell whether to push back or adapt.
- A workflow is producing output that looks correct but feels wrong.
- You are about to recommend the full practice in an environment where it clearly will not work.

## 1. Regulated, contractually-fixed, or formally-specified domains

The skill assumes requirements are emergent, negotiable, and elaborated just-in-time. In domains where the requirements are contractually fixed before build, or where a regulator mandates a specific specification artefact (medical devices, aerospace, some finance, government procurement), this assumption breaks.

Specifically:
- "Requirements age like milk" is false when the requirement is a contract. It does not age - it is the legal boundary of scope.
- "Elaborate just in time" is incompatible with stage-gated approval processes.
- "Negotiable" is often not true: the requirement was written by someone who cannot be renegotiated with.

The skill has no good workflow for this. Partial answers: use the skill to write a *design-level specification* that interprets the contractual requirement into buildable behaviors, keep the contractual requirement as the outer envelope, and flag any contradictions found between them as risks needing legal/compliance sign-off. But the skill is not a substitute for domain-specific regulatory practice, and should not be used as one.

## 2. Platform, infrastructure, and foundational work

The 2-day rule and vertical slicing both assume every increment has user-visible value. Platform and infrastructure work often does not. Building a service mesh, migrating a database, rewriting a framework - these can be months of work before any user notices.

The skill's advice here is thin. "Slice vertically" can become dishonest when there is no user-visible vertical to slice. Forced vertical slicing in platform work produces either:
- A thin demo slice that does not de-risk the actual migration, or
- "Stories" like *"Configure the staging environment"* which are tasks pretending to be user stories.

Better practice for platform work (which this skill does not fully cover):
- Size by *risk retired per unit time*, not by user value delivered.
- Accept that the "user" of platform work is the team consuming the platform - their feedback is the feedback loop.
- Use ADRs (Architecture Decision Records) and spikes more heavily; use user-story format less.
- Be suspicious of anyone applying a user-story skill to platform work without modification.

## 3. Discovery of entirely new problems vs. defining known problems

This skill assumes the problem is roughly understood and the work is to produce a buildable specification of the solution. It is a *solution-shaping* skill.

It is not a discovery or research skill. When the user does not yet know what problem they are solving, or when the user is exploring whether a problem exists at all, the skill will push them to produce premature specificity: concrete scenarios for behaviors that may not be the right behaviors, user stories for users who may not exist, acceptance criteria that lock in assumptions the user has not yet tested.

For discovery work, the skill should be used later or not at all. Better tools for that phase: user interviews, concept testing, problem-statement workshops, Wardley mapping. This skill can document what you learned from those activities, but it cannot replace them.

## 4. Teams that do not have the authority the skill assumes

The skill speaks to a product owner who can negotiate scope, slice stories, push back on stakeholders, and defer non-critical acceptance criteria. Many people with the PO title have none of these powers. They are scribes, not authors; translators, not decision-makers.

In those contexts, advice like "negotiate the slice with your PO" or "defer the edge case to a follow-up story" assumes a political reality the user does not inhabit. The skill does not address this directly. The [adapting-to-your-context.md](adapting-to-your-context.md) reference partially helps, but it frames the problem as a tooling/ceremony gap, when the actual gap is often authority.

We do not have a good answer for this. The honest advice is: if you do not have the authority the skill assumes, use the skill to produce *private* clarity for yourself, communicate in whatever format your organisation rewards, and note in the Risks & Unknowns section the constraints you could not negotiate. Do not pretend the skill gave you a sword it did not give you.

## 5. The skill assumes good faith from the authoring team

The rationalization table, the curiosity prompts, the multi-audience test - all of these work when the authoring team genuinely wants clear requirements and is willing to sit with uncomfortable questions. In teams where the actual goal is to get a story into the sprint with minimum friction, to appear busy, or to produce documentation that satisfies an external auditor without reflecting reality, the skill's questions will be answered strategically and its tests will be gamed.

The skill has no defence against this. No documentation does. The best the skill can do is fail *visibly* when gamed - incomplete multi-audience sign-offs, empty Participants & Decisions tables, scenarios that are obviously formulaic - so that a later reader (auditor, new team member, future self) can see what was skipped.

If the user is operating in a context where the skill is being gamed, the skill cannot fix that. The honest thing is to name it.

## 6. The "AI agent as consumer" audience is under-specified

The skill treats an AI agent as a first-class consumer of requirements, alongside PO/dev/tester. This is directionally right, but the practical test - "could an AI agent build this without inventing defaults?" - is harder to run than the other three. Humans can object aloud; AI agents will happily produce confident-sounding wrong code.

The skill's workflows do not include a concrete mechanism for running the AI audience test. You can imagine one (feed the requirement to an AI, ask it to flag unknowns, compare its flags to the human reviewer's flags), but the skill does not operationalise this. Treat the AI-agent-consumer test as aspirational rather than as a reliable check.

## 7. The skill over-indexes on the English-speaking, technology-industry idiom

Terms like "PO," "story," "backlog," "refinement," "three amigos," "Gherkin" have specific meanings inside a particular cultural milieu. In organisations that use different terminology - *fachliche Anforderung*, *specifica funzionale*, *cahier des charges*, or industry-specific idioms like *use case* (often in a non-UML sense), *functional spec*, *design control input* - the skill's vocabulary creates translation overhead and occasional mismatch.

The [glossary.md](glossary.md) helps, but does not translate into other practice languages. If your user operates in a different tradition, do not assume the mapping is one-to-one; ask what the equivalent artefacts and ceremonies are in their context, and adapt the workflow vocabulary accordingly.

## 8. We have not tested this skill against its own advice

The iron law for skill authoring is "no skill without a failing test first." This skill was authored by practitioners who believed in the practices and synthesised them into reference material, not by running pressure scenarios against a baseline agent and watching it fail. That means some advice here is theoretically sound but not empirically validated against the way real agents (or real practitioners) fail.

Treat the skill as a well-reasoned first draft rather than a tested artefact. If you find it giving bad advice in a specific scenario, that is a signal worth more than the skill's own confidence.

## How to use this file

If a user's situation clearly falls into one of these limitations, say so. The skill does not lose credibility by admitting where it does not work - it gains credibility. Naming the limitation also lets the user make an informed decision about whether to adapt the skill, supplement it, or reach for a different tool.

If you find a new limitation in use, that is exactly the kind of thing worth adding here. A limitations file that never grows is a limitations file that is not being read.

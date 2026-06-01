# Adapting These Practices to Your Context

This skill describes practices at their ideal form. Most real organisations are not at that ideal - most are somewhere on the road. This file exists so that a single practitioner (often a product owner, sometimes an analyst, developer, or tester) can take **most of the value** from this skill without needing their team, their org, or their tooling to change first.

The goal of this skill is to help you *learn and improve your own practice*. It is not to push you to reorganise your company. If you can do the ideal, do it. If you cannot, the principles still apply - the ceremonies do not.

## The principles travel. The ceremonies do not.

The five principles in [essential-principles.md](essential-principles.md) are invariant:
1. Requirements are shared understanding made visible.
2. If you can't verify it, you don't understand it.
3. Describe behaviour, not implementation.
4. Smaller is always better.
5. Requirements age like milk.

The *ceremonies* in this skill - Gherkin notation, "three amigos" meetings, Definition of Ready, 2-day delivery, example mapping - are specific ways of enacting those principles. They are helpful when available and not the point. The point is the principle.

## Notation is flexible; concreteness is not

The template uses Gherkin because it is unambiguous, readable by all four audiences, and easy to turn into tests. But if your team writes acceptance criteria as Jira bullet lists, paste the scenarios as bullet points. If your team uses free-text paragraphs, write free-text paragraphs that are **specific and verifiable**. The form does not matter; the substance does.

**Good bullet list:**
- When a registered user submits the correct credentials, they are taken to their account home page.
- When a registered user submits incorrect credentials 5 times within 15 minutes, their account is temporarily locked and they are told when they can try again.
- When the authentication service is unavailable, the user sees a transient error and can retry without re-entering their credentials.

This is BDD in bullets. No one needs the word "Given" for it to work.

**Not good:**
- User can log in.
- Error shown for wrong credentials.

That is vagueness in any notation.

## The 2-day rule as a design exercise

Your team may deliver in two-week sprints, or monthly releases, or whatever the cadence is. You may not be able to change that.

Apply the 2-day test anyway, as a *design exercise*:

> "If I had to put some valuable increment of this feature into production in under two days, what would it be?"

You will find:
- Which slice is the absolute smallest valuable thing.
- Which parts of your "story" are actually separate stories.
- Which assumptions are doing the heavy lifting and should be verified first.
- Where you could cut scope if something goes sideways mid-sprint.

Even if you never ship it slice-by-slice, knowing the slice changes how you write the requirement, what you flag as risk, and what you can drop under pressure. The rule is not about delivery cadence - it is about **clarity**.

## Multi-audience sign-off as a thought experiment

The sign-off section in the template asks whether a PO, dev, tester, and AI agent can all read the requirement and determine done-ness from it alone.

In an ideal org, you run that check as an actual conversation with actual people. In many orgs you cannot - the tester is on a different team, the dev is offshore, there is no formal PO conversation.

**Run the check as a thought experiment.** Mentally play each audience. For each one, ask:

- "If I were this person, reading this cold, could I do my job from this?"

If yes, mark the requirement as **self-reviewed**. A self-reviewed requirement is a draft with your best-effort check - it is **not** a trusted requirement. Before build starts, get at least one real audience other than yourself to read it and push back. If even that is not possible, flag the requirement's "trust level" in your Risks & Unknowns section. Partial assurance is better than false confidence.

## Getting refinement conversations when your org has no refinement

"Three amigos" is a refinement pattern. If your org does not hold them:
- **Find one amigo.** Grab 15 minutes with any dev or QA engineer. Show them your scenarios. Ask "what would you need that this doesn't tell you?" Capture their answers and update.
- **Use async if you must.** Post the scenarios in a channel that the right people read. Invite reactions. Silence is not agreement - ping directly if needed.
- **When no conversation is possible at all**, apply the thought-experiment version of the multi-audience check and flag the requirement's trust level explicitly.

## Spikes without a "spike" concept

If your team does not formally recognise spikes:
- Raise the open question in your next refinement/planning meeting as a risk.
- Time-box it yourself: "we need to know X by date Y, or we're building blind."
- If you cannot get a dedicated investigation, propose doing the riskiest thing first and letting it tell you what it needs.

The vocabulary is optional. The behaviour - *resolving uncertainty before committing to a big bet* - is not.

## DoR and DoD without team agreement

If your team has no Definition of Ready or Definition of Done, use this skill's quality gate as *your personal bar*. Do not send a requirement forward unless it passes your own check. Do not accept a story as "done" unless it meets the acceptance criteria you wrote for it.

You cannot impose standards on others, but you can decline to participate in your own confusion.

## Handing off to real-world next steps (not Claude skills)

The workflow closers point to downstream activities (system design, testing strategy, code review) that may or may not have dedicated skills in the user's environment. In your real workplace, the analogous hand-offs are:

- **Open assumptions:** raise them at the next planning or refinement meeting; log them on the team's impediments board; email the person who can answer.
- **Open spikes:** propose a time-boxed investigation; if denied, mark the dependent story as higher risk.
- **NFRs you don't own:** tag the tech lead, architect, or security partner. Do not silently drop the NFR - if it goes away, note *who* made it go away and when.
- **Stories too large for your org's cadence:** either slice and negotiate the slice, or explicitly mark the parts you are accepting to carry.
- **Requirements that failed your own quality gate:** do not hand them forward. Either revise, or deliver them with the failed checks visible so the receiver can decide.

## The lone practitioner's honest stance

You are operating in a system that does not yet reward these practices. You can still:

- Write scenarios for **your own** clarity, even if no one else reads them.
- Apply the 2-day test as a thought exercise, even if no one else cares about sizing.
- Run the multi-audience check in your head before submitting, even if you cannot get real sign-off.
- Use the rationalization table to catch yourself, even if no one else is watching.
- Name the gaps explicitly in your output (assumptions, missing NFRs, untrusted sign-off), so a future reader (including future-you) knows what was done and what was not.

This is not a compromise. It is how the practice enters an organisation - one person at a time, doing the work visibly, letting the results make the case.

## When to stop: the exit clause

The lone-practitioner mode described here is unsustainable indefinitely. It asks the user to invest more care in their work than the surrounding system rewards. That is fine for a while - sometimes even rewarding - but it has a shelf life, and pretending otherwise is how people burn out.

**Signs the mode is failing rather than the environment failing:**

- **You are the only one benefiting from your work.** Your scenarios, sign-offs, and flags are read by no one; the code shipped is the code the team would have shipped with half the specification. You are producing documentation for an audience of one.
- **Your flags go unread for three cycles.** You raise an assumption, a risk, or a missing audience three sprints in a row. Nothing changes. The team did not reject your input - they did not process it. The signal is not landing.
- **Downstream work ignores your requirements.** Developers build what they would have built without the spec. Testers write tests against the code, not against your scenarios. Your document is a historical artefact within days of being written.
- **You are the only one using the vocabulary.** "Three amigos," "vertical slice," "DoR" - nobody else on the team uses these words, and every meeting feels like translation work done exclusively by you.
- **Your care is treated as friction, not contribution.** Raising questions about Givens, edge cases, or audiences is met with visible impatience. You learn to phrase them as apologies.

**What to do when you see these signs:**

1. **Name it.** To yourself, in writing. "The environment is not absorbing my input. I am doing extra work for no effect." This is diagnosis, not self-blame.
2. **Try one visible experiment.** Pick the single practice that has the clearest observable payoff - usually the Participants & Decisions table or the multi-audience sign-off - and run it loudly for one feature. Make the outcome legible. If it changes nothing downstream, the environment is genuinely not ready.
3. **Lower your standard to match what the organisation rewards - or leave.** Doing neither is how people burn out. "Lower your standard" is not defeat; it is a choice to stop investing energy in returns the environment will not pay. You can always raise it again when the environment changes or when you move.
4. **Know the difference between the practice failing and the person failing.** The practices in this skill are sound in most contexts. If they fail in yours, the first hypothesis is that the environment is not ready - not that you are doing them wrong. Talk to someone who has practiced this elsewhere before concluding the problem is you.

**Success, for the lone practitioner, is not "I changed the organisation."** It is one of:

- You raised the quality bar on your own output, learned the practice, and are now better at this than you were. This is the minimum success case and it is always available.
- You found one or two colleagues who work with you on this, creating a small pocket of the practice within the org. This is often achievable and already changes your day-to-day.
- You influenced a team or product area enough that the practice becomes local default. This is rare and takes years.
- You moved to an organisation that already practices this. This is legitimate and often the right answer - the skill does not require you to be a martyr.

If none of these look reachable, the skill is not failing you and you are not failing the skill. The environment is simply not the right one for this work. Notice that, and choose accordingly.

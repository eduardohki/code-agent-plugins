<objective>
Catalog the most common ways requirements go wrong, explain WHY each pitfall causes real damage, and provide concrete corrections. Every pitfall follows the same structure: what people do, why they do it, why it hurts, and what to do instead.
</objective>

<table-of-contents>
1. Writing requirements as solutions
2. Acceptance criteria that describe steps, not outcomes
3. Stories too large to deliver in 2 days
4. Slicing by technical layer
5. Slicing by activity
6. Missing edge cases and unhappy paths
7. Refining requirements too far in advance
8. Open-ended epics without defined outcomes
9. Starting work without a Definition of Ready
10. Treating BDD as a testing tool
11. Missing non-functional requirements
12. Hidden assumptions presented as facts
13. Requirements without a value statement
14. Writing requirements only your role understands
15. Assuming AI agents can infer what you meant
16. Accepting AI-authored requirements without adversarial review
</table-of-contents>

<pitfalls>

**Pitfall 1: Writing requirements as solutions.**

- **What people do:** "Add a dropdown menu for country selection." "Create a Redis cache layer." "Use a modal dialog for confirmation."
- **Why they do it:** It feels productive. Specifying the solution makes the requirement feel concrete and actionable. Often the person writing it - especially a product owner who knows the product deeply - has a strong mental model where the behavior and the implementation have fused together. They're not being prescriptive out of ignorance; they've internalized the product so thoroughly that the UI *is* the behavior in their mind.
- **Why it hurts:** It eliminates the implementer's ability to find a better solution. The dropdown might not be the right pattern for 240 countries. Redis might not be the right caching strategy. A modal might not be the right confirmation UX. Worse, it creates false precision - the requirement *looks* specific but says nothing about what behavior the user actually needs. When the prescribed solution doesn't work, there's no fallback because the underlying need was never stated.
- **What to do instead:** Honor the domain knowledge, but reframe it as behavior. The person prescribing "add a dropdown" clearly understands what the user needs - help them express that understanding as the underlying behavior: "The user can select their country from the full list of supported countries. The selection should be efficient even with 240+ options." The insight ("users need to pick a country quickly") is preserved. The solution constraint is removed. Now anyone - human or AI - can choose the best implementation for the context.

---

**Pitfall 2: Acceptance criteria that describe steps, not outcomes.**

- **What people do:** "1. Navigate to the login page. 2. Enter username. 3. Enter password. 4. Click Login. 5. Verify the dashboard loads."
- **Why they do it:** It feels thorough. Step-by-step instructions are easy to write because they mirror how a person manually tests the feature.
- **Why it hurts:** These are test scripts, not acceptance criteria. They break whenever the UI changes. They test a specific implementation path rather than the required behavior. They can't be reused across different interfaces (web, mobile, API). They tell the tester exactly what to click but nothing about what actually matters - and they give an AI agent a rigid script instead of an understanding of intent.
- **What to do instead:** Write declarative criteria: "Given valid credentials, when the user authenticates, then they are taken to their dashboard." This describes the outcome regardless of how authentication is implemented.

---

**Pitfall 3: Stories too large to deliver in 2 days.**

- **What people do:** Write stories that represent a week or more of work. "Implement user registration with email verification, profile setup, and password recovery."
- **Why they do it:** It feels like the natural unit of work. Breaking it smaller feels artificial or wasteful - "why would I make three stories when it's clearly one feature?"
- **Why it hurts:** Large stories hide complexity. You won't discover the hard parts until you're deep into implementation. They make estimation unreliable - the confidence interval on a 2-day story is much tighter than on a 10-day story. They delay feedback: nothing is deliverable until everything is done. They increase risk: if priorities change mid-work, you've invested heavily with nothing to show. And they make cycle time unpredictable, destroying team trust with stakeholders.
- **What to do instead:** Decompose ruthlessly. "User can register with email and password" is one story. "User receives verification email" is another. "User can reset password" is a third. Each can be delivered and validated independently. Each reveals whether the next one is actually needed.

---

**Pitfall 4: Slicing by technical layer.**

- **What people do:** Create a "backend story," a "frontend story," and sometimes a "database story" for the same feature. Three stories in the backlog, three people assigned, feels like parallel progress.
- **Why they do it:** It maps to how the team is organized. There are backend developers, frontend developers, and DBAs or platform engineers. Giving each specialist "their own story" feels efficient - everyone works on what they know. It also feels like clean separation of concerns.
- **Why it hurts:** No individual story delivers any observable behavior. The backend story is useless without the frontend. The frontend is useless without the backend. All must be deployed together before anyone can verify anything works. This means:
  - **Illusory progress.** Three stories "in progress" with zero value delivered. The board looks busy but nothing is done.
  - **Integration risk deferred.** The backend developer builds what they think the frontend needs. The frontend developer builds against what they think the API returns. They integrate at the end and discover mismatches.
  - **Coordination tax.** Every standup becomes: "I'm blocked waiting on the backend story." Every sprint review becomes: "We can't demo this yet because the frontend story isn't done."
  - **The math is brutal.** The odds of delivering on time for any activity are 1 in 2^n where n is the number of hard dependencies. Three coupled stories means a 1-in-8 chance of smooth delivery.
- **What to do instead:** Slice vertically. Each story delivers an end-to-end behavior that touches whatever layers it needs. A backend developer and a frontend developer can both work on the SAME story as parallel tasks - one building the API, the other building the UI - but the story is a single vertical slice with one set of acceptance criteria that describes the observable behavior. When it's done, a user can see it working. The story isn't "build the API" or "build the form" - it's "user can submit their order."

---

**Pitfall 5: Slicing by activity.**

- **What people do:** Create separate stories for coding, testing, code review, and deployment. Or more commonly: the developer "finishes" a story, then a separate "QA story" or "testing story" is created and handed off to the tester.
- **Why they do it:** It mirrors the sequential flow of work: build it, then test it, then review it, then ship it. It makes handoffs visible on the board. It lets testers manage their own backlog.
- **Why it hurts:** This creates a mini-waterfall within each feature. The developer codes in isolation, considers themselves "done," and moves on. The tester picks it up days later, finds problems, and sends it back. The developer has context-switched to something else and must re-load context. Each round trip costs more than the last. Specifically:
  - **Delayed feedback.** Testing only begins after coding "finishes," which means defects are discovered at the most expensive possible moment - after the developer has moved on.
  - **False progress.** The "coding story" is marked done but the feature doesn't work. The board shows completed work that isn't complete.
  - **Inflated WIP.** Two stories (coding + testing) occupy board space for what is actually one piece of work, making it harder to see true throughput.
  - **Broken ownership.** The developer doesn't feel responsible for quality because "that's the testing story." The tester doesn't feel empowered to influence design because "that was the coding story."
- **What to do instead:** Each story includes all activities needed to deliver it to production. Coding, testing, review, and deployment are part of the Definition of Done, not separate work items. The developer and tester collaborate on the same story. Testing happens alongside development, not after it. The story isn't done until it's tested, reviewed, and deployable.

---

**Pitfall 6: Missing edge cases and unhappy paths.**

- **What people do:** Write acceptance criteria only for the happy path. "Given a valid order, when submitted, then confirmation is shown."
- **Why they do it:** The happy path is the feature. Edge cases feel like details to figure out during implementation.
- **Why it hurts:** Every unspecified edge case becomes an assumption made by whoever implements it. Different developers will make different assumptions. An AI agent will make plausible-sounding assumptions that nobody validated. The result: inconsistent behavior, bugs discovered late, and rework. The unhappy paths are often where the real complexity lives - and where users actually spend significant time (typos, network failures, invalid data, browser back button, timeout scenarios).
- **What to do instead:** For each happy path scenario, explicitly ask: What happens when the input is invalid? When a dependency is unavailable? When the operation times out? When the user does something unexpected? Each answer is a new acceptance scenario.

---

**Pitfall 7: Refining requirements too far in advance.**

- **What people do:** Spend weeks detailing stories for work that won't start for months. Build a comprehensive, fully-specified backlog.
- **Why they do it:** It feels responsible. Having everything planned reduces anxiety. Stakeholders feel reassured seeing a detailed plan.
- **Why it hurts:** Requirements gathered months ago are based on assumptions that may no longer hold. The market shifts. User feedback changes priorities. Technical discoveries invalidate earlier plans. The team ends up either building outdated requirements or re-doing the refinement work - wasting the original effort. Worse, detailed advance plans create inertia: teams resist changing direction because "we already planned all this."
- **What to do instead:** Keep a rough backlog of epics and goals. Refine stories to full acceptance criteria only when the team is about to start working on them - typically within the current or next iteration. Just-in-time refinement produces fresher, more relevant requirements.

---

**Pitfall 8: Open-ended epics without defined outcomes.**

- **What people do:** Create epics like "Improve search" or "Tech debt cleanup" or "Performance optimization" with no defined end state.
- **Why they do it:** These are real concerns that deserve attention. Framing them as ongoing efforts ensures they don't get forgotten.
- **Why it hurts:** Without a defined outcome, the epic can never be closed. Work accumulates indefinitely. The team can't tell whether they're making progress or just staying busy. Stakeholders can't evaluate whether the investment produced results. And because the epic is always "in progress," it becomes a catch-all bucket that absorbs loosely related work.
- **What to do instead:** Define a measurable outcome: "Search returns results in under 200ms for 95th percentile queries" or "Reduce build time from 12 minutes to under 4 minutes." Once achieved, the epic is done. If more work is needed, it's a new epic with a new outcome.

---

**Pitfall 9: Starting work without a Definition of Ready.**

- **What people do:** Pull stories into development as soon as they appear in the backlog, without checking whether the story is actually understood.
- **Why they do it:** It feels faster. Stopping to check readiness feels like bureaucracy. "We'll figure it out as we go."
- **Why it hurts:** Developers start work, hit questions, and block. They make assumptions to keep moving - assumptions that may be wrong. Testers discover the acceptance criteria are vague only after code is written. Rework spikes. Context switching increases as developers juggle blocked and active work. Cycle time balloons and becomes unpredictable.
- **What to do instead:** Establish and enforce a Definition of Ready. At minimum: value statement (why), testable acceptance criteria (when are we done), and team review (has the team seen it). If a story doesn't meet the DoR, it goes back to the backlog for refinement. No exceptions.

---

**Pitfall 10: Treating BDD as a testing tool.**

- **What people do:** Hand finished stories to testers and say "write the BDD tests." Or adopt a BDD framework (Cucumber, SpecFlow) and call it "doing BDD."
- **Why they do it:** BDD tools exist, and they look like test automation frameworks. It's natural to associate Given/When/Then with automated testing.
- **Why it hurts:** The entire point of BDD is the conversation that happens BEFORE development starts - the collaborative process of defining behavior through examples. Using BDD only as a test format skips the most valuable part: the shared understanding that emerges from discussing scenarios together. The tests may pass, but they're testing the developer's interpretation of requirements rather than a shared agreement about behavior.
- **What to do instead:** Use BDD as a conversation framework during refinement. Product owner, developer, and tester discuss scenarios together BEFORE work begins. The Given/When/Then format is the output of that conversation, not a task for the test team.

---

**Pitfall 11: Missing non-functional requirements.**

- **What people do:** Specify what the feature does but not how well it needs to do it. No mention of response time, concurrent users, accessibility standards, or security boundaries.
- **Why they do it:** Non-functional requirements feel like "architecture concerns" that the team will handle. They're hard to specify without technical knowledge.
- **Why it hurts:** Every unstated non-functional requirement gets whatever the implementer's default is. A developer might build something that works for 10 users but collapses at 10,000. An API might return results in 5 seconds because nobody said it mattered. Accessibility may be ignored entirely. Security boundaries may be assumed rather than designed. Retrofitting non-functional requirements is always more expensive than building them in.
- **What to do instead:** For each feature, explicitly state expectations for performance, security, accessibility, observability, and data handling. These don't need to be precise engineering specs - "search results in under 500ms" is sufficient. The point is to make the expectation visible.

---

**Pitfall 12: Hidden assumptions presented as facts.**

- **What people do:** Write "users will authenticate via SSO" without verifying that the SSO provider supports the required flow. Or "the API returns data in under 100ms" without measuring the actual API.
- **Why they do it:** Assumptions streamline the writing process. Stopping to verify each one feels slow and disruptive.
- **Why it hurts:** Every hidden assumption is a risk. When the assumption turns out to be wrong - and some will - the impact is proportional to how much work was built on top of it. The later you discover a wrong assumption, the more expensive the correction. Hidden assumptions are invisible risks.
- **What to do instead:** Mark assumptions explicitly: "ASSUMPTION: SSO provider supports PKCE flow - needs verification." This turns hidden risks into visible ones that can be validated through spikes or early investigation.

---

**Pitfall 13: Requirements without a value statement.**

- **What people do:** Write "As a user, I want to reset my password" without explaining why or what problem it solves.
- **Why they do it:** The value seems self-evident. Of course users need to reset passwords.
- **Why it hurts:** Without the "so that" clause, nobody can evaluate whether this is the right solution to the underlying problem. Maybe the real problem is that users forget passwords because the password policy is too complex. Maybe passkeys would eliminate the need entirely. The value statement enables negotiation - without it, every requirement is equally non-negotiable because there's no basis for comparing the value of different approaches.
- **What to do instead:** Always include the value: "As a user, I want to reset my password so that I can regain access to my account when I've forgotten my credentials." Now the team can discuss whether password reset is the best solution to the access recovery problem.

---

**Pitfall 14: Writing requirements only your role understands.**

- **What people do:** Product owners write in business jargon with no testable behavior. Developers write technical specs with no user context. Testers write test plans disconnected from acceptance criteria.
- **Why they do it:** Each role defaults to their own vocabulary and frame of reference. It's faster to write in your own language.
- **Why it hurts:** Creates handoff gaps. The developer misinterprets the product owner's intent. The tester covers the wrong scenarios. The AI agent takes ambiguous text literally in the wrong direction. Every translation between role-specific languages loses information and introduces error.
- **What to do instead:** Use concrete scenarios (Given/When/Then) as the shared language. They force specific examples that everyone can read regardless of role. A product owner can verify the scenario matches their intent. A developer can see exactly what to build. A tester knows exactly what to verify. An AI agent has unambiguous criteria to work from.

---

**Pitfall 15: Assuming AI agents can infer what you meant.**

- **What people do:** Write vague, high-level requirements expecting the AI to "figure it out." Or provide a brief description and assume the AI will ask clarifying questions.
- **Why they do it:** AI agents are impressive at inference, so people provide less precision. It feels efficient - "the AI is smart enough."
- **Why it hurts:** AI agents are confidently wrong when requirements are ambiguous. They fill gaps with plausible-sounding assumptions that may not match your intent. Unlike a human teammate who might push back or ask for clarification, many AI workflows proceed silently with their interpretation. The result is code that looks correct, passes superficial review, and implements behavior nobody actually wanted.
- **What to do instead:** Treat AI agents as your strictest audience. If a requirement has ambiguity, the AI will exploit it - not maliciously, but inevitably. Well-written acceptance criteria with explicit edge cases and non-functional constraints are the best prompt you can give an AI.

  **Example - the same requirement, vague vs. precise:**

  Vague: "Users can search for orders. Results should be relevant and fast."

  What the AI builds: full-text search across all order fields, returns top 50 results sorted by a relevance score it invented, no pagination, no performance target, no handling for zero results. It works. It's plausible. Nobody asked for any of it.

  Precise:
  ```gherkin
  Scenario: User searches by order number
    Given I have two orders: "ORD-001234" and "ORD-005678"
    When I search for "ORD-001234"
    Then I see order ORD-001234 in the results
    And I do not see order ORD-005678
    And results appear within 500ms

  Scenario: User searches with no matching orders
    Given I have no orders matching the query "ORD-999999"
    When I search for "ORD-999999"
    Then I see a message indicating no matching orders were found
    And I am offered the option to contact support
  ```

  The AI can now build exactly what was specified - and everyone can verify whether it did.

---

**Pitfall 16: Accepting AI-authored requirements without adversarial review.**

- **What people do:** An AI agent generates user stories from meeting notes or a product brief. The stories are well-formatted, have Given/When/Then scenarios, and look thorough. The team accepts them as-is.
- **Why they do it:** The output looks professional. It's structured, complete-looking, and uses the right vocabulary. Reviewing AI output feels redundant when it already looks good.
- **Why it hurts:** AI-generated requirements have predictable failure modes. They tend to be plausible-sounding but vague on genuine edge cases. Happy and unhappy paths often feel like mirror images rather than scenarios derived from real-world understanding. The AI fills in blanks with reasonable-sounding assumptions that nobody actually validated. Format quality masks content gaps. The most dangerous AI-generated requirement is the one that reads so well that nobody questions it.
- **What to do instead:** Apply the same review workflow to AI-authored requirements as to human-authored ones - and be MORE skeptical of completeness. Specifically check: Are the edge cases genuine or formulaic? Do the unhappy paths reflect real failure modes or just inverted happy paths? Are the non-functional requirements specific or generic? Are there assumptions the AI made that need human verification? Run every AI-generated requirement through the multi-audience test.

  **Example - AI-generated requirement with annotated failure modes:**

  Prompt to AI: "Write a user story for a document upload feature."

  AI output:
  ```
  As a user, I want to upload documents so that I can attach them to my account.

  Scenario: Successful upload
    Given I am logged in
    When I upload a valid document
    Then the document is stored and I see a success message

  Scenario: Failed upload
    Given I am logged in
    When I upload an invalid document
    Then I see an error message

  NFRs:
  - Performance: Uploads should be handled efficiently
  - Security: Documents should be stored securely
  - The system supports files up to 25MB
  ```

  What to catch:
  - **Formulaic edge cases:** "Failed upload" is just the happy path inverted. What's an "invalid document"? Too large? Wrong type? Corrupted? Password-protected PDF? Each is a different scenario with a different expected behavior. A real edge case would be: network drops mid-upload, the account is locked, or concurrent uploads of the same file.
  - **Plausible-sounding vagueness:** "Efficiently" and "securely" sound like NFRs but commit to nothing. What response time? Encrypted at rest? Virus-scanned? Subject to a retention schedule?
  - **Assumption backfill:** Where did "25MB" come from? Nobody specified a limit. The AI picked a plausible number and presented it as a requirement.
  - **Missing "I don't know":** No mention of allowed document types (PDF, JPEG, TIFF?), who can view uploaded documents (the uploader only? administrators too?), or whether documents must be linked to a specific record. A human would flag these as open questions. The AI presented everything as settled.

</pitfalls>

<red-flags>

**Phrases that signal weak requirements.** When you see these in requirements, meeting notes, or feature descriptions, they indicate a gap that needs to be addressed before the requirement is ready.

**Vague motivation:**
- "We need to..." (why? what happens if you don't?)
- "It would be nice if..." (is this a real need or a wish?)
- "We should probably..." (who's asking for this and why?)
- "Let's just add..." (what problem does this solve?)

**Missing or generic personas:**
- "A user..." (which user? what role? what context?)
- "The system should..." (who observes this behavior? who benefits?)
- "Users want..." (which users? how do you know?)
- "Stakeholders need..." (which stakeholders? what's their specific need?)

**Implementation leaking into requirements:**
- "Add a button/dropdown/modal/tab that..." (describe the behavior, not the widget)
- "Create an endpoint/API/service that..." (describe what the consumer needs, not the implementation)
- "Use [technology/framework/library] to..." (that's a design decision, not a requirement)
- "Not wired up yet" / "hook it up to..." (implementation status, not behavior)
- "Refactor the..." (what observable behavior changes? if none, this isn't a requirement)
- Class names, file paths, database table names in the requirement text

**Vague acceptance:**
- "Should work correctly" (what does "correctly" mean specifically?)
- "Intuitive UI" / "user-friendly" (what can the user do without help?)
- "Handle errors gracefully" (which errors? what does the user see for each?)
- "Should be fast" / "performant" / "responsive" (what response time? what load?)
- "Secure" (authenticated? authorized? encrypted? audited? all of these?)

**Unbounded scope:**
- "And more..." / "etc." / "and so on" (what specifically?)
- "Support all types of..." (enumerate them - "all" hides unknown complexity)
- "Flexible enough to..." (flexible how? what variations must be supported?)
- "Future-proof" (against what future? name the specific change scenarios)

**Hidden assumptions:**
- "Obviously..." / "of course..." / "naturally..." (if it's obvious, state it explicitly)
- "As usual..." / "like we always do..." (spell it out - not everyone shares the context)
- "Simple" / "just" / "quickly" (these words hide complexity)
- "Should be straightforward" (for whom? based on what knowledge?)

</red-flags>

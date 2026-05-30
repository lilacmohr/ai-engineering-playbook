# Workflow: Human Acceptance Session

The human acceptance session begins after `[ACCEPTANCE-PREP]` closes. By this
point the pre-flight pipeline has run, blocking issues are ticketed, and the
synthesis brief is waiting. Your job in this session is not discovery — it is
decision-making.

---

## Before You Open the App

Read the synthesis brief from `[ACCEPTANCE-PREP]`. This is your agenda.

Confirm before opening the app:
- [ ] All `[BLOCKING]` adversarial findings have linked `[BUG]` tickets
- [ ] All `[CRITICAL]` real-data failures have linked `[BUG]` tickets
- [ ] You have read the synthesis brief and know what questions to answer

If the synthesis brief is empty — the pre-flight pipeline found nothing
requiring human judgment — you are doing a confirmatory walkthrough, not a
decision-making session. That is a short session.

---

## Running the Session

### 1. Walk the flows

Open the application. Walk through the flows in `ACCEPTANCE-DEMO.md`, in order.
You are verifying that what was built matches your intent — not re-testing
what the pre-flight pipeline already covered.

As you go, narrate issues to the agent:

> *"I'm going to describe issues. For each one: (a) do you understand it,
> (b) is it a quick fix or needs more thought, (c) could fixing it break
> anything else?"*

### 2. Answer the decisions brief

For each item in the synthesis brief, make a decision and state it clearly.
These are the judgment calls only you can make. The agent cannot resolve them
and the pre-flight pipeline cannot resolve them. State your decision in plain
language; the agent will handle implementation.

### 3. Triage as you go

Not everything you find should be fixed immediately. Apply this discipline:

| Fix immediately | Create a ticket |
|---|---|
| Copy or label changes | Architecture changes |
| Layout adjustments | New feature requests |
| Obvious single-line bugs | Behavior requiring stakeholder input |
| Missing input validation | Performance issues requiring investigation |

Fixing complex issues in the session creates regression risk and turns a scoped
validation into an open-ended debugging session. The right output of an
acceptance session is: a set of immediate fixes, a set of new `[BUG]` tickets,
and a closed `[ACCEPTANCE]` issue.

### 4. Devil's advocate (optional but high-value)

Before closing, ask the agent to challenge its own work:

> *"Before I close this, challenge your own work. What edge cases did you not
> handle? What assumptions did you make that might be wrong? What would break
> this in production that wouldn't break it in testing?"*

Agents are often candid here. The builder knows where the implementation is
shaky. Making this explicit at the end of the session surfaces risks that
would otherwise stay buried until a customer finds them.

---

## Closing the Session

The `[ACCEPTANCE]` issue closes when:

- [ ] All flows in `ACCEPTANCE-DEMO.md` have been walked
- [ ] All synthesis brief decisions have been made and documented
- [ ] Fix-now items have been fixed
- [ ] Follow-on items have `[BUG]` tickets with links back to this issue

After the issue closes, write or update Playwright tests for any newly validated flows (`playwright:required` — see the [README](../README.md)).

Document the decisions made in the `[ACCEPTANCE]` issue thread. When someone
asks later why a feature behaved a certain way and then changed, the decision
record should be visible in the issue history.

---

## After Acceptance: Playwright

After `[ACCEPTANCE]` closes, write or update Playwright tests for any flows
that were validated in this session. The human validated the intent; Playwright
guards it forever after.

Do not write a *new* Playwright test for a flow that hasn't passed `[ACCEPTANCE]`.
You would be encoding behavior that hasn't been validated as correct. Existing
Playwright tests for previously validated flows are unaffected — they run at
Layer 0 to guard against regression, independent of this cycle.

---

## After Acceptance: Retro

The retro step asks: *what did acceptance testing find that an earlier
phase should have caught?*

- Bugs found in pre-flight → why didn't the test suite catch them?
- Intent misalignment found in `[ACCEPTANCE]` → why didn't spec review catch it?
- Edge cases the adversarial reviewer found → should these be in `CLAUDE.md`
  testing requirements for future tickets?
- Judgment calls made during `[ACCEPTANCE]` → are any of these generalizable
  rules that should go in `CLAUDE.md` so agents apply them next time?

Use the `retro` skill from `03-delivery-cycle/skills/retro/SKILL.md` and include
the acceptance testing findings as an additional category.

---

## Key Prompts

**Starting a session (post-pipeline):**
> *"I've read the synthesis brief and ACCEPTANCE-DEMO.md. Let's walk the flows together.
> I'll narrate what I observe; you help me triage. Start with the first flow in
> ACCEPTANCE-DEMO.md and tell me what I should see at each step."*

**Giving feedback during the session:**
> *"I'm going to describe issues. For each one: (a) do you understand it,
> (b) is it a quick fix or needs more thought, (c) could fixing it break anything else?"*

**After a round of fixes:**
> *"You just made N changes. Walk me through what you changed and what regression
> risks exist. What should I re-test?"*

**Acceptance criteria verification:**
> *"Here are my original requirements. Go through each one and tell me whether
> the app fully satisfies it, partially satisfies it, or misses it. For any
> partial or miss, describe the gap."*

**Devil's advocate:**
> *"Before I close this, challenge your own work. What edge cases did you not
> handle? What assumptions did you make that might be wrong? What would break
> this in production that wouldn't break it in testing?"*

**Closing the session:**
> *"We've completed acceptance testing. Summarize: what's been fixed, what's
> ticketed for a future [IMPL] cycle, and what open questions remain for the
> product owner. I'll post this summary to the [ACCEPTANCE] issue thread."*

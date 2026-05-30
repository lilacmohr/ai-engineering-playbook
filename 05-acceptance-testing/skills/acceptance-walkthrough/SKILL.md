---
name: acceptance-walkthrough
description: >
  Walk through a single feature or flow using the demo agent persona. Use when
  asked to "demo this feature", "walk me through [flow]", "show me what was
  built", "run acceptance walkthrough for [feature]", or when validating one
  specific flow without running the full pre-flight pipeline. Produces a
  focused ACCEPTANCE-DEMO.md for a single flow. Does not run the adversarial
  reviewer or real-data stress agent — use acceptance-prep for the full pipeline.
argument-hint: 'Feature or flow name (e.g. "user signup", "report export", "password reset")'
---

# Acceptance Walkthrough

Walk through a single feature or flow as the demo agent. Produces a structured
self-demo that can be used for a quick human acceptance session or as input to
the full pre-flight pipeline.

Use this skill for:
- Validating a single flow without running the full pipeline
- A quick human check before a demo or stakeholder review
- Scoped acceptance testing when the full `[ACCEPTANCE-PREP]` pipeline is not warranted

For the full pre-flight pipeline (all four layers), use `acceptance-prep` instead.

---

## Inputs

1. **Feature or flow name** — from the argument, or ask if not provided
2. **PR diff or code** — read the relevant code for the feature
3. **CLAUDE.md** — understand the project's conventions and constraints
4. **SPEC.md or acceptance criteria** — if a `[CRITERIA]` ticket exists, read it

If no argument is provided, ask: *Which feature or flow should I walk through?*

---

## Instructions

Apply the [`demo-agent`](../../personas/demo-agent.md) persona to the specified
feature or flow.

Work through the demo agent's output format:

1. **Feature Summary** — what does this do from the user's perspective?
2. **User-Facing Flows** — every path through the feature
   - Entry point, happy path, expected output
   - Edge cases handled and not handled
3. **Integration Points** — external systems touched
4. **Assumptions** — anything not in the spec that was decided during implementation
5. **Lowest-Confidence Areas** — three specific places most likely to fail under
   conditions not tested

---

## Output Format

```
## Acceptance Walkthrough — [Feature/Flow Name]

### Feature Summary
[What this does, from the user's perspective]

### Flows

#### [Flow Name]
- Entry point: [how a user gets here]
- Happy path: [step by step]
- Expected output: [what the user sees]
- Edge cases handled: [list]
- Edge cases NOT handled: [list — be specific]

[Repeat for each flow]

### Integration Points
[External systems, expected inputs/outputs, failure handling]

### Assumptions
[Anything decided during implementation that isn't in the spec]

### Lowest-Confidence Areas
1. [Specific area, specific reason]
2. [Specific area, specific reason]
3. [Specific area, specific reason]
```

---

## After the Walkthrough

The human reviews the output and either:
- Signs off: flow matches intent, no follow-up needed
- Gives feedback: narrate issues to the agent using the live feedback pattern

> *"I'm going to describe issues. For each one: (a) do you understand it,
> (b) is it a quick fix or needs more thought, (c) could fixing it break
> anything else?"*

Apply the triage discipline: fix-now items (copy, layout, obvious single-line
bugs) are handled immediately. Items requiring architecture changes, stakeholder
input, or investigation are ticketed as `[BUG]` issues.

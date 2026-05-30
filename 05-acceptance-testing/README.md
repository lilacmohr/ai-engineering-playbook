# 05 — Acceptance Testing

Intent validation: confirming the built thing matches what a human actually wanted.

Acceptance testing answers the question tests cannot: **did we build the right thing?**
Code correctness — whether tests pass, whether the spec was followed — is a tractable
problem automated layers can handle. Intent alignment is not. A feature can pass every
test and still fail to serve the customer the way you meant.

This phase runs after `[IMPL]` closes and before the feature ships.

---

## The Extended Delivery Cycle

```
[CRITERIA]         → acceptance criteria defined before tests are written
[TEST]             → write failing tests
[IMPL]             → make tests pass
[ACCEPTANCE-PREP]  → automated pre-flight pipeline (agents only)
[ACCEPTANCE]       → human validation, scoped to judgment calls only
retro              → run the retro skill with acceptance testing as an added category
```

**[CRITERIA]** belongs at the front. The best time to define "done" from a user's
perspective is before the test file is written. Acceptance criteria defined upfront let
the `[TEST]` ticket include not just unit and integration tests but the user-facing
scenarios the pre-flight pipeline will later exercise. Without `[CRITERIA]`, acceptance
testing is guesswork — deciding after the fact what you should have decided before.

**[ACCEPTANCE-PREP]** is the full automated pre-flight pipeline. It runs before any
human opens the application. It is a ticket with a definition of done, not an informal
step. Everything systematic — bug hunting, edge case coverage, real-data failures —
happens here. By the time a human opens the application, they should be making
judgment calls, not hunting bugs.

**Retro** (the `retro` skill from `03-delivery-cycle`) gains a new category:
*what did acceptance testing surface that an earlier phase should have caught?*
Findings that feed back into spec review or delivery cycle artifacts belong in the
retro, not just in the ticket thread. This is not a new ticket type — it's the
existing retro with an additional acceptance testing lens applied.

---

## When to Open [CRITERIA]

Open a `[CRITERIA]` ticket when a feature is tagged `acceptance:required` or
`acceptance:data-only`. The trigger is simple:

> Does this feature have user-facing behavior that the test suite needs to
> know about before tests are written?

If yes: open `[CRITERIA]` before `[TEST]`. If no: apply `acceptance:skip` and
proceed directly to `[TEST]`.

```
Customer-facing feature?
  └─ Yes → Does it need a human walkthrough (UI, report, auth flow)?
              └─ Yes → Set acceptance:required. Open [CRITERIA] now.
              └─ No  → Customer-facing API or internal pipeline feature?
                          └─ Yes → Set acceptance:data-only. Open [CRITERIA] now.
                          └─ No  → Set acceptance:required. Open [CRITERIA] now — spec is underspecified.
  └─ No  → Set acceptance:skip. Proceed to [TEST].
```

`[CRITERIA]` must close before `[TEST]` opens. The acceptance criteria it defines
are what the pre-flight pipeline will later exercise — and what the `[TEST]` ticket
should cover in its user-facing scenarios.

---

## When to Skip

The test for whether acceptance testing is warranted: **is there a human-observable
behavior to validate?**

| Tag | When to apply |
|---|---|
| `customer-facing` | Feature is visible or impactful to end users |
| `acceptance:required` | Needs `[ACCEPTANCE-PREP]` + `[ACCEPTANCE]` tickets |
| `acceptance:data-only` | Internal or pipeline feature; real-data stress run sufficient, no human walkthrough |
| `acceptance:skip` | Pure logic or calculation; unit and integration tests are the full story |

| Feature type | Tags |
|---|---|
| Data ingestion / calculation | `acceptance:skip` |
| Internal API endpoint | `acceptance:skip` |
| Customer-facing API | `customer-facing`, `acceptance:data-only` |
| UI feature or flow | `customer-facing`, `acceptance:required` |
| Report with deterministic data | `customer-facing`, `acceptance:required` |
| Auth / permissions flow | `customer-facing`, `acceptance:required` |
| Background job / scheduler | `acceptance:skip` — test effects, not the job itself |

**Playwright tags** (a separate dimension from acceptance tags):

| Tag | Meaning |
|---|---|
| `playwright:required` | Deterministic UI flow; Playwright test must be written or updated |
| `playwright:skip` | Internal feature, LLM-generated content sections, or flow not yet acceptance-validated |

The rule: **write Playwright tests after `[ACCEPTANCE]` signs off on a flow.** The human
validates the intent; Playwright guards it forever after.

> Acceptance testing defines the bar. Playwright holds the bar.

---

## The Pre-Flight Pipeline

```
[ACCEPTANCE-PREP] pipeline:
Layer 0:   Playwright (existing tests)     → did we break anything?
Layer 1:   Demo Agent                      → here's what we built
Layer 2:   Adversarial Reviewer            → here's what looks wrong
Layer 3:   Real Data Stress Agent          → here's what breaks with real data
Layer 4:   Synthesis Agent                 → here's what the human needs to decide

[ACCEPTANCE] (next phase — not part of [ACCEPTANCE-PREP]):
           Human Acceptance                → judgment calls only
```

If Layer 0 fails, `[ACCEPTANCE-PREP]` does not proceed. Return the ticket to `[IMPL]`.

The pipeline produces a chain of artifacts: `ACCEPTANCE-DEMO.md` (Layer 1) is what
all subsequent layers work from. The adversarial reviewer reads it cold. The real-data
stress agent runs against the same flows it documents. The synthesis agent reads all
three prior outputs and produces the brief the human receives.

The pipeline is a ticket. The `[ACCEPTANCE-PREP]` issue template tracks completion
of each layer and surfaces the synthesis brief for the human session.

---

## Issue Taxonomy

| Issue type | Opens when | Closes when | Template |
|---|---|---|---|
| `[CRITERIA]` | Before `[TEST]`, when feature is `acceptance:required` or `acceptance:data-only` | Acceptance criteria are unambiguous and cover user-facing scenarios | [`templates/criteria-issue-template.md`](templates/criteria-issue-template.md) |
| `[ACCEPTANCE-PREP]` | After `[IMPL]` PR merges | All pipeline layers complete; synthesis brief produced | [`templates/acceptance-prep-issue-template.md`](templates/acceptance-prep-issue-template.md) |
| `[ACCEPTANCE]` | After `[ACCEPTANCE-PREP]` closes | Judgment calls resolved; fixes done or ticketed | [`templates/acceptance-issue-template.md`](templates/acceptance-issue-template.md) |
| `[BUG]` | Pre-flight or `[ACCEPTANCE]` finds a defect needing `[IMPL]` work | Same as `[IMPL]` — `[BUG]` is a label on a standard `[IMPL]` ticket, not a separate type. Use the `[IMPL]` template; add the `bug` label and a link back to the `[ACCEPTANCE-PREP]` or `[ACCEPTANCE]` issue that found it. | — |

---

## Skills

| Skill | Use when |
|---|---|
| [`acceptance-walkthrough`](skills/acceptance-walkthrough/SKILL.md) | Walk through a specific flow — triggers the demo agent for one feature |
| [`acceptance-prep`](skills/acceptance-prep/SKILL.md) | Run the full pre-flight pipeline for an `[ACCEPTANCE-PREP]` ticket |

---

## Personas

Run each in an isolated session. The adversarial reviewer especially must not share
context with the demo agent — isolation is what gives it signal.

| Persona | Role | Layer |
|---|---|---|
| [`demo-agent`](personas/demo-agent.md) | Builder self-demo; produces ACCEPTANCE-DEMO.md | 1 |
| [`adversarial-reviewer`](personas/adversarial-reviewer.md) | Skeptic reviewing the demo script — fresh session | 2 |
| [`real-data-stress`](personas/real-data-stress.md) | Exercises real data, produces structured failure report | 3 |
| [`synthesis-agent`](personas/synthesis-agent.md) | Reads all prior outputs; produces human-decisions-only brief | 4 |

---

## Evaluation

| Artifact | Purpose |
|---|---|
| [`evaluation/acceptance-scorecard.md`](evaluation/acceptance-scorecard.md) | Per-feature tracking template; fill out after each `[ACCEPTANCE]` closes |
| [`evaluation/agent-calibration.md`](evaluation/agent-calibration.md) | Calibration rubric for each persona — what well-calibrated looks like, signs of over/under-sensitivity, and how to recalibrate |
| [`evaluation/pipeline-review.md`](evaluation/pipeline-review.md) | Periodic review workflow — converts scorecard signal into concrete changes |

The scorecard answers: *is the proportion of issues that reach human `[ACCEPTANCE]`
declining over time?* The calibration rubric answers: *are the agents finding the
right things, or are they too noisy or too lenient?* The pipeline review is where
those signals become action — run it every 5th scorecard entry.

---

## Connecting to the Delivery Cycle

The delivery cycle in `03-delivery-cycle/` describes the base red-green-retro loop.
This phase extends it without replacing it.

- `[CRITERIA]` is drafted before the `[TEST]` ticket
- `[ACCEPTANCE-PREP]` opens after the `[IMPL]` PR merges
- `[BUG]` issues from pre-flight re-enter the delivery cycle as new `[IMPL]` tickets
- The `retro` skill gains a new category: what did acceptance testing find that earlier
  phases missed?

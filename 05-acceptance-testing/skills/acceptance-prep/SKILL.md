---
name: acceptance-prep
description: >
  Run the full acceptance testing pre-flight pipeline for a feature. Use when
  asked to "run acceptance prep", "run the pre-flight pipeline", "prepare for
  acceptance testing", "run [ACCEPTANCE-PREP]", or when an [ACCEPTANCE-PREP]
  issue is open and ready to start. Orchestrates all four pipeline layers:
  demo agent, adversarial reviewer, real-data stress agent, synthesis agent.
  Produces the synthesis brief that the human uses to run the [ACCEPTANCE] session.
argument-hint: 'PR number or feature name (e.g. 42 or "report export feature")'
---

# Acceptance Pre-Flight Pipeline

Orchestrate the four-layer pre-flight pipeline for a feature and produce the
synthesis brief the human needs to open the `[ACCEPTANCE]` session.

---

## Prerequisites

Before starting:

- [ ] `[IMPL]` PR is merged
- [ ] Playwright suite passes against the current build
  - Run or confirm: `make test` / `npx playwright test` (or project equivalent)
  - If Playwright fails: stop here. Return the ticket to `[IMPL]`.
- [ ] A real data snapshot is available (sanitized/anonymized)
  - If no snapshot exists: note this gap, proceed with Layer 1 and 2 only,
    and flag that Layer 3 was skipped and why

If the PR number is not provided and cannot be inferred, ask before proceeding.

---

## Layer 1: Demo Agent

Apply the [`demo-agent`](../../personas/demo-agent.md) persona.

Read the PR diff and CLAUDE.md. Produce `ACCEPTANCE-DEMO.md` covering:
- Every user-facing flow (entry point, happy path, expected output)
- Edge cases handled and not handled per flow
- Integration points and failure handling
- Assumptions made during implementation
- Three lowest-confidence areas

Post the demo document as a PR comment or artifact before proceeding to Layer 2.

---

## Layer 2: Adversarial Reviewer

Apply the [`adversarial-reviewer`](../../personas/adversarial-reviewer.md) persona.

**Important:** Treat this as a fresh context. Read `ACCEPTANCE-DEMO.md` and the
PR diff. Do not carry forward the reasoning from Layer 1.

Find:
- Flows not demoed
- Assumptions that won't hold with real data
- Optimistic claims that can't be verified
- Edge cases in the "not handled" list that warrant reconsideration

Label all findings: `[BLOCKING]`, `[AMBIGUITY]`, `[FALSE PRECISION]`, `[SUGGESTION]`, `[NIT]`.

**After Layer 2:**
- `[BLOCKING]` findings → open `[BUG]` tickets before proceeding to Layer 3
- `[AMBIGUITY]` findings → carry forward to Layer 4 (synthesis)
- Other findings → carry forward to Layer 4

---

## Layer 3: Real Data Stress Agent

Apply the [`real-data-stress`](../../personas/real-data-stress.md) persona.

Run each flow from `ACCEPTANCE-DEMO.md` against the real data snapshot.
Target specifically:
- Null or missing fields
- Long strings and special characters
- Boundary dates
- Orphaned references
- Unexpected record shapes

Produce the structured failure report with `[CRITICAL]`, `[DEGRADED]`, and
`[COSMETIC]` classifications.

**After Layer 3:**
- `[CRITICAL]` failures → open `[BUG]` tickets before proceeding to Layer 4
- `[DEGRADED]` and `[COSMETIC]` findings → carry forward to Layer 4

---

## Layer 4: Synthesis Agent

Apply the [`synthesis-agent`](../../personas/synthesis-agent.md) persona.

Read all three prior outputs. Produce the human decisions brief:
- 2–4 items only
- Each item must require human judgment — not engineering judgment
- Scope questions, UX trade-offs, stakeholder calls, product decisions

Post the brief to the `[ACCEPTANCE-PREP]` issue as the final output.

---

## If Blocking Issues Are Found and Fixed

When `[BLOCKING]` adversarial findings or `[CRITICAL]` real-data failures produce
`[BUG]` tickets that are subsequently fixed, do not re-run the entire pipeline from
Layer 0. Re-run only from the layer that found the blocking issue:

- `[BLOCKING]` from Layer 2 fixed → re-run Layer 2 (adversarial review) only,
  then re-run Layer 4 (synthesis) to update the brief
- `[CRITICAL]` from Layer 3 fixed → re-run Layer 3 (real data stress) only,
  then re-run Layer 4 (synthesis) to update the brief
- If the fix is large enough to plausibly affect earlier layers (e.g., a major
  flow was changed), re-run from Layer 1

Layer 0 (Playwright) does not need to re-run unless the bug fix touches existing
tested flows — that is a judgment call, not a rule.

---

## Output: [ACCEPTANCE-PREP] Completion Report

After all four layers, post the following to the `[ACCEPTANCE-PREP]` issue:

```
## [ACCEPTANCE-PREP] Complete — [Feature Name]

### Pipeline Status

- [x] Layer 0: Playwright — passed / [N] failures (return to [IMPL] if any)
- [x] Layer 1: Demo Agent — ACCEPTANCE-DEMO.md produced
- [x] Layer 2: Adversarial Review — [N] [BLOCKING], [N] [AMBIGUITY], [N] other
- [x] Layer 3: Real Data Stress — [N] [CRITICAL], [N] [DEGRADED], [N] [COSMETIC]
- [x] Layer 4: Synthesis — [N] human decisions identified

### Tickets Opened
[List any [BUG] tickets opened during pre-flight, with links]

### Human Decisions Brief
[Paste synthesis output here]

### Ready for [ACCEPTANCE]
- [ ] All [BLOCKING] adversarial findings have linked [BUG] tickets
- [ ] All [CRITICAL] real-data failures have linked [BUG] tickets
- [ ] Human decisions brief has been read
```

The `[ACCEPTANCE-PREP]` issue closes when this checklist is complete. The
`[ACCEPTANCE]` issue can then open.

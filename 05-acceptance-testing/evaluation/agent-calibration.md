# Agent Calibration

The scorecard tracks what the pipeline found. This document tracks whether the
agents are finding the right things — whether each layer's sensitivity is set
correctly. A well-calibrated pipeline catches real issues and suppresses noise.
A miscalibrated one is either a rubber stamp or a false-alarm generator.

Check calibration during the periodic pipeline review (see `pipeline-review.md`).

---

## How to Read This

For each agent, three calibration states are described:

| State | Meaning |
|---|---|
| Well-calibrated | The agent is finding real issues at the right threshold |
| Over-sensitive | The agent is producing noise — false positives, excessive escalation |
| Under-sensitive | The agent is missing real issues — false negatives |

The recalibration action is a specific change to make: to the persona file, to
the [CRITERIA] template, or to the pipeline setup. Vague notes ("do better") are
not recalibration.

---

## Layer 1: Demo Agent

**Well-calibrated when:**
- All user-facing flows are documented in ACCEPTANCE-DEMO.md
- The "edge cases not handled" section is explicit — it lists real gaps, not a
  boilerplate disclaimer
- Where the demo says a behavior is "handled," it specifies how — not just that it is

**Over-sensitive (excessive caveats):**
- "Edge cases not handled" section is so long that the adversarial reviewer and
  synthesis agent spend time on things that are genuinely out of scope or
  clearly acceptable gaps
- The demo spends more words disclaiming than describing

Signal: adversarial reviewer and synthesis agent consistently ignore the
"not handled" list because it's not actionable.

Recalibration: Add guidance to the demo agent persona that "not handled" items
must be things a user could plausibly encounter, not an exhaustive theoretical
edge case list. Cap the section at 5 items; force prioritization.

**Under-sensitive (over-optimistic):**
- Adversarial reviewer consistently finds `[FALSE PRECISION]` — claims of
  "handled" that the code doesn't support
- The `[ACCEPTANCE]` session discovers flows that aren't described in the demo
- "Edge cases not handled" section is empty or one line

Signal: adversarial reviewer [FALSE PRECISION] count is consistently > 2 per cycle.

Recalibration: Add a minimum coverage checklist to the demo agent persona. Require
the demo agent to check each claim of "handled" against the actual code before
writing it. Add examples of acceptable vs. unacceptable vagueness.

---

## Layer 2: Adversarial Reviewer

**Well-calibrated when:**
- `[BLOCKING]` items are almost always confirmed at `[ACCEPTANCE]` — the human
  agrees they were real problems
- `[SUGGESTION]` items are genuinely useful, not restatements of spec requirements
- The review finds issues the demo agent didn't surface, and those issues turn
  out to matter

**Over-sensitive (too harsh):**
- `[BLOCKING]` items are frequently downgraded or dismissed during `[ACCEPTANCE]`
- The human disagrees with what constitutes a blocker
- `[FALSE PRECISION]` findings are flagged on language that is adequately specific
  in context

Signal: more than 1 in 3 `[BLOCKING]` items is downgraded in `[ACCEPTANCE]` over
the last 5 cycles.

Recalibration: Add examples of what `[BLOCKING]` is *not* to the persona. Review
the last 3–5 dismissed findings and identify the pattern. Update the persona's
label criteria section with concrete counter-examples.

**Under-sensitive (too lenient):**
- `[ACCEPTANCE]` consistently surfaces blocking issues the adversarial reviewer
  didn't flag
- Synthesis agent receives very few `[AMBIGUITY]` items to escalate
- `[BLOCKING]` count is consistently 0 across multiple cycles (possible but worth
  investigating — is the reviewer actually checking the code, or just the demo?)

Signal: `[ACCEPTANCE]` finds a missed blocking issue more than once across the
last 5 cycles.

Recalibration: Verify the reviewer is reading the PR diff, not just
ACCEPTANCE-DEMO.md. Add the specific miss type to the "What to Look For"
section of the persona. If the reviewer was over-relying on the demo's framing,
add a prompt to deliberately look for flows that aren't in the demo.

---

## Layer 3: Real Data Stress Agent

**Well-calibrated when:**
- `[CRITICAL]` failures are real code bugs triggered by real data — not problems
  with the snapshot itself
- `[DEGRADED]` outputs reflect genuine product quality degradation a user would
  notice, not formatting preferences
- The agent finds at least one issue per cycle that fixture-based tests didn't
  surface (if it never finds anything, either the feature is genuinely robust or
  the agent isn't reaching the interesting data)

**Over-sensitive (too noisy):**
- `[CRITICAL]` items turn out to be problems with the data snapshot: orphaned
  records from old migrations, test records in production, data that pre-dates
  the current schema
- Engineer time is spent investigating issues that aren't real bugs

Signal: more than 1 in 4 `[CRITICAL]` items is attributed to snapshot quality
rather than code behavior over the last 5 cycles.

Recalibration: Add a snapshot validation step before the stress run. Flag
obviously corrupted or pre-schema records and exclude them from the run, or add
them to a known-bad list. Adjust the `[CRITICAL]` vs. `[DEGRADED]` threshold if
the boundary is consistently in the wrong place.

**Under-sensitive (missing coverage):**
- `[ACCEPTANCE]` or production finds real-data failures the stress agent didn't
  surface
- The "clean flows" list is suspiciously long — every flow passes
- The agent is only running happy-path records, not the messy categories
  (null fields, long strings, encoding edge cases)

Signal: a production incident involves a real-data failure class that was in
scope for the stress agent.

Recalibration: Verify the agent is exercising all record categories in the persona,
not just the first few. Check whether the snapshot contains records from each
category — if not, refresh it. If a specific category (e.g., encoding) keeps
getting missed, add a dedicated data fixture for that category.

---

## Layer 4: Synthesis Agent

**Well-calibrated when:**
- Brief contains 2–4 items requiring genuine human judgment
- Human resolves all brief items during `[ACCEPTANCE]`; none are punted to a
  follow-on session
- Nothing in the brief could have been resolved by an engineer with the spec and
  enough time

**Over-sensitive (too broad):**
- Brief has 5+ items
- Several items are engineering decisions the spec already answers, or cosmetic
  findings that don't rise to a product decision
- Human `[ACCEPTANCE]` session runs long because of unnecessary escalation

Signal: synthesis brief consistently has > 4 items; human closes most of them
quickly as "obviously do X."

Recalibration: Apply the "would a reasonable engineer resolve this given time
and context?" test more strictly. Review recent briefs and remove item types
that were consistently resolved without real human judgment. If the same type
of decision keeps appearing, it belongs in `[CRITERIA]` — encode it there
so it's resolved before `[ACCEPTANCE-PREP]` runs.

**Under-sensitive (too narrow):**
- Brief is empty, but the `[ACCEPTANCE]` session still surfaces decisions
- Human makes judgment calls that weren't in the brief, and those calls represent
  real product questions (not just bugs that got through)

Signal: human `[ACCEPTANCE]` surfaces a genuine product decision more than once
that synthesis didn't flag.

Recalibration: Check whether the agent is reading all three prior outputs, or
skipping `[DEGRADED]` items from the real-data report. Review the missed decision
types and add them to the persona's "Items that belong in the brief" list.

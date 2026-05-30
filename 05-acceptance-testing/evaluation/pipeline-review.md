# Periodic Pipeline Review

The scorecard accumulates signal. This review converts that signal into changes.
Run it after every 5th scorecard entry, or whenever a `[ACCEPTANCE]` session
surfaces something the entire pre-flight pipeline missed.

The review takes one session. The output is not a report — it is specific changes
made to at least one of: a persona file, a template, or CLAUDE.md. If no changes
are made, the review did not happen.

---

## Who Runs It

The person who most recently ran `[ACCEPTANCE]` — they have the freshest signal
on what the pipeline did and didn't surface. If multiple people have run
`[ACCEPTANCE]` in the last 5 cycles, one person reviews the scorecard entries
and presents findings; the team decides what to change.

---

## Inputs

- Last 5–10 scorecard entries (from `acceptance-scorecard.md`)
- The persona files (`personas/`)
- Any `[BUG]` tickets opened from `[ACCEPTANCE]` sessions in that window

---

## Step 1: Read the Aggregate Signals

Pull up the last 5–10 scorecard entries and run through the Aggregate Signals
table in `acceptance-scorecard.md`. For each signal that's firing, note:

- Which layer is the issue?
- How many consecutive cycles has it appeared?
- Is it the same sub-type each time, or different?

A signal that fires once is noise. A signal that fires in 3 of 5 cycles is a
pattern worth acting on.

---

## Step 2: Run the Calibration Check

For each agent layer, pull the calibration entries from the scorecard and ask:

**Layer 1 — Demo Agent**
- What fraction of adversarial `[FALSE PRECISION]` findings are attributable to
  the demo being vague vs. the reviewer being too strict?
- Did `[ACCEPTANCE]` find undocumented flows more than once?

**Layer 2 — Adversarial Reviewer**
- What fraction of `[BLOCKING]` items were confirmed vs. downgraded at `[ACCEPTANCE]`?
- Did `[ACCEPTANCE]` surface anything the adversarial reviewer should have caught?

**Layer 3 — Real Data Stress Agent**
- What fraction of `[CRITICAL]` items were real code bugs vs. snapshot artifacts?
- Did `[ACCEPTANCE]` or production surface any real-data failures the stress agent missed?

**Layer 4 — Synthesis Agent**
- What was the average brief length? Were items consistently resolved quickly or
  did they represent real judgment calls?
- Did `[ACCEPTANCE]` surface product decisions not in the brief?

Use `agent-calibration.md` to identify whether each layer is well-calibrated,
over-sensitive, or under-sensitive, and what the recalibration action is.

---

## Step 3: Identify the Weakest Layer

Name one layer as the primary target for this review. Do not try to fix everything
at once. The pipeline has four layers; improving the weakest one produces the most
gain.

If all layers look calibrated, check the upstream: is `[CRITERIA]` underspecified?
Are tickets missing user-facing scenarios that the pre-flight pipeline needs to
exercise? If so, the fix is in the `[CRITERIA]` template or in spec review, not
in the pre-flight pipeline.

---

## Step 4: Make the Changes

Changes belong in one of these places:

| What changed | Where to make the change |
|---|---|
| Agent is finding the wrong things | Edit the relevant persona file |
| The same class of decision keeps reaching humans | Add it to `[CRITERIA]` template or CLAUDE.md |
| Playwright tests are being written before acceptance validates the flow | Update CLAUDE.md with the rule |
| A specific real-data failure type keeps slipping through | Refresh the data snapshot; add to the stress agent's record categories |
| Pre-flight finds nothing; `[ACCEPTANCE]` consistently finds things | Strengthen the adversarial reviewer persona; review whether the demo agent is too optimistic |

Make the change in the session. Do not create a ticket to do it later. If the
change takes longer than 30 minutes, the scope is wrong — narrow it.

---

## Step 5: Close the Review

The review is complete when:

- [ ] At least one file has been changed (persona, template, or CLAUDE.md)
- [ ] The change is explained in a commit message or issue comment (future-you needs
  to know why the persona changed)
- [ ] Write one sentence in a comment on this review (or in the commit message)
  describing what signal in the next scorecard entry will confirm the change worked
  (e.g., "next cycle: check whether [BLOCKING] downgrade rate dropped below 1 in 3")

If the review produced no changes, write down why — "pipeline is well-calibrated
and no action is needed" is a valid conclusion, but it requires the reasoning
to be visible, not just the outcome.

---

## Cadence

| Trigger | Action |
|---|---|
| Every 5th scorecard entry | Run the full review |
| `[ACCEPTANCE]` surfaces something the entire pipeline missed | Run the review immediately; don't wait for the 5-entry cadence |
| 3 consecutive empty synthesis briefs | Run the review to confirm the pipeline is working, not just silent |
| Pre-flight consistently finds nothing across low-risk follow-ons | Consider shortcutting to adversarial review only for those ticket types; confirm with scorecard data before making it the default |

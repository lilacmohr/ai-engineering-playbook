# [ACCEPTANCE-PREP] Feature name

<!-- Open after the [IMPL] PR merges. Run the pre-flight pipeline using the
`acceptance-prep` skill. Do not open [ACCEPTANCE] until this ticket closes. -->

## Feature
<!-- What feature is this pipeline run for? -->

**[IMPL] PR:** #
**[CRITERIA] issue:** #

---

## Pipeline checklist

### Layer 0: Playwright
- [ ] Playwright suite passes against the current build
  - Command run:
  - Result:
- **If any Playwright tests fail: stop. Do not proceed. Return to [IMPL].**

### Layer 1: Demo Agent
- [ ] `ACCEPTANCE-DEMO.md` produced and committed to the PR
  - Link to artifact:
  - Flows documented: N

### Layer 2: Adversarial Review
- [ ] Review complete (fresh session — do not share context with Layer 1)
  - `[BLOCKING]` items found: N
  - `[AMBIGUITY]` items found: N
  - `[FALSE PRECISION]` items found: N
  - `[SUGGESTION]` items found: N
- [ ] All `[BLOCKING]` items have linked `[BUG]` tickets:
  - 
- [ ] `[AMBIGUITY]` and other findings carried forward to Layer 4

### Layer 3: Real Data Stress
- [ ] Stress run complete
  - Snapshot version/date:
  - Flows exercised: N / [total]
  - `[CRITICAL]` failures: N
  - `[DEGRADED]` outputs: N
  - `[COSMETIC]` issues: N
- [ ] All `[CRITICAL]` failures have linked `[BUG]` tickets:
  - 
- [ ] `[DEGRADED]` and `[COSMETIC]` findings carried forward to Layer 4

*If no real data snapshot is available: document this here, proceed with Layers
1 and 2 only, and note the gap in the completion report below.*

### Layer 4: Synthesis
- [ ] Brief produced
  - Human decisions identified: N
- [ ] Brief posted in the section below

---

## Human decisions brief

<!-- Paste the synthesis agent output here. The human reads this before opening
the application for [ACCEPTANCE]. -->

---

## Ready for [ACCEPTANCE]
- [ ] All `[BLOCKING]` adversarial findings have linked `[BUG]` tickets
- [ ] All `[CRITICAL]` real-data failures have linked `[BUG]` tickets
- [ ] Human decisions brief is posted above
- [ ] Human has confirmed they have read the brief

## Done when
All pipeline checklist items are complete and the synthesis brief is posted.
The `[ACCEPTANCE]` issue can open when this ticket closes.

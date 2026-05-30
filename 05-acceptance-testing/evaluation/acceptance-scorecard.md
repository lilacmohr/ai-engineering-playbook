# Acceptance Testing Scorecard

Track pipeline performance across cycles. The goal is a pipeline that improves
over time: fewer issues reaching human `[ACCEPTANCE]`, and better-quality
judgment calls when they do.

Fill this out after each `[ACCEPTANCE]` issue closes. The signal accumulates
across features and cycles — a single scorecard entry tells you little; a
pattern across ten tells you where to improve.

---

## Per-Feature Entry

```
## [Feature Name] — [Date] — PR #[N]

### Pipeline coverage

#### Layer 1: Demo Agent
- Flows documented in ACCEPTANCE-DEMO.md: N
- Did [ACCEPTANCE] find flows the demo agent omitted? Y / N

#### Layer 2: Adversarial Review
- [BLOCKING] items found: N
- [AMBIGUITY] items found: N
- [FALSE PRECISION] items found: N
- Were any [BLOCKING] items things that should have been caught by:
  - The test suite? Y / N
  - Spec review? Y / N
  - CLAUDE.md rules? Y / N

#### Layer 3: Real Data Stress
- Real data snapshot version: [version/date]
- Flows exercised: N / [total documented in ACCEPTANCE-DEMO.md]
- [CRITICAL] failures found: N
- [DEGRADED] outputs found: N
- Were any [CRITICAL] failures things that should have been caught by:
  - Fixture-based unit tests? Y / N
  - Integration tests? Y / N

#### Layer 4: Synthesis
- Human decisions in the brief: N
- Were any decisions in the brief things that should have been resolved:
  - In [CRITERIA]? Y / N
  - In spec review? Y / N

### Human [ACCEPTANCE] session
- Fix-now items addressed in session: N
- [BUG] tickets opened: N
- Were any items found during [ACCEPTANCE] that the pre-flight pipeline missed?
  If yes, which layer should have caught them?

### Calibration check
- Did adversarial reviewer [BLOCKING] items hold up at [ACCEPTANCE]? Y / N
  - Items downgraded or dismissed: [list or "none"]
- Did the synthesis brief contain the right class of decisions? Y / N
  - Too many items (some resolved without real human judgment): Y / N
  - Too few items ([ACCEPTANCE] surfaced decisions not in the brief): Y / N
- Did [ACCEPTANCE] find real-data failures the stress agent missed? Y / N

### Retro findings
- Items encoded in CLAUDE.md from this cycle: N
- Items added to issue templates: N
- Items that changed the [CRITERIA] process: N
```

---

## Aggregate Signals

Review across the last 5–10 entries and look for patterns:

| Signal | What it means | Action |
|---|---|---|
| Adversarial review consistently finds `[BLOCKING]` items | Demo agent is too optimistic; or test coverage is thin | Tighten `[TEST]` template's Done When checklist |
| Real data stress consistently finds `[CRITICAL]` failures | Fixture data is diverging from production | Refresh snapshot more frequently; add real-data records to fixture set |
| Synthesis brief consistently has 4+ items | `[CRITERIA]` is underspecified; or spec review is missing intent questions | Update `[CRITERIA]` template; add persona to spec review |
| Human `[ACCEPTANCE]` consistently finds things pre-flight missed | A specific layer is weak | Strengthen the relevant persona |
| Pre-flight finds nothing; `[ACCEPTANCE]` is a formality | Pipeline is working well | Consider running only the adversarial review layer and skipping real-data stress on low-risk follow-on iterations. Use the scorecard to confirm the shortcut is safe before making it the default. |
| Adversarial `[BLOCKING]` items consistently downgraded at `[ACCEPTANCE]` | Reviewer is over-sensitive; blocking threshold is too low | Review dismissed items for the pattern; add counter-examples to the persona's label criteria |
| Adversarial review finds 0 `[BLOCKING]` items across multiple cycles | Reviewer may be under-sensitive, or features are genuinely clean | Cross-check: did `[ACCEPTANCE]` find anything? If yes, the reviewer missed it — tighten the persona |
| `[CRITICAL]` real-data failures are consistently snapshot artifacts, not bugs | Snapshot quality is degrading; or `[CRITICAL]` threshold is wrong | Add snapshot validation; adjust the `[CRITICAL]` vs. `[DEGRADED]` threshold |
| Synthesis brief consistently has 0 items but `[ACCEPTANCE]` makes decisions | Synthesis agent is filtering too aggressively | Review missed decision types; add them to the persona's "belongs in the brief" list |

---

## The Improvement Signal

The pipeline is improving when:
- Fewer issues reach human `[ACCEPTANCE]` over time (pre-flight is catching more)
- The synthesis brief gets shorter (intent is better specified upfront)
- `[ACCEPTANCE]` sessions get faster (the human knows what to look for)
- `[CRITERIA]` tickets get richer (teams learn what to specify early)

The pipeline is degrading when:
- `[ACCEPTANCE]` sessions consistently surface things pre-flight missed
- The same class of real-data failure appears across multiple cycles
- Synthesis briefs are consistently empty (pipeline running but adding no value)
  or consistently long (human judgment is not being encoded into agent layers)

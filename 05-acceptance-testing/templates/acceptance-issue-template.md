# [ACCEPTANCE] Feature name

<!-- Open after [ACCEPTANCE-PREP] closes. The synthesis brief is your agenda —
read it before opening the application. Your job is judgment calls, not bug hunting. -->

## Feature
<!-- What feature is this session for? -->

**[ACCEPTANCE-PREP] issue:** #
**Synthesis brief:** [paste link or scroll to [ACCEPTANCE-PREP] for the brief]

---

## Pre-session checklist
- [ ] Synthesis brief has been read
- [ ] All `[BLOCKING]` adversarial items have linked `[BUG]` tickets
- [ ] All `[CRITICAL]` real-data failures have linked `[BUG]` tickets

---

## Decisions made

<!-- For each item in the synthesis brief, record your decision. -->

### Decision: [title from brief]
**Decision:**

### Decision: [title from brief]
**Decision:**

<!-- Add sections for each item in the brief. -->

---

## Issues found during session

### Fixed immediately
<!-- Items addressed during the walkthrough: copy changes, layout tweaks, single-line bugs. -->

- 

### [BUG] tickets opened
<!-- Items requiring [IMPL] work. Link each ticket. -->

- 

---

## Post-session checklist
- [ ] All flows in `ACCEPTANCE-DEMO.md` have been walked
- [ ] All synthesis brief decisions are documented above
- [ ] Fix-now items have been fixed and confirmed
- [ ] Follow-on items have `[BUG]` tickets linked back to this issue
- [ ] Playwright tests written (or `[IMPL]` ticket opened) for newly validated flows
- [ ] Scorecard entry added to [`evaluation/acceptance-scorecard.md`](../evaluation/acceptance-scorecard.md)

## Done when
All post-session checklist items are complete. Decisions are documented in this
issue thread so the intent is visible when someone asks why a feature behaves
a certain way.

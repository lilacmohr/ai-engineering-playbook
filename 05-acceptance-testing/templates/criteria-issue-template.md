# [CRITERIA] Feature name

<!-- Open this ticket before [TEST]. It defines what "done" looks like from a
user's perspective, so the test suite can cover the right scenarios. -->

## Feature
<!-- What is this feature? One sentence. Link to the spec or planning ticket. -->

## User-facing scenarios

<!-- Define acceptance criteria in observable, non-implementation terms.
Each scenario follows Given / When / Then. At minimum: happy path, empty or
zero-data state, and the primary error state. -->

### Scenario 1: [name]
- **Given:** 
- **When:** 
- **Then:** 

### Scenario 2: [name]
- **Given:** 
- **When:** 
- **Then:** 

<!-- Add scenarios for each meaningful user-facing behavior. -->

## Out of scope
<!-- Behaviors that are explicitly not covered by this feature. Name them —
it prevents scope creep and tells the acceptance pipeline what not to test. -->

## Acceptance tag
<!-- Choose one and delete the other. Set this tag on the linked [TEST] issue too.
     ([CRITERIA] is only opened when acceptance:skip does not apply. If you'd use
     acceptance:skip, close this ticket and proceed directly to [TEST].) -->

- `acceptance:required` — customer-facing; needs [ACCEPTANCE-PREP] + [ACCEPTANCE]
- `acceptance:data-only` — internal or pipeline feature; real-data stress run sufficient, no human walkthrough

## Done when
- [ ] Every user-facing scenario has an observable Given/When/Then (no implementation terms)
- [ ] Out-of-scope behaviors are listed
- [ ] Acceptance tag is set on this issue and on the linked [TEST] issue
- [ ] This issue is linked to the [TEST] issue it will inform

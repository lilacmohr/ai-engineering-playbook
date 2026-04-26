---
name: draft-tickets
description: >
  Draft the [TEST] and [IMPL] ticket content for one phase of the implementation
  plan. Use when asked to "draft tickets for phase N", "write tickets for [module]",
  "create the TEST and IMPL issues for [phase]", or "prepare the next phase tickets".
  Produces filled-in ticket content for every module in the phase, ready for
  review-issue to validate before the tickets are opened. Run one phase at a time.
argument-hint: 'phase number or module name (e.g. "phase 1" or "pre_filter")'
---

# Draft [TEST] and [IMPL] Tickets

For each module in the specified phase, produce fully filled-in ticket content
following the project's issue templates. The output is ready-to-paste content
for GitHub issues — the engineer reviews each ticket and runs `review-issue`
before opening it.

**One phase at a time.** Don't draft phase 2 tickets until phase 1 is underway.
Context and decisions from phase 1 implementation often inform phase 2 ticket content.

---

## Prerequisites

Before drafting, verify:
1. The phase plan from `breakdown-spec` has been reviewed and approved
2. All pre-ticket decisions for this phase are resolved (no open `[DECISION]` blockers)
3. `CLAUDE.md` and `SPEC.md` are current
4. Any data model types this phase depends on are defined (even if not yet implemented)

If pre-ticket decisions are unresolved, stop and tell the engineer which `[DECISION]`
issues must be closed first. Do not draft tickets for blocked modules.

---

## Inputs

Read in this order:

1. **The phase plan** (output of `breakdown-spec`, or engineer's description of the phase)
2. **SPEC.md** — the sections referenced for each module in this phase
3. **CLAUDE.md** — failure handling table (§5), testing standards (§6), quality gates (§7)
4. **Data model definitions** — the types this phase's modules consume and produce
5. **Existing test infrastructure** — `conftest.py`, fixture files, to know what already exists

---

## Drafting Process

For each module in the phase, produce a TEST ticket and an IMPL ticket in sequence.
Draft the TEST ticket first — the test cases it specifies become the behavioral
summary in the IMPL ticket.

### Drafting the [TEST] ticket

Fill every field:

**Module** — exact file path, derived from SPEC.md and CLAUDE.md file naming conventions.

**Test file to create** — the mirror path in the test directory. Follow the naming
convention in CLAUDE.md (e.g., `tests/unit/test_{module_name}.py`).

**Spec reference** — the exact sections of SPEC.md that define the behavior being
tested. Be specific: `SPEC.md §3.2, §3.7` not `SPEC.md`.

**Module context** — copy or summarize directly from SPEC.md:
- What stage/role this module plays
- Input type (exact type from data model)
- Output type (exact type from data model)
- Position in the pipeline (what calls it, what it calls)

**Happy path tests** — derive from the spec's behavioral requirements. One line per
test case. Be specific about inputs and expected outputs — not "tests filtering" but
"items where any keyword matches title → included in output". Every behavior the spec
describes needs a test.

**Failure mode tests** — derive from CLAUDE.md's failure handling table (§5) for
this module's failure types. Include:
- Empty input (every module that filters or transforms must handle `[]` without raising)
- Each external dependency failure that applies to this module
- Any module-specific edge cases the spec describes

**Interface/contract tests** — if the module implements a public interface (abstract
base class, protocol, or shared contract), list the contract compliance tests. Otherwise
`N/A` with explanation.

**Fixtures required** — list what test data this module needs:
- New fixtures to create (path + description)
- Existing fixtures it can reuse (from conftest.py or fixtures directory)
- Pydantic/dataclass validation constraints that affect fixture construction

**Mocking strategy** — derived from CLAUDE.md testing standards and the module's
external dependencies:
- Pure logic (no I/O) → no mocking, use real instances
- External HTTP → mock at the HTTP client level
- LLM calls → use the project's test LLM client
- Database → per project's established pattern
Include the mock depth check note if the module has external dependencies.

**Done when** — use the standard checklist from the issue template. Add any
module-specific items (e.g., "if this module parses structured LLM output, include
regression test for parser constants").

### Drafting the [IMPL] ticket

Fill every field:

**Module to implement** — same path as the TEST ticket's Module field.

**Paired [TEST] issue** — leave as `#TBD` — the engineer fills this in after
opening the TEST ticket.

**Test file** — same path as the TEST ticket's "Test file to create" field.

**Spec reference** — same as TEST ticket.

**Pipeline context** — stage, input type, output type, caller, callees. Should
match TEST ticket's Module Context exactly.

**Public interface** — the method signature(s) this module must expose, derived
from SPEC.md and the test cases in the TEST ticket. Format as a code block with
the exact signature. Flag any interface decisions not fully specified by the spec
with `[DECISION NEEDED: <description>]`.

**Key behaviors** — a readable summary of behavioral requirements from SPEC.md,
framed as "the test file is authoritative; this is a summary." Do not invent
requirements not in the spec.

**Failure handling** — derived from CLAUDE.md's failure handling table for this
module's failure types. Format as a bullet list matching CLAUDE.md's lookup table
format.

**Dependencies** — internal imports only. Derived from data model and architecture:
- Which model types it uses
- Which modules it calls
- Standard library only, or which approved packages (from CLAUDE.md dependencies section)
Flag any dependency not already in the approved list with `[DECISION NEEDED]`.

**Done when** — use the standard checklist from CLAUDE.md quality gates (§7). Add
module-specific items if the spec calls for them.

**Explicitly out of scope** — critical field. Derive from:
- Adjacent modules whose responsibility is clearly the neighbor's (not this module's)
- Spec sections this module explicitly does not cover
- Common scope creep patterns for this type of module (e.g., "do not fetch data —
  that's the fetcher's job")

---

## Output Format

For each module in the phase, produce ticket content in this structure:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MODULE: [module name] ([phase])
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

── [TEST] TICKET ──────────────────────────────────────

Title: [TEST] Write tests for [module name]

Module: [file path]
Test file to create: [test file path]
Spec reference: [sections]

Module Context:
[content]

Happy Path Tests:
[content]

Failure Mode Tests:
[content]

Interface/Contract Tests:
[content]

Fixtures Required:
[content]

Mocking Strategy:
[content]

Done When:
[checklist]

Paired [IMPL] issue: #TBD (fill in after opening this issue)

── [IMPL] TICKET ──────────────────────────────────────

Title: [IMPL] Implement [module name]

Module to implement: [file path]
Paired [TEST] issue: #TBD
Test file: [test file path]
Spec reference: [sections]

Pipeline Context:
[content]

Public Interface:
[code block]

Key Behaviors:
[content]

Failure Handling:
[content]

Dependencies:
[content]

Done When:
[checklist]

Explicitly Out of Scope:
[content]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

After all modules in the phase, output:

```
PHASE [N] DRAFT SUMMARY
────────────────────────────────────────────────────────────
Modules drafted: N ([list])
Tickets produced: N TEST + N IMPL = N total

Pre-open checks:
  → Run review-issue on each ticket before opening
  → Resolve any [DECISION NEEDED] items flagged above before opening affected tickets
  → Open TEST tickets first; add IMPL ticket number to paired field when opening IMPL

Decisions flagged during drafting:
  → [Description, if any]
  → None
────────────────────────────────────────────────────────────
```

---

## What Not to Do

- Do not invent test cases the spec doesn't support — only test specified behavior
- Do not leave any required field empty or with a placeholder that isn't `#TBD`
  (the `#TBD` convention is only for the cross-reference between paired tickets)
- Do not draft tickets for the next phase in the same run
- Do not resolve spec ambiguities about interfaces — flag with `[DECISION NEEDED]`
- Do not merge the TEST and IMPL tickets into one — TDD discipline requires them separate

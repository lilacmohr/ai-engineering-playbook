---
name: breakdown-spec
description: >
  Read the project spec and produce a phased implementation plan: modules identified,
  ordered by dependency, grouped into phases. Use when asked to "break down the spec",
  "create an implementation plan", "phase the work", "what order do we build things",
  or "plan the tickets". Output is a phase plan the engineer reviews and approves
  before draft-tickets runs on each phase. Does not create tickets — that's draft-tickets.
argument-hint: 'path to spec file (default: SPEC.md)'
---

# Spec Breakdown — Phased Implementation Plan

Read the spec and produce a dependency-ordered phase plan. The output is a structured
artifact the engineer reviews, edits, and approves before ticket drafting begins.

**This skill plans. It does not create tickets.** Ticket drafting is a separate step
(`draft-tickets`), run one phase at a time as each phase completes.

---

## When to Use

After `audit-pre-impl` passes (sprint zero is complete). Before any feature ticket
is opened. The phase plan is the bridge between the spec and the delivery cycle.

---

## Inputs

Read in this order:

1. **SPEC.md** — primary source: architecture, data flow, module descriptions,
   data models, dependencies
2. **CLAUDE.md** — architecture section, to validate your module reading
3. **Existing directory structure** — to distinguish what's already scaffolded
   from what's not yet built
4. **Open GitHub issues** (if accessible) — to avoid duplicating work already in flight

---

## Breakdown Process

### Step 1 — Identify all discrete modules

From the spec's architecture and data flow sections, list every distinct module that
needs implementation. A module is a discrete unit with:
- A defined input type
- A defined output type
- A described behavior
- A clear boundary from adjacent modules

Do not invent modules the spec doesn't describe. Do not merge modules the spec
describes as separate. When the spec is ambiguous about boundaries, note the ambiguity
— don't resolve it silently.

For each module, record:
- Name (match the spec's terminology exactly)
- Spec reference (section where it's described)
- Input type(s)
- Output type(s)
- External dependencies (network, database, LLM, filesystem)
- Internal dependencies (other modules it calls or requires)

### Step 2 — Map dependencies

For each module, identify what must exist before it can be built:
- **Data model dependencies:** if module A produces `TypeX` and module B consumes
  `TypeX`, module A (or at least `TypeX`) must exist before module B's tests can
  be written
- **Runtime dependencies:** if module B calls module A, module A must be implemented
  before module B's integration tests can pass
- **Scaffolding dependencies:** toolchain, config loading, test infrastructure, and
  data models come before any feature module

Note: for TDD, what matters for TEST ticket ordering is *type availability*, not
*implementation availability*. You can write tests for module B before module A is
implemented, as long as the types module B uses are defined.

### Step 3 — Assign phases

Group modules into phases based on dependency order:

**Phase 0 — Scaffolding** (usually complete after sprint zero)
Infrastructure that enables feature development: directory structure, toolchain,
CI, test framework, quality gate command, agent briefing doc, enforcement hooks.
If `audit-pre-impl` has passed, this phase is done. Note it as complete.

**Phase 1 — Foundation**
Data models and shared types. Config loading. Any pure-logic module with no
dependency on other feature modules. These have no external calls, can be tested
in complete isolation, and unblock everything that follows.

**Phase N — Feature modules**
Ordered by dependency. Each phase contains modules whose dependencies are all
satisfied by prior phases. Modules within a phase can be built in parallel.

**Final phase — Integration**
End-to-end wiring: the pipeline or orchestration layer, CLI entry point, CI
pipeline configuration.

### Step 4 — Flag pre-ticket decisions

Before ticket drafting can begin for any module, some decisions may need to be
resolved. Identify any spec ambiguities that would block ticket creation:

- Interface shapes that aren't fully specified
- Failure behaviors that are unspecified for a given module
- Ordering choices within a phase where the spec is silent

These become `[DECISION]` issues that must be resolved before `draft-tickets`
runs for the affected phase.

---

## Output Format

```
IMPLEMENTATION PHASE PLAN — [Project Name]
════════════════════════════════════════════════════════════
Spec: [path] | CLAUDE.md: [path] | Generated: [date]

PHASE 0 — SCAFFOLDING                              [COMPLETE / IN PROGRESS / NOT STARTED]
────────────────────────────────────────────────────────────
[If complete: "Verified by audit-pre-impl. No tickets needed."]
[If not complete: list what's missing]

PHASE 1 — FOUNDATION
────────────────────────────────────────────────────────────
Modules in this phase:

  1. [Module name]
     Spec: [section reference]
     Input:  [type]
     Output: [type]
     Why this phase: [no dependencies on other feature modules / defines shared types]
     External deps: [none / list]
     Internal deps: [none / list]

  2. [Module name]
     ...

Pre-ticket decisions needed before this phase:
  → [Decision description] — blocks [module name] ticket creation
  → None

PHASE 2 — [NAME DESCRIBING THIS PHASE]
────────────────────────────────────────────────────────────
Modules in this phase:

  3. [Module name]
     Spec: [section reference]
     Input:  [type]
     Output: [type]
     Why this phase: depends on [Phase 1 module] for [reason]
     External deps: [list]
     Internal deps: [Phase 1 modules it calls]

Pre-ticket decisions needed before this phase:
  → [Decision description]

[... additional phases ...]

FINAL PHASE — INTEGRATION
────────────────────────────────────────────────────────────
  N. [Pipeline orchestrator / CLI entry / integration layer]
     Spec: [section reference]
     Why last: wires together all prior phases; integration tests require all
               prior modules to be implemented

SUMMARY
────────────────────────────────────────────────────────────
Total modules identified: N
Total phases: N
Modules with pre-ticket decisions needed: N

AMBIGUITIES NOTED (did not resolve — flag for engineer):
  → [Description of spec ambiguity, spec section reference]

════════════════════════════════════════════════════════════
NEXT STEPS
  1. Review this plan and correct any module boundaries or phase assignments
  2. Resolve pre-ticket decisions (open [DECISION] issues for each)
  3. Run draft-tickets for Phase 1 once decisions are resolved
  4. Run audit-pre-impl if Phase 0 is not yet complete
════════════════════════════════════════════════════════════
```

---

## What Not to Do

- Do not resolve spec ambiguities about module boundaries — flag them
- Do not invent modules the spec doesn't describe
- Do not create tickets — that's `draft-tickets`
- Do not assign time estimates — this is a dependency plan, not a schedule
- Do not merge phases to make the plan look simpler — if two groups have a real
  dependency relationship, they belong in separate phases

---
name: draft-claudemd
description: >
  Draft an agent briefing document (CLAUDE.md or equivalent) from the project spec
  and existing toolchain configuration. Use when asked to "draft CLAUDE.md", "create
  the agent briefing doc", "write CLAUDE.md from the spec", or "set up CLAUDE.md".
  Produces a first draft covering all 7 sections of the briefing framework, with
  [NEEDS INPUT] markers where the spec doesn't provide enough detail. Run
  review-claudemd on the output before committing.
argument-hint: 'path to spec file (default: SPEC.md)'
---

# Draft Agent Briefing Document

Produce a first draft of the project's agent briefing document from the spec and
toolchain configuration. The output is a complete 7-section CLAUDE.md ready for
the engineer to fill gaps and run through `review-claudemd`.

**Direction:** extract from the spec; don't invent. Every section that can't be
derived from an existing artifact gets a `[NEEDS INPUT: <question>]` marker.

---

## Inputs

Gather in this order before drafting:

1. **SPEC.md** (or path provided) — primary source for all 7 sections
2. **Toolchain config** — `pyproject.toml`, `package.json`, `go.mod`, or equivalent
3. **Directory structure** — `find . -type f -name "*.py" | head -40` or equivalent,
   to understand module layout
4. **Existing CLAUDE.md** — if present, treat as a draft to improve, not replace
5. **Makefile / justfile / package.json scripts** — to identify quality gate commands

If SPEC.md is absent or below the 7.0 readiness score, stop and tell the engineer
to complete spec review (Chapter 1) before drafting the briefing doc.

---

## Drafting Process

Work through the 7 sections in order. For each:
1. Extract what the spec says
2. Derive what can be inferred from toolchain config and directory structure
3. Mark what cannot be determined with `[NEEDS INPUT: <specific question>]`

**Word budget:** target 1,500–2,000 words for the draft. Leave room for the
engineer to add project-specific content without hitting the 2,500-word limit.

**Rationale rule:** every behavioral rule (ALWAYS, NEVER, MUST, DO NOT) must have
a **Why:** line. Draft the rationale from the spec where possible. If the rationale
isn't in the spec, write `Why: [NEEDS INPUT: rationale for this rule]`.

---

## Section-by-Section Guidance

### Section 1 — Project Overview

Extract from the spec:
- What does this project do? (1 paragraph max)
- How does it run? (headless? UI? API?)
- What are the highest-priority constraints? (correctness, latency, cost, observability?)
- What is the authoritative source of truth? (the spec itself)

Include a compaction priority note — which sections should the agent preserve if
the context window forces compaction? Default: failure handling table, quality gates,
current task's spec reference. Adjust based on what's most critical for this project.

### Section 2 — Architecture

Extract from the spec's architecture or data flow section:
- Data flow diagram (ASCII if the spec has one; summarize if not)
- Key components and their responsibilities
- Stage boundaries and the data types that cross them
- Any critical design constraints (e.g., "never call X before Y")

If the spec has a data model section, summarize the key types and where they appear.

### Section 3 — Autonomy & Decision-Making

This section is the most important behavioral instruction in the document.

Extract from the spec or derive from project context:
- What decisions can the agent make independently? (naming, internal helpers,
  additional test cases, constants for clarity)
- What must be surfaced to a human? (spec ambiguities producing structurally different
  code, public interface changes, new dependencies, deviations from data flow)

Include the standard DECISION NEEDED format:
```
DECISION NEEDED: [one-line description]
Options:
  A) [option] — [consequence]
  B) [option] — [consequence]
Spec reference: [section]
Recommendation: [recommendation and why]
```

If the spec doesn't define the autonomy boundary:
`[NEEDS INPUT: What decisions should the agent make autonomously vs. surface to a human?]`

### Section 4 — Code Standards

Extract from toolchain config:
- Language and version (from pyproject.toml, package.json, go.mod)
- Type checking tool and strictness level (mypy, tsc, etc.)
- Linter and formatter (ruff, eslint, gofmt, etc.)
- Test framework (pytest, jest, go test, etc.)
- Package manager (uv, npm, go modules, etc.)

Derive from spec or project conventions:
- Naming conventions (if specified)
- Error handling pattern (if specified)
- Logging approach (if specified — structured? format?)
- Module/file structure conventions

For each style rule, include a Why: line derived from the spec's rationale, or flag
with `[NEEDS INPUT: rationale for this rule]`.

### Section 5 — Failure Handling

This is the most important behavioral contract. Extract directly from the spec's
failure handling or error handling section.

Format as a lookup table:

| Failure type | Required behavior |
|---|---|
| [failure from spec] | [required response from spec] |

Include:
- Exit code contract (if the project is a CLI or script)
- Any "cache safety" or idempotency rules
- Retry policies if specified

If the spec doesn't have a failure handling section:
`[NEEDS INPUT: For each external dependency (network, database, API), what should the agent do when it fails?]`

### Section 6 — Testing Standards

Extract from spec or project conventions:
- TDD or test-after workflow
- Test directory structure
- Required test types per module (unit, integration, contract)
- Mocking strategy (what gets mocked, what tooling)
- Quality gate commands (from Makefile/justfile/scripts)

If TDD is used, include explicitly:
> Tests are written BEFORE implementation. Every issue comes in a pair:
> `[TEST]` — write failing tests first. `[IMPL]` — implement until tests pass.
> Do not write implementation code until the test file exists and is failing.

### Section 7 — Quality Gates

The stopping condition for every task. Extract from spec or derive:
- The single quality gate command (e.g., `make check`) that runs lint + typecheck + tests
- What must pass before any task is considered done
- Any PR-specific requirements

Format as a checklist the agent verifies before closing a task.

---

## Output Format

Produce the full CLAUDE.md content, ready to write to the file. Use this structure:

```markdown
# CLAUDE.md — [Project Name] Agent Briefing

> **Who this file is for:** [agent tool name], and any engineer joining this project.
> **What it is:** The encoded contract between this codebase's standards and every
> agent session that contributes to it. Read this before writing a single line of code.

---

## 1. Project Overview
[content]

## 2. Architecture
[content]

## 3. Autonomy & Decision-Making
[content]

## 4. Code Standards
[content]

## 5. Failure Handling Convention
[content]

## 6. Testing Standards
[content]

## 7. Quality Gates
[content]
```

After the draft, output a summary:

```
DRAFT SUMMARY
─────────────────────────────────────────────
Word count (estimated): ~XXXX / 2500 limit
[NEEDS INPUT] markers: N
  - Section X: [question]
  - Section Y: [question]

Next step: fill the [NEEDS INPUT] gaps, then run review-claudemd to audit the result.
```

---

## What Not to Include

- Content written for human readers (new hire onboarding, architecture rationale for
  engineering leaders) — that belongs in `docs/`. The briefing doc is for the agent.
- Purely mechanical rules the linter already enforces (formatting whitespace, import
  ordering) — hooks catch these; don't waste instruction budget on them.
- Restatements of what the code already makes obvious — the agent can read the code.

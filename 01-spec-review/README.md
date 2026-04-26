# 01 — Spec Review

Review a spec before a single line of implementation code is written. Find ambiguity,
unstated assumptions, and non-obvious issues while they're still cheap to fix.

The core insight: most AI code failures trace back to a spec that was ambiguous or
incomplete. A single agent reading a PR will silently resolve ambiguity. Multiple
independent reviewers, each looking through a different lens, surface it.

---

## What's in this chapter

```
01-spec-review/
├── protocol/
│   ├── spec-reviewer-base-instructions.md           ← base for code-spec reviewers
│   ├── framework-spec-reviewer-base-instructions.md ← base for framework-spec reviewers
│   ├── spec-reviews-synthesis-agent.md              ← synthesizes all reviews + Decision Register
│   └── spec-revision-agent.md                       ← applies synthesis findings to the spec
├── personas/
│   ├── code-spec/        ← reviewers for software/code specs (via GitHub PR)
│   │   ├── architect.md
│   │   ├── skeptic.md
│   │   ├── security.md
│   │   ├── oss-adoptability.md
│   │   ├── scope.md
│   │   ├── domain-expert.md
│   │   ├── legal-compliance.md
│   │   ├── operator.md
│   │   ├── test-strategy.md
│   │   └── ambiguity-auditor.md
│   └── framework-spec/   ← reviewers for framework/content specs (file-based)
│       ├── target-audience-skeptic.md
│       ├── practitioner-executor.md
│       ├── consistency-auditor.md
│       ├── build-agent.md
│       └── stakeholder-alignment.md
├── workflows/
│   ├── spec-review.md           ← code-spec review: 9 personas + ambiguity auditor
│   ├── framework-spec-review.md ← framework-spec review: 5 personas
│   └── spec-scorer-workflow.md  ← score a spec before review
├── skills/
│   └── score-spec/SKILL.md      ← Claude Code skill for scoring a spec
├── evaluation/
│   ├── scorecard-10-reviewer.md ← scorecard for 10-reviewer runs
│   ├── scorecard-9-reviewer.md  ← scorecard for 9-reviewer runs
│   └── spec-scorer-scorecard.md ← scorecard for the spec scorer
├── docs/
│   ├── multi-agent-spec-review-writeup.md ← how the protocol works and why
│   └── nine-reviewer-agents-findings.md   ← example findings from a real run
└── examples/
    └── ai-radar-spec-review/    ← end-to-end example from a real project
```

---

## Quickstart — Code Spec Review

**Prerequisites:** Claude Code CLI, GitHub CLI (`gh`), a PR open on GitHub.

**Step 1:** Start a fresh Claude Code session for each reviewer persona.

**Step 2:** Build the prompt by combining two files:
```
protocol/spec-reviewer-base-instructions.md  +  personas/code-spec/architect.md
```
Copy both into your Claude Code session (base first, then persona). Replace `$PR_NUMBER`.

**Step 3:** Set model and effort:
```
/model opus
/effort high
```

**Step 4:** Repeat for each persona in a fresh session:

| Persona | Model | Effort |
|---|---|---|
| architect | Opus | high |
| skeptic | Opus | high |
| security | Sonnet | medium |
| oss-adoptability | Sonnet | medium |
| scope | Sonnet | medium |
| domain-expert | Opus | high |
| legal-compliance | Sonnet | medium |
| operator | Sonnet | medium |
| test-strategy | Sonnet | medium |

**Step 5:** After all nine have posted, run the **Ambiguity Auditor** (it reads the other
reviews to find implementation forks that any single reviewer would miss):
```
personas/code-spec/ambiguity-auditor.md — Opus, high effort
```

**Step 6:** Run the **Synthesis Agent** last. It reads all ten reviews and posts a
prioritized action list with a Decision Register:
```
protocol/spec-reviews-synthesis-agent.md — Opus, high effort
```

**Step 7:** Work from the Synthesis Agent's output. Resolve `[HUMAN DECISION]` items
yourself and document your decisions in the PR thread.

**Step 8:** Run the **Spec Revision Agent** to apply all synthesis findings to the spec:
```
protocol/spec-revision-agent.md — Opus, high effort
```
You review the diff and merge.

### Isolation matters

Each of the nine independent reviewer sessions must be isolated — start a fresh
Claude Code session for each persona. The Ambiguity Auditor is the exception: it runs
after all nine and explicitly reads their output. See `workflows/spec-review.md` for
the full protocol.

---

## Comment Labels

| Label | Meaning | Author should... |
|---|---|---|
| `[BLOCKING]` | Something is wrong | Fix before merge |
| `[AMBIGUITY]` | Something is undefined | Make a decision, document it |
| `[FALSE PRECISION]` | Looks decided but isn't | Confirm or mark TBD |
| `[SUGGESTION]` | Worth improving | Consider and decide |
| `[NIT]` | Minor polish | Fix if easy |

`[AMBIGUITY]` and `[FALSE PRECISION]` are treated as merge-blocking.

---

## Evaluating the Protocol

After running a review, fill in the appropriate scorecard in `evaluation/`. It tracks
per-comment signal quality, persona differentiation, conflict rate, and Protocol
Effectiveness Score (PES). Don't skip the scorecard for the first few runs — it's
what turns this into a learnable workflow.

Use `scorecard-10-reviewer.md` for a full 10-persona run, `scorecard-9-reviewer.md`
for 9-persona runs, and `spec-scorer-scorecard.md` for the spec scorer.

---

## Background and Examples

`docs/multi-agent-spec-review-writeup.md` explains how the protocol works and the
design principles behind it — useful when onboarding a team to this workflow.

`examples/ai-radar-spec-review/` contains the full artifact trail from a real spec
review run: original spec, per-reviewer findings, synthesis, and before/after scores.

---

## Framework Spec Review

For reviewing framework or content specs (as opposed to code specs), use the five-persona
workflow in `workflows/framework-spec-review.md`. Same isolation protocol, same comment
labels, fewer personas.

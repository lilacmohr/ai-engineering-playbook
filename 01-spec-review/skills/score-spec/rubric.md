# Spec Scoring Rubric — Detailed Criteria

Reference file for the `score-spec` skill. Read when you need
per-dimension scoring guidance beyond the summary in SKILL.md.

---

## Dimension 1: Unambiguity (Weight: 25%)

**Core question:** Could two independent agents read this spec section and make
the same implementation decision?

| Score | Criteria |
|---|---|
| 9–10 | Every behavioral decision is specified. Algorithm choices, field names, filter logic, output schema — all stated explicitly. Two agents produce nearly identical code. |
| 7–8 | Major behaviors specified. Minor details (exact error messages, field ordering) left to implementation. Agents produce compatible code with cosmetic differences. |
| 5–6 | Core flows described but key algorithms vague. "Filter by relevance" without defining relevance. Agents make different structural choices that affect integration. |
| 3–4 | Behavior implied but not stated. Agents must infer from examples or domain knowledge. Significant divergence likely. |
| 1–2 | Requirements are goals, not specifications. "Make it fast" level of precision. |

**Common ambiguity patterns to flag:**
- "optionally" without specifying the trigger condition
- "etc." or "and so on" in lists of specific things
- Verbs without subjects ("articles are filtered" — by what?)
- Thresholds stated as approximations ("around 200 words")
- Process steps that reference undefined terms

---

## Dimension 2: Completeness (Weight: 20%)

**Core question:** Are all modules, data models, failure modes, and
cross-cutting concerns described?

| Score | Criteria |
|---|---|
| 9–10 | All modules described. All data models fully typed. All failure modes covered. Cross-cutting concerns (logging, auth, testing, CI) documented. |
| 7–8 | All major modules described. Data models defined (may have minor gaps). Most failure modes covered. At least one cross-cutting concern missing. |
| 5–6 | Core flow described. Some modules absent or implied. Data models referenced but not defined. Failure handling sparse. |
| 3–4 | Happy path only. Multiple modules absent. No data model definitions. No failure handling. |
| 1–2 | High-level description only. Not sufficient to identify the modules needed. |

**Common completeness gaps to flag:**
- Data models referenced by name but fields not specified
- "Error handling TBD" or similar deferrals without a plan
- Cross-cutting concerns (logging format, auth flow) assumed but not described
- CLI or API surface not documented
- CI/deployment not mentioned for a headless system

---

## Dimension 3: Consistency (Weight: 15%)

**Core question:** Do all sections of the spec agree with each other?

| Score | Criteria |
|---|---|
| 9–10 | No contradictions found. All sections describe the same system. Config matches behavior. Data flow diagram matches prose. |
| 7–8 | Minor terminology inconsistencies (different names for same concept). No structural contradictions. |
| 5–6 | One or more behavioral contradictions between sections. Different sections describe different pipeline orders or data shapes. |
| 3–4 | Multiple contradictions. Config, data flow, and prose describe different systems. |
| 1–2 | Spec is internally self-contradictory in ways that cannot be resolved without author clarification. |

**Common consistency issues to flag:**
- Section A describes step X before Y; section B describes Y before X
- Config key appears in example but not in config schema description
- Data model in section 3 has different fields than data model in section 5
- Dependency list contradicts technology choices in implementation section

---

## Dimension 4: Verifiability (Weight: 15%)

**Core question:** Can each requirement be turned into a passing/failing test
without ambiguity about what "pass" means?

| Score | Criteria |
|---|---|
| 9–10 | Explicit acceptance criteria per module. Golden-set tests specified for LLM outputs. Exit codes defined. Threshold values stated (not approximated). |
| 7–8 | Most behaviors testable. Exit codes defined. Some thresholds approximate but close enough. LLM output schema specified (structural test possible). |
| 5–6 | Core behaviors testable. No acceptance criteria stated. LLM quality unverifiable beyond schema. Several "should" statements with no measurable bar. |
| 3–4 | Few behaviors verifiable. Most requirements are qualitative goals. No test strategy implied. |
| 1–2 | Requirements are not testable as stated. No observable outcomes defined. |

**Verifiability-specific flags:**
- "Should be fast" without a latency target
- LLM output described qualitatively ("insightful summaries") with no schema
- "Approximately" for any threshold that a test must check
- No exit code contract for CLI tools
- Acceptance criteria stated for system but not per-module

---

## Dimension 5: Implementation Guidance (Weight: 10%)

**Core question:** Does the spec tell agents *how* to work, not just *what* to build?

| Score | Criteria |
|---|---|
| 9–10 | Technology choices stated with rationale. Error handling convention specified. Logging format specified. Testing strategy described. Prompt templates included (for LLM systems). |
| 7–8 | Technology choices stated. Error handling and logging described. Testing mentioned. One major cross-cutting convention missing. |
| 5–6 | Technology choices implied or stated without rationale. No error handling convention. No logging format. Testing not described. |
| 3–4 | No implementation guidance. What to build is described; how to build it is not. |
| 1–2 | Implementation entirely left to the agent. |

---

## Dimension 6: Forward Traceability (Weight: 5%)

**Core question:** Can a GitHub Issue be written that references a specific
spec section, and would that be sufficient context for an agent to implement it?

| Score | Criteria |
|---|---|
| 9–10 | Requirements numbered. Each module maps to a specific section. Tickets can reference §X.Y.Z. |
| 7–8 | Sections clearly titled. Module-to-section mapping is clear even without numbered requirements. |
| 5–6 | Sections exist but boundaries are unclear. A ticket referencing "section 3" would cover too much. |
| 3–4 | No clear section structure. Tickets must quote prose rather than reference sections. |
| 1–2 | No structure. Cannot trace implementation back to spec. |

---

## Dimension 7: Singularity (Weight: 5%)

**Core question:** Does each requirement describe exactly one thing?

| Score | Criteria |
|---|---|
| 9–10 | Every requirement is atomic. No "and" in requirement statements that could be split. |
| 7–8 | Most requirements atomic. A few compound requirements exist but are low-risk (both parts clearly defined). |
| 5–6 | Several compound requirements. "Implement X, Y, and Z" as one line. Risk of partial compliance. |
| 3–4 | Requirements routinely bundle multiple behaviors. Hard to know when one is "done." |
| 1–2 | Requirements are capabilities, not behaviors. Entirely compound. |

---

## Dimension 8: Failure Mode Coverage (Weight: 3%)

**Core question:** Is the unhappy path as specified as the happy path?

| Score | Criteria |
|---|---|
| 9–10 | Every external dependency has failure handling specified. Exit codes defined. Partial failure behavior described. Recovery path documented. |
| 7–8 | Major failure modes covered. Exit codes defined. One or two edge cases missing. |
| 5–6 | Some failure modes described. No exit code contract. Partial failure not addressed. |
| 3–4 | Happy path only. Failure handling mentioned but not specified. |
| 1–2 | No failure handling. System assumed to always succeed. |

---

## Dimension 9: Interface Contracts (Weight: 2%)

**Core question:** Are inputs, outputs, and types defined at every stage boundary?

| Score | Criteria |
|---|---|
| 9–10 | All data models fully typed with field names and types. Pipeline stage boundaries explicit. Empty input behavior specified. Error types defined. |
| 7–8 | Data models defined. Stage boundaries clear. Some edge case behavior (empty input) not specified. |
| 5–6 | Data models described in prose but not formally typed. Stage boundaries implied. |
| 3–4 | Data referred to by concept, not structure. Agents would invent their own data shapes. |
| 1–2 | No interface contracts. Each module is a black box. |

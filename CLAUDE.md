# ai-review-protocols

This repo contains a structured multi-agent PR review framework.

## Key files
- `protocol/base-instructions.md` — start here for any review session
- `personas/` — pick one persona per session
- `workflows/spec-review.md` — full workflow instructions
- `evaluation/scorecard.md` — fill in after each review run

## How to run a review
Combine base-instructions.md + a persona file, replace $PR_NUMBER, 
set model and effort, paste into a fresh Claude Code session.

## You are NOT an implementer in this repo
Do not write application code here. This repo contains prompts and 
documentation only.

# /review-pr

<!--
CLAUDE CODE SLASH COMMAND
Place this file at .claude/commands/review-pr.md in your repo.
Usage: /review-pr <PR_NUMBER> <PERSONA>

Valid personas: architect, skeptic, security, oss-adoptability, scope, synthesis

This command is a STUB — it documents the intended Level 2 behavior.
At Level 1, combine base-instructions.md + persona file manually.

TODO: When upgrading to Level 2, replace the instructions below with 
dynamic prompt construction that reads the base + persona files and 
injects $PR_NUMBER automatically.
-->

## Usage

```
/review-pr $ARGUMENTS
```

Where `$ARGUMENTS` is: `<PR_NUMBER> <PERSONA>`

Example: `/review-pr 42 architect`

## Current Behavior (Level 1 Stub)

This command is not yet fully automated. To run a reviewer:

1. Open `protocol/base-instructions.md`
2. Open `personas/<PERSONA>.md`
3. Combine both files (base first, persona second)
4. Replace `$PR_NUMBER` with your PR number
5. Set model and effort per `workflows/spec-review.md`
6. Paste into a fresh Claude Code session

## Intended Level 2 Behavior

When fully implemented, this command will:

1. Accept PR number and persona name as arguments
2. Run `gh pr diff <PR_NUMBER>` to fetch the diff
3. Load `protocol/base-instructions.md`
4. Load `personas/<PERSONA>.md`
5. Inject PR number and diff into the combined prompt
6. Set model and effort based on persona defaults
7. Post the structured review comment automatically

## Upgrade Path

To upgrade this stub to a working Level 2 command:
- Read the Claude Code slash command documentation
- Replace this file's body with dynamic prompt construction logic
- Test with one persona before rolling out all five
- Update `workflows/spec-review.md` to reflect Level 2 instructions

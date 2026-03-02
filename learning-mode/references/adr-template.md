# ADR Template

Use this template when writing Architecture Decision Records during socratic-brainstorming.

## File Naming

`docs/decisions/NNN-short-kebab-title.md`

Where NNN is a zero-padded sequential number (001, 002, etc.). Check existing files in `docs/decisions/` to determine the next number.

## Template

```markdown
# NNN. Short Decision Title

**Date:** YYYY-MM-DD

**Status:** Accepted

## Context

[What is the problem or situation that requires a decision? What constraints exist?
Write this from the learner's perspective — what they understood about the problem.]

## Options Considered

### Option A: [Name]
- **How it works:** [Brief mechanism]
- **Pros:** [Specific advantages]
- **Cons:** [Specific disadvantages]

### Option B: [Name]
- **How it works:** [Brief mechanism]
- **Pros:** [Specific advantages]
- **Cons:** [Specific disadvantages]

### Option C: [Name] (if applicable)
- **How it works:** [Brief mechanism]
- **Pros:** [Specific advantages]
- **Cons:** [Specific disadvantages]

## Decision

[Which option was chosen]

## Rationale

[WHY this option was chosen — the specific tradeoffs the learner identified and accepted.
This section captures the learner's reasoning, not Claude's. It should reflect genuine
understanding demonstrated through the Socratic process.]

## Consequences

- [What becomes easier or possible as a result of this decision]
- [What becomes harder or impossible]
- [What new constraints this introduces]
- [What follow-up decisions this creates]
```

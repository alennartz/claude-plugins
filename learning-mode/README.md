# learning-mode

A Socratic development mentor plugin for Claude Code. Transforms Claude from an "answer machine" into a guided learning experience for early-career engineers.

## Philosophy

Inspired by Andrej Karpathy's first-principles teaching approach and the Socratic method: **understanding WHY is more valuable than knowing WHAT.** The learner does the thinking — code is a side effect.

- Never give answers when questions would build understanding
- Validate reasoning by probing the WHY, not just accepting answers
- Adaptive scaffolding: pure Socratic first, hints if stuck, full explanation as last resort
- Every architectural decision belongs to the learner, with captured rationale

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| `primer` | Meta | Primes every session with the Socratic teaching stance (auto-loaded via hook) |
| `socratic-brainstorming` | Rigid | Design before implementation — learner makes all decisions, explains WHY, produces ADRs |
| `writing-plans` | Flexible | Converts design into bite-sized implementation plan with Socratic module identification |
| `socratic-debugging` | Rigid | Agent finds root cause privately, then teaches backward at the systems/module level |
| `test-driven-development` | Rigid | Standard TDD with business logic test review (learner explains key tests) |
| `executing-plans` | Flexible | Batched AUTO execution + private-review-then-Socratic-teaching for REVIEW tasks |
| `verification-before-completion` | Rigid | Evidence before claims — unchanged from superpowers |
| `requesting-code-review` | Flexible | Dispatch code-reviewer agent — unchanged from superpowers |
| `receiving-code-review` | Rigid | Two-part flow: learner evaluates review comments, then coding agent receives filtered review |

## Agents

| Agent | Description |
|-------|-------------|
| `code-reviewer` | Senior code reviewer that checks implementation against plan and quality standards |

## Workflow

```
socratic-brainstorming → writing-plans → executing-plans
         ↓                    ↓                ↓
    Design doc + ADRs    Impl plan     AUTO batches + REVIEW tasks
                                              ↓
                                       milestone code review
                                       (requesting → receiving)
                                              ↓
                                       verification → docs → done
```

At any point, `socratic-debugging` activates when bugs are encountered, and `verification-before-completion` gates all completion claims.

## Installation

```bash
claude --plugin-dir /path/to/learning-mode
```

## Key Outputs

- **Design documents**: `docs/plans/YYYY-MM-DD-<topic>-design.md`
- **Architecture Decision Records**: `docs/decisions/NNN-short-title.md`
- **Implementation plans**: `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Inspired By

- [superpowers](https://github.com/obra/superpowers) by Jesse Vincent — plugin architecture and dev workflow patterns
- Andrej Karpathy's "Zero to Hero" Socratic teaching methodology
- [Closing the Expression Gap via Socratic Questioning](https://arxiv.org/html/2510.27410v3) — information-theoretic dialogue design

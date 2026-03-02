# Claude Plugins Marketplace

A collection of plugins for Claude Code CLI.

## Installation

### From the terminal

Add this repository as a marketplace in Claude Code:

```bash
claude plugins:add-marketplace https://github.com/alennartz/claude-plugins
```

Then install individual plugins:

```bash
claude plugins:install playwright-ui-tester
```

### From within Claude Code

You can also manage plugins using slash commands inside a Claude Code session:

```
/plugins:add-marketplace https://github.com/alennartz/claude-plugins
```

Then install plugins with:

```
/plugins:install playwright-ui-tester
```

## Available Plugins

### playwright-ui-tester

A Playwright-based UI testing skill that returns concise, actionable results with code paths.

**Features:**
- Concise pass/fail tables instead of verbose MCP output
- Automatic code path discovery when bugs are found
- Auto-delegation to general-purpose agent with MCP tool access

**Prerequisites:**
- Playwright MCP server configured in your `.mcp.json`
- Browser installed (`npx playwright install chromium`)

**Usage:**

Simply ask Claude to test UI:
- "Check if the graph displays correctly"
- "Verify the form submission works"
- "Test the login flow"

See the [plugin README](./playwright-ui-tester/README.md) for detailed usage.

### learning-mode

A Socratic development mentor for early-career engineers. Transforms Claude from an answer machine into a guided learning experience where the learner makes every design decision and defends their reasoning.

**Features:**
- Socratic brainstorming — design through guided discovery, not dictation
- Architecture Decision Records capturing the learner's reasoning
- Implementation plans with autonomous REVIEW/AUTO classification
- Batched execution for boilerplate, private-review-then-Socratic-teaching for high-risk tasks
- TDD with business logic test review
- Socratic debugging — private investigation, then teach backward
- Code review where the learner evaluates feedback critically

**Inspired by:** [superpowers](https://github.com/obra/superpowers) by Jesse Vincent and Karpathy's Socratic teaching methodology.

See the [plugin README](./learning-mode/README.md) for detailed usage.

## License

MIT

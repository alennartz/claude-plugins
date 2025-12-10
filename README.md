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

A Playwright-based UI testing agent that returns concise, actionable results with code paths.

**Features:**
- Concise pass/fail tables instead of verbose MCP output
- Automatic code path discovery when bugs are found
- Auto-delegation via companion skill
- Model flexible (inherits parent, overridable per-test)

**Prerequisites:**
- Playwright MCP server configured in your `.mcp.json`
- Browser installed (`npx playwright install chromium`)

**Usage:**

Simply ask Claude to test UI:
- "Check if the graph displays correctly"
- "Verify the form submission works"
- "Test the login flow"

See the [plugin README](./playwright-ui-tester/README.md) for detailed usage.

## License

MIT

# Playwright UI Tester Plugin

A Claude Code plugin providing a skill for Playwright-based UI testing that returns concise, actionable results instead of verbose MCP output.

## Features

- **Concise Results**: Returns pass/fail tables, not raw page snapshots
- **Code Path Discovery**: Identifies relevant source files when bugs are found
- **Auto-Delegation**: Skill automatically delegates UI testing requests to a general-purpose agent

## Installation

The plugin is installed at `~/.claude/plugins/playwright-ui-tester/`.

Restart Claude Code after installation for changes to take effect.

## Prerequisites

- Playwright MCP server must be configured in your `.mcp.json`
- Browser must be installed (`npx playwright install chromium`)

## Usage

Simply ask Claude to test UI:
- "Check if the graph displays correctly"
- "Verify the form submission works"
- "Test the login flow"

The `ui-testing` skill will automatically delegate to a `general-purpose` agent with MCP tool access.

## Output Format

The agent returns structured results:

```markdown
### Test Results

| Test | Status | Details |
|------|--------|---------|
| Login button visible | ✅ PASS | Button found with correct text |
| Form validation | ❌ FAIL | Email field accepts invalid format |

### Issues Found

- **Severity**: Warning
- **Description**: Email validation missing
- **Location**: Login form email input
- **Code Path**: `src/components/LoginForm.tsx:45-52`
- **Suggested Fix**: Add email regex validation

### Code Files to Review

- `src/components/LoginForm.tsx:45-52` - Email input validation
```

## Design Principles

### What Gets Returned
- Pass/fail status for each assertion
- Bug descriptions with severity
- Relevant file paths with line numbers
- Screenshot paths (failures only)
- Suggested fixes when obvious

### What Gets Filtered Out
- Full accessibility tree dumps
- Success screenshots
- Unrelated console warnings
- Network request logs
- Step-by-step navigation confirmations

## Plugin Structure

```
playwright-ui-tester/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── skills/
│   └── ui-testing/
│       └── SKILL.md         # Auto-delegation skill
└── README.md                # This file
```

## License

MIT

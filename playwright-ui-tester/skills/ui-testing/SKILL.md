---
name: ui-testing
description: UI testing with Playwright MCP. Use when the user asks to test UI, verify visual changes, check for bugs in the browser, or validate user flows. ALWAYS delegates to the ui-tester agent for actual browser interactions.
---

# UI Testing with Playwright

When the user asks to test UI or verify browser behavior, you MUST delegate to the `ui-tester` agent using the Task tool.

## Auto-Delegation Rule

If the user's request involves ANY of these, immediately delegate:
- "test the UI"
- "verify in the browser"
- "check if [something] displays correctly"
- "see if [feature] works"
- "look at the app"
- "visual verification"
- "UI test"
- "playwright test"
- "check the webapp"
- "verify the changes in browser"

## How to Delegate

```
Task(
  subagent_type="ui-tester",
  prompt="""
    Test [URL]:

    ## Assertions
    1. [derived from user request]
    2. [derived from user request]

    ## Expected Behavior
    - [what correct behavior looks like]

    ## If Issues Found
    - Search codebase for relevant component files
    - Include file paths with line numbers in response
  """
)
```

## Model Selection

The agent inherits your current model by default. For faster/cheaper tests:
```
Task(
  subagent_type="ui-tester",
  model="haiku",
  prompt="..."
)
```

## Do NOT

- Run Playwright MCP tools directly from the primary agent (bloats context)
- Return verbose page snapshots to the user
- Take screenshots on every navigation step
- Include raw accessibility trees in conversation

## After Receiving Results

The ui-tester agent will return:
- Pass/fail table
- Issue descriptions with file paths
- Code files to review

Use the provided file paths to proceed with fixes without additional code searches.

## Example Delegation

User: "Check if the graph shows the correct edge labels"

You should respond:
```
Task(
  subagent_type="ui-tester",
  prompt="""
    Test http://localhost:5173:

    ## Assertions
    1. Navigate to Explorer tab
    2. Click on a trait in the tree (e.g., cargo-carrier)
    3. Verify edge labels show target type suffix (e.g., "has state", "raises event" not just "has", "raises")
    4. Verify icons are vertically centered in graph nodes

    ## Expected Behavior
    - Edge labels should include the target node type for clarity
    - Icons should be at vertical center (50%) not offset

    ## If Issues Found
    - Search for graph styling code
    - Include file paths in response
  """
)
```

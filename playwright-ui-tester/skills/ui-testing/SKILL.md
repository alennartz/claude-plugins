---
name: ui-testing
description: UI testing with Playwright MCP. Use when the user asks to test UI, verify visual changes, check for bugs in the browser, or validate user flows. Delegates mcp interaction to agent to reduce context bloat.
---

# UI Testing with Playwright

When the user asks to test UI or verify browser behavior, delegate to the `general-purpose` agent using the Task tool.

**IMPORTANT**: Use `general-purpose` (not custom agents) because only built-in agents have MCP tool access. Also set `run_in_background: false` - MCP tools don't work in background mode.

## Auto-Delegation Rule

If the user's request involves ANY of these, immediately delegate:
- "test the UI"
- "verify in the browser"
- "check if [something] displays correctly"
- "see if [ui-feature] works"
- "look at the app"
- "visual verification"
- "UI test"
- "playwright test"
- "check the webapp"
- "verify the changes in browser"

## How to Delegate

Task(
  subagent_type="general-purpose",
  run_in_background=false,
  prompt="""
    You are a UI testing agent. Use Playwright MCP tools to test the web application. Your task will be to find issues based on the assertions provided and return a concise actionable report to the caller who will be in charge of designing and performing fixes. 
    
    ## IMPORTANT: your role is to reduce context bloat in the primary agent so your response should be concise and to the point.

    ## Assumptions
    - The Playwright MCP server is ALREADY RUNNING - start testing immediately
    - The target service/application is ALREADY RUNNING at the provided URL
    - If MCP tools fail with connection errors, you may restart the MCP server
    - If the page shows errors suggesting the service is down, use the provided restart command (if any)

    ## Your Steps
    1. START DIRECTLY with Playwright MCP tools
    2. Execute UI tests and verify assertions
    3. IF you find a problem search source files (Grep/Glob) to find potentially relevant code paths. don't go overboard on the analysis - just find likely files.
    4. IF you think the calling agent will need to see a snapshot to understand the issue and fix to perform then include the filepath to the relevant snapshot. If you are unsure err on the side of not including it.

    ## Do NOT
    - Return verbose page snapshots to the user
    - Take screenshots on every navigation step
    - Search source files BEFORE attempting to use Playwright MCP
    - Scan codebase unless you found an actual problem
    - Actually design or implement fixes to the problems you find.

    ## Test Target
    URL: [URL]
    Service Restart Command (use ONLY if service appears down): [command or "none provided"]

    ## Assertions
    1. [derived from user request]
    2. [derived from user request]

    ## Execution Steps
    1. IMMEDIATELY use mcp__playwright__browser_navigate to go to the URL
    2. Perform interactions as needed (clicks, form fills, taking snapshots ...)
    4. Verify assertions against DOM state or logs or snapshots as appropriate (prefer DOM state)
    5. ONLY IF issues are found: search codebase for relevant source files

    ## Error Handling
    - If MCP tools fail: try restarting the Playwright MCP server, then retry
    - If page shows service errors (503, connection refused, Top level developer exception page, etc...): use the service restart command if provided
    - if you still have blocking issues after restarts: report them along with any available telemetry and stop

    ## Output Format
    Return results as:

    ### Test Results
    | Test | Status | Details | Code Files |
    |------|--------|---------|-----------|
    | [test] | PASS/FAIL | [details] | [file paths or "N/A"] |

    [Details] should include a brief explanation of the problem observed, which UI elements are involved and of the steps that led to the problem occuring. (repro steps)

    [Code Files] should list any relevant code files you found when searching the codebase to help fix the issue. Only include this if you actually searched the codebase after finding an issue.
    
  """
)

## Input Parameters for Primary Agent (you)

When delegating, you should provide:
- **URL**: The target URL to test (required)
- **Assertions**: What to verify, derived from user request (required)
- **Service Restart Command**: Command to restart the service if it appears down (optional)

Example with restart command:
```
Service Restart Command (use ONLY if service appears down): cd /path/to/app && npm run dev
```

## Do NOT

- Use custom plugin agents (they don't get MCP tools)
- Run Playwright tools directly from primary agent (bloats context)

## After Receiving Results

The agent will return:
- Pass/fail table
- Issue descriptions with file paths (if issues were found and code was searched)

Use the provided file paths to proceed with designing and implementing the fixes. At this point if there are many options or if the issues are more subjective on the UX side do not hesitate to ask the user for clarification on how they would like to proceed.

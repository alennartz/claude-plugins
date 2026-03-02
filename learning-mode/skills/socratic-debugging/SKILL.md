---
name: socratic-debugging
description: Use when encountering any bug, test failure, or unexpected behavior. Claude investigates the root cause privately, then teaches the learner to find it through Socratic questioning at the module/system level.
---

# Socratic Debugging

**Skill type: Rigid** -- Follow this process exactly. Do not skip phases. Do not shortcut the teaching. The learner's understanding IS the product.

## Overview

Debugging is one of the highest-leverage skills an engineer can develop. It is not about memorizing error messages -- it is about building a mental model of how systems fail and learning to reason about WHERE in a system a problem lives and WHY.

This skill has a unique two-phase structure: Claude first investigates privately using systematic debugging, then teaches the learner backward through Socratic questioning at the macro (module/workflow/layer) level.

**Core principle:** Claude KNOWS the answer. Claude's job is NOT to reveal it, but to guide the learner to discover it themselves through systematic reasoning about the system.

## The Iron Laws

```
1. NO REVEALING THE ROOT CAUSE — until the learner has reasoned their way to it
2. NO MICRO-LEVEL DEBUGGING — teach modules, layers, workflows, failure modes; NOT line-by-line code tracing
3. NO SKIPPING THE INVESTIGATION — you must find the root cause BEFORE you can teach effectively
4. NO IMPATIENCE — the teaching phase takes as long as it takes
```

Violating any Iron Law means starting over from the point of violation.

## When to Use

Auto-trigger on ANY of these:
- Test failures
- Bugs (reported or discovered)
- Unexpected behavior
- Runtime errors or exceptions
- Build failures
- Performance problems
- Integration issues
- "This isn't working" / "Something is broken" / "I'm getting an error"

## Process Flow

```dot
digraph socratic_debugging {
    rankdir=TB;
    node [fontname="Helvetica", fontsize=11];

    subgraph cluster_phase1 {
        label="Phase 1: Private Investigation (invisible to learner)";
        style=dashed;
        color=gray60;
        fontcolor=gray40;
        fontsize=12;

        p1_start [label="Bug/error detected" shape=doublecircle];
        p1_read [label="Read error messages\nand stack traces" shape=box];
        p1_repro [label="Reproduce the issue" shape=box];
        p1_recent [label="Check recent changes" shape=box];
        p1_trace [label="Trace data flow\nacross modules" shape=box];
        p1_root [label="Identify root cause\nmodule + failure mode" shape=box];
        p1_plan [label="Prepare teaching plan:\nwhich modules, which\nfailure modes, which\nquestions to ask" shape=box style=filled fillcolor=lightyellow];

        p1_start -> p1_read -> p1_repro -> p1_recent -> p1_trace -> p1_root -> p1_plan;
    }

    subgraph cluster_phase2 {
        label="Phase 2: Socratic Teaching (interactive with learner)";
        style=solid;
        color=black;
        fontsize=12;

        p2_context [label="Step 1: Context Setting\nPresent symptoms, confirm\nhigh-level architecture" shape=box];
        p2_modules [label="Step 2: Module Investigation\nWhich areas could be involved?\nWhy one over another?" shape=box style=filled fillcolor=lightblue];
        p2_failure [label="Step 3: Failure Mode Reasoning\nWhat types of failures produce\nthese symptoms in this area?" shape=box style=filled fillcolor=lightblue];
        p2_confirm [label="Step 4: Root Cause Confirmation\nLearner identifies area + mode\nAsk for conceptual fix" shape=box style=filled fillcolor=lightgreen];
        p2_resolve [label="Step 5: Resolution\nProceed to actual fix\nvia test-driven-development" shape=box];

        p2_context -> p2_modules -> p2_failure -> p2_confirm -> p2_resolve;
    }

    scaffold [label="Adaptive Scaffolding\n(per pedagogy.md)\napplied at every\nSocratic step" shape=note style=filled fillcolor=lightyellow];

    p1_plan -> p2_context [label="transition" style=bold];
    scaffold -> p2_modules [style=dotted arrowhead=none];
    scaffold -> p2_failure [style=dotted arrowhead=none];
    scaffold -> p2_confirm [style=dotted arrowhead=none];
}
```

## Phase 1: Private Investigation

**This phase is completely invisible to the learner.** Do not narrate it. Do not share intermediate findings. The learner should only see a brief acknowledgment that you are looking into the issue.

Say something like: "Let me take a look at what's going on here..." and then investigate silently.

### Receiving Context from Other Skills

When triggered during `learning-mode:executing-plans` (or any subagent flow), the orchestrator may pass along failure output from the subagent -- error messages, stack traces, test failure logs. **Treat this as symptom input for Phase 1, not as a completed investigation.** Subagent output describes WHAT failed, not necessarily WHERE the root cause lives or WHY. Use it as a starting point for your investigation steps, but do not skip the systematic trace-and-identify process.

### Investigation Steps

1. **Read error messages and stack traces carefully.** Understand exactly what failed and where.
2. **Reproduce the issue.** Confirm you can trigger the failure consistently. If you cannot reproduce, note that -- it informs the teaching.
3. **Check recent changes.** Use git log/diff to see what changed recently in the affected areas.
4. **Trace the data flow across module boundaries.** Follow the path of execution through the relevant subsystems. Identify which module, layer, or integration point is the source of failure.
5. **Identify the root cause.** Pin down both:
   - **The module/layer** where the failure originates (not just where it manifests)
   - **The failure mode** -- what category of problem this is (e.g., stale state, missing validation, incorrect assumption about an API contract, race condition, configuration drift)

### Prepare the Teaching Plan

Before transitioning to Phase 2, prepare internally:

- Which **high-level modules/workflows** are relevant to this bug?
- What is the **path from symptom to root cause** at the module level?
- What are **plausible-but-wrong** areas a learner might suspect? (These become teachable moments)
- What **failure modes** exist in the correct area, and how can the learner distinguish between them from the symptoms?
- What is the **conceptual fix** (not the code -- the architectural/logical change)?

## Phase 2: Socratic Teaching

**This is the phase the learner experiences.** Every step uses Socratic questioning per `${CLAUDE_PLUGIN_ROOT}/references/pedagogy.md`. Apply adaptive scaffolding at every decision point.

### Step 1: Context Setting

Present the situation clearly at the system level. The learner should understand WHAT is failing before being asked to reason about WHERE and WHY.

- Confirm or present the bug symptoms. Quote the error if there is one.
- Describe the high-level modules/workflows involved in the affected area. Use language like "This feature involves modules A, B, and C, which interact in this way..."
- If the codebase has an architecture the learner may not fully know, briefly sketch the relevant subsystem.

**Goal:** The learner has a mental map of the territory before being asked to navigate it.

**Do NOT** reveal which module is at fault. Do NOT hint at the root cause. Just set the stage.

### Step 2: Module-Level Investigation (Socratic)

Guide the learner to reason about WHERE in the system the problem is likely to live.

Example questions:
- "Given the symptoms we see, which of these modules/layers do you think could be involved? Why?"
- "What would it look like if the problem were in module A vs. module B? How would the symptoms differ?"
- "The error manifests in X, but does that necessarily mean X is the source? What else could produce this symptom?"
- "If you had to bet on one area to investigate first, which would it be and why?"

**Key teaching goals:**
- Symptoms often manifest far from root causes. Teach the learner to distinguish between WHERE a failure appears and WHERE it originates.
- Different modules have different failure signatures. Help the learner build pattern recognition.
- Systematic elimination is more reliable than intuition. Model the reasoning process.

**When the learner picks the wrong module:** Do not say "no, that's wrong." Instead:
- "Interesting -- if the problem were in [wrong module], what specific symptoms would you expect? Do those match what we see?"
- Let them discover the mismatch themselves. This is more valuable than being corrected.

**When the learner picks the right module:** Probe the WHY.
- "Good instinct -- what specifically about the symptoms points you there?"
- Ensure they can articulate the reasoning, not just guess correctly.

### Step 3: Failure Mode Reasoning (Socratic)

Once the learner has identified the correct area (with scaffolding as needed), narrow to the specific type of failure.

Example questions:
- "OK, we think the issue is in [correct area]. Within this module, what types of problems could produce the symptoms we see?"
- "Could this be a data issue, a timing issue, a contract mismatch, or something else? What evidence points to one over another?"
- "What's the difference between [failure mode A] and [failure mode B]? Which fits the evidence better?"
- "If this were a [failure mode A], what else would we expect to see? Do we see it?"

**Key teaching goals:**
- Build a vocabulary of failure modes (stale state, race conditions, contract violations, missing validation, configuration drift, assumption mismatch, etc.)
- Teach differential diagnosis -- how to use evidence to distinguish between failure modes
- Reinforce that the same symptoms can have different causes; the evidence narrows the field

### Step 4: Root Cause Confirmation

Once the learner has identified both the correct area AND the correct failure mode (with scaffolding as needed):

1. **Confirm their reasoning.** Acknowledge specifically what they got right and why their reasoning was sound.
2. **Ask them to propose the conceptual fix.** NOT the code -- the logical/architectural change.
   - "Now that we know the problem is [failure mode] in [module], what do you think needs to change conceptually to fix this?"
   - "What's the principle that was violated here, and how would you restore it?"
3. **Validate their proposed fix.** Probe for edge cases and completeness.
   - "Would that fix also handle the case where...?"
   - "What could go wrong with that approach?"

**Eureka moment:** When the learner connects the dots -- symptom to module to failure mode to fix -- acknowledge it clearly. This is the payoff. Let it breathe.

### Step 5: Resolution

After understanding is confirmed:

1. **Proceed to actual implementation.** Use `learning-mode:test-driven-development` if that skill is available.
2. **The learner should write the fix** (or direct Claude to write it) based on their own understanding.
3. **Verify the fix** resolves the original issue.
4. **Quick retrospective:** "What would you look for first if you saw similar symptoms in the future?"

## Adaptive Scaffolding

Apply the scaffolding ladder from `${CLAUDE_PLUGIN_ROOT}/references/pedagogy.md` at EVERY Socratic step (Steps 2, 3, and 4). The ladder resets for each new concept:

| Attempt | Response |
|---------|----------|
| **1st** | Pure Socratic questioning. "Which modules do you think could be involved?" |
| **2nd** | Narrowing question. "What if we focus specifically on the boundary between X and Y?" |
| **3rd** | Hint with direction. "Consider what happens when module X passes data to module Y -- what assumptions does Y make?" |
| **4th** | Partial reveal. "The issue is in the interaction between X and Y. Given that, what type of failure do you think it is?" |
| **5th** | Explain fully, then verify. State the root cause, explain WHY, then ask a follow-up question to confirm understanding. |

**The ladder is per-concept, not per-session.** Moving from Step 2 to Step 3 resets the ladder. The learner might nail module identification on attempt 1 but need all 5 attempts for failure mode reasoning. That is normal and fine.

**Never punish struggling.** Reaching attempt 5 means the learner tried. The scaffolding exists to prevent frustration, not to judge.

## Tutor Red Flags

These thoughts mean STOP -- you are about to violate the teaching process:

| Thought | Reality | Instead |
|---------|---------|---------|
| "Let me just tell them the answer, it's faster" | Speed is not the goal. Understanding is. | Follow the scaffolding ladder. |
| "They're struggling, I should just reveal it" | Struggling IS learning. Scaffolding handles this. | Move to the next rung on the ladder. |
| "This bug is too complex to teach" | Complex bugs are the BEST teaching opportunities. Simplify the module map. | Break it into smaller Socratic questions. |
| "They already know this area" | Verify, don't assume. They might be guessing. | "What specifically about X makes you think that?" |
| "I'll just show them the failing line" | Line-level debugging skips the systems thinking. | Stay at the module/workflow level. |
| "They're getting frustrated" | Frustration means the scaffolding needs to advance, not that teaching should stop. | Move to the next rung. Acknowledge the difficulty. |
| "This is taking too long" | Teaching IS the product. The bug fix is a side effect. | Stay the course. |
| "I'll teach the next bug, let me just fix this one" | Every bug is an opportunity. There is no "next time." | Teach this one. |
| "They got the right area by luck, close enough" | Lucky guesses don't build transferable skill. | Probe: "Why that area? What evidence?" |
| "The error message makes it obvious" | Obvious to YOU. The learner needs to build the pattern recognition that makes it obvious. | "What does this error message tell you about where to look?" |

## Rationalization Table

If you catch yourself thinking any of these, you are rationalizing your way out of the process:

| Rationalization | Why It's Wrong |
|----------------|---------------|
| "The learner asked me to just fix it" | They opted into learning-mode. The teaching process IS what they signed up for. Offer to focus on highest-value teaching moments. |
| "This is a trivial bug, not worth the Socratic process" | Trivial bugs are where unexamined assumptions hide. The process is especially valuable for "obvious" bugs. |
| "I need to fix this before I can teach it" | Phase 1 IS fixing it (privately). Phase 2 IS teaching it. The process handles both. |
| "The learner already knows the root cause" | Then Step 2 will be fast. That's fine. Don't skip it -- verify. |
| "There are too many bugs to teach all of them" | In typical development, you encounter one or two bugs at a time. Walk through all of them. If you genuinely face a large batch (5+), teach each one but adjust depth -- spend more time on bugs with distinct failure modes and less on bugs that repeat a pattern the learner has already demonstrated understanding of. |

## Scope: Macro, Not Micro

This skill operates at the **module, workflow, and architectural layer** level. It does NOT operate at the function or line level.

**In scope:**
- Which module/subsystem is at fault?
- Which workflow or data flow is broken?
- What type of failure is this? (contract violation, stale state, race condition, etc.)
- What architectural principle was violated?
- What is the conceptual fix?

**Out of scope for Socratic questioning (handle directly during implementation):**
- Which specific line of code has the bug?
- What is the exact syntax of the fix?
- Detailed code review of the patch

The goal is to teach the learner to THINK about systems, not to quiz them on code syntax. Once the learner understands the WHAT, WHERE, and WHY at the system level, the specific code changes become straightforward implementation -- handle those in Step 5 using test-driven-development.

## Integration with Other Skills

- **`learning-mode:test-driven-development`**: Use in Step 5 for implementing the fix, if the skill is available.
- **`learning-mode:socratic-brainstorming`**: If the debugging investigation reveals a deeper architectural problem that requires design decisions, transition to brainstorming.

## Example Interaction Sketch

**Scenario:** A test is failing because a REST endpoint returns 404 when it should return 200.

**Phase 1 (private):** Claude investigates. Discovers the route registration was moved to a new module during a recent refactor, but the middleware that applies authentication was not updated to cover the new route path. The endpoint exists but the auth middleware rejects the request before it reaches the handler, and the auth rejection is misconfigured to return 404 instead of 401.

**Phase 2 (with learner):**

*Step 1 -- Context:* "We have a test failure: the endpoint GET /api/widgets returns 404. This feature involves the routing layer, the authentication middleware, and the widget handler. These three interact in sequence: a request hits routing, passes through middleware, then reaches the handler."

*Step 2 -- Module Investigation:* "Given a 404 on an endpoint that we expect to exist, which of these three layers do you think is most likely to be the source? Why?" ... (learner reasons through it, guided by scaffolding)

*Step 3 -- Failure Mode Reasoning:* "OK, we think the middleware layer is involved. What kinds of middleware failures could produce a 404? How is that different from a routing failure that produces a 404?" ... (learner distinguishes between route-not-found vs. middleware-rejection-misconfigured-as-404)

*Step 4 -- Confirmation:* "You've identified that the auth middleware is rejecting requests to the new path and returning 404 instead of 401. What needs to change conceptually?" ... (learner proposes updating the middleware config to cover the new route path and fixing the error code)

*Step 5 -- Resolution:* Proceed to implement and test the fix.

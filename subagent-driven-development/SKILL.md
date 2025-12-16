---
name: subagent-driven-development
description: "Use when executing implementation plans with independent tasks in the current session - executes tasks sequentially with code review between tasks, enabling fast iteration with quality gates (adapted for Codex: subagents not available, execute tasks directly)"
---

# Subagent-Driven Development

Execute plan by completing tasks sequentially with code review after each.

**Note for Codex:** Subagents aren't available in Codex. This skill is adapted to execute tasks directly in the current session with review checkpoints.

**Core principle:** Sequential task execution + review between tasks = high quality, fast iteration

## Overview

**vs. Executing Plans (parallel session):**
- Same session (no context switch)
- Sequential task execution (clear separation between tasks)
- Code review after each task (catch issues early)
- Faster iteration (no human-in-loop between tasks)

**When to use:**
- Staying in this session
- Tasks are mostly independent
- Want continuous progress with quality gates

**When NOT to use:**
- Need to review plan first (use executing-plans)
- Tasks are tightly coupled (manual execution better)
- Plan needs revision (brainstorm first)

## The Process

### 1. Load Plan

Read plan file, create update_plan with all tasks.

### 2. Execute Task

For each task:

**Execute the task directly:**
1. Read the task from the plan file carefully
2. Implement exactly what the task specifies
3. Write tests (following TDD if task says to)
4. Verify implementation works
5. Commit your work
6. Document what you implemented, what you tested, test results, files changed, any issues

**Note:** In Codex, execute tasks directly rather than dispatching subagents.

### 3. Review Task Work

**Perform code review:**
- Use the requesting-code-review skill to review your work
- Review against: Task N from [plan-file]
- Check BASE_SHA (commit before task) vs HEAD_SHA (current commit)
- Document: Strengths, Issues (Critical/Important/Minor), Assessment

**Note:** In Codex, perform the review yourself using the code review skill rather than dispatching a subagent.

### 4. Apply Review Feedback

**If issues found:**
- Fix Critical issues immediately
- Fix Important issues before next task
- Note Minor issues

**Fix issues if needed:**
- Fix Critical issues immediately
- Fix Important issues before next task
- Note Minor issues for later

### 5. Mark Complete, Next Task

- Mark task as completed in update_plan
- Move to next task
- Repeat steps 2-5

### 6. Final Review

After all tasks complete, perform final code review:
- Review entire implementation
- Check all plan requirements met
- Validate overall architecture

### 7. Complete Development

After final review passes:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## Example Workflow

```
You: I'm using Subagent-Driven Development to execute this plan.

[Load plan, create update_plan]

Task 1: Hook installation script

[Execute task directly]
Implemented install-hook with tests, 5/5 passing

[Get git SHAs, perform code review]
Review: Strengths: Good test coverage. Issues: None. Ready.

[Mark Task 1 complete]

Task 2: Recovery modes

[Execute task directly]
Added verify/repair, 8/8 tests passing

[Perform code review]
Review: Strengths: Solid. Issues (Important): Missing progress reporting

[Fix issues directly]
Added progress every 100 conversations

[Verify fix, mark Task 2 complete]

...

[After all tasks]
[Perform final code review]
Final review: All requirements met, ready to merge

Done!
```

## Advantages

**vs. Manual execution:**
- Sequential execution with clear task boundaries
- Each task completed fully before moving to next
- Review checkpoints prevent issues from accumulating

**vs. Executing Plans:**
- Same session (no handoff)
- Continuous progress (no waiting)
- Review checkpoints automatic

**Cost:**
- More review checkpoints
- But catches issues early (cheaper than debugging later)

## Red Flags

**Never:**
- Skip code review between tasks
- Proceed with unfixed Critical issues
- Start next task before current task is fully complete and reviewed
- Implement without reading plan task

**If task has issues:**
- Fix issues immediately before moving to next task
- Don't accumulate technical debt

## Integration

**Required workflow skills:**
- **writing-plans** - REQUIRED: Creates the plan that this skill executes
- **requesting-code-review** - REQUIRED: Review after each task (see Step 3)
- **finishing-a-development-branch** - REQUIRED: Complete development after all tasks (see Step 7)

**Each task execution must follow:**
- **test-driven-development** - Follow TDD for each task

**Alternative workflow:**
- **executing-plans** - Use for parallel session instead of same-session execution

See code-reviewer template: requesting-code-review/code-reviewer.md

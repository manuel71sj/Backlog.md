---
name: backlog-workflow
description: Use when you need guidance on Backlog.md workflows including when to create tasks, how to execute tasks properly, and how to complete tasks. Essential for understanding the task lifecycle and best practices.
---

# Backlog Workflow Guide

This skill provides comprehensive workflow guidance for using Backlog.md effectively.

## When to Use Backlog

**Create a task if the work requires planning or decision-making:**

Ask yourself: "Do I need to think about HOW to do this?"
- **YES** - Search for existing task first, create if needed
- **NO** - Just do it (the change is trivial/mechanical)

### Examples of Work That Needs Tasks
- "Fix the authentication bug" - need to investigate, understand root cause, choose fix
- "Add error handling to the API" - need to decide what errors, how to handle them
- "Refactor UserService" - need to plan new structure, migration path

### Examples of Work That Doesn't Need Tasks
- "Fix typo in README" - obvious mechanical change
- "Update version number to 2.0" - straightforward edit
- "Add missing semicolon" - clear what to do

### Always Skip Tasks For
- Questions and informational requests
- Reading/exploring/explaining code, issues, or concepts

## Typical Workflow

When the user requests non-trivial work:

1. **Search first:** Use `backlog task search` or `backlog task list` with status filters - work might already be tracked
2. **If found:** Work on the existing task
3. **If not found:** Create task(s) based on scope
4. **Execute:** Follow task execution guidelines

---

## Task Creation Workflow

### Step 1: Search for Existing Work

**IMPORTANT - Always use filters when searching:**
- Use `backlog task search "query" --plain` for text search
- Use `backlog task list -s "To Do" --plain` to filter by status
- Never list all tasks including "Done" status without explicit user request

### Step 2: Assess Scope BEFORE Creating Tasks

**CRITICAL**: Before creating any tasks, assess whether the request is:
- **Single atomic task** (single focused PR): Create one task immediately
- **Multi-task feature** (multiple PRs): Create appropriate task structure

**Scope Assessment Checklist:**
1. Can this be completed in a single focused pull request?
2. Would a code reviewer be comfortable reviewing all changes in one sitting?
3. Are there natural breaking points where work could be independently delivered?
4. Does the request span multiple subsystems or architectural concerns?

### Step 3: Choose Task Structure

**Use subtasks** when:
- Multiple tasks modify the same component
- Tasks are tightly coupled with the same high-level goal
- Tasks represent sequential phases of the same feature

**Use separate tasks with dependencies** when:
- Tasks span different components or subsystems
- Tasks can be worked on independently
- Tasks have loose coupling with clear boundaries

### Step 4: Create Tasks with Proper Scope

- **Title and description**: Explain desired outcome (the WHY)
- **Acceptance criteria**: Specific, testable, independent (the WHAT)
- **Never embed implementation details** in title, description, or criteria
- **Record dependencies** for task ordering
- **Ask for clarification** if requirements are ambiguous

### Step 5: Report Created Tasks

After creation, show the user each new task's ID, title, description, and acceptance criteria.

---

## Task Execution Workflow

### Planning Phase

> **Non-negotiable:** Capture an implementation plan in the Backlog task _before_ writing any code.

1. **Mark task as In Progress:** `backlog task edit <id> -s "In Progress"`
2. **Assign to yourself:** `backlog task edit <id> -a @claude`
3. **Draft the implementation plan** - Think through the approach, review code, identify key files
4. **Present plan to user** - Show your proposed implementation approach
5. **Wait for explicit approval** - Do not start coding until user confirms
6. **Record approved plan:** `backlog task edit <id> --plan "Approved plan here"`

### Execution Phase

- **Do not touch codebase** until implementation plan is approved and recorded
- **Work in short loops:** implement, test, check off acceptance criteria
- **Log progress:** `backlog task edit <id> --notes "Progress update"`
- **Keep task status aligned** with reality

### Handling Scope Changes

If new work appears during implementation that wasn't in the original acceptance criteria:

**STOP and ask the user:**
"I discovered [new work needed]. Should I:
1. Add acceptance criteria to the current task and continue, or
2. Create a follow-up task to handle this separately?"

**Never:**
- Silently expand scope without user approval
- Create new tasks on your own initiative
- Add acceptance criteria without user confirmation

---

## Task Completion Workflow

### Completion Checklist

1. **Verify all acceptance criteria** - Confirm every criterion is satisfied
2. **Run the Definition of Done checklist** (see below)
3. **Summarize the work:** `backlog task edit <id> --notes "Implementation summary"`
4. **Confirm implementation plan is current** - Update if approach deviated
5. **Update task status:** `backlog task edit <id> -s "Done"`
6. **Propose next steps** - Never autonomously create or start new tasks

### Definition of Done

- [ ] Implementation plan exists in the task record and reflects the final solution
- [ ] All acceptance criteria are checked/satisfied
- [ ] Automated and relevant manual tests pass; no new warnings or regressions
- [ ] Documentation or configuration updates completed when required
- [ ] Implementation notes capture what changed and why
- [ ] Status transitions to "Done"

### After Completion

**Never autonomously create or start new tasks.** Instead:

- **If follow-up work is needed:** Present the idea to the user and ask whether to create a follow-up task
- **If this was a subtask:**
  - If user said "work on parent task and all subtasks": Proceed to next subtask
  - Otherwise: Ask "Subtask X is complete. Should I proceed with subtask Y?"
- **If all subtasks complete:** Update parent task status if appropriate, then ask user what to do next

---

## Core Principle

Backlog tracks **commitments** (what will be built). Use your judgment to distinguish between "help me understand X" (no tracking) vs "add feature Y" (track in Backlog).

## CLI Quick Reference

| Action | Command |
|--------|---------|
| Create task | `backlog task create "Title" -d "Desc" --ac "Criteria"` |
| List tasks | `backlog task list --plain` |
| Search tasks | `backlog task search "query" --plain` |
| View task | `backlog task <id> --plain` |
| Edit task | `backlog task edit <id> -s "Status" --notes "Update"` |
| Archive task | `backlog task archive <id>` |

**Always use `--plain` flag for AI-friendly output.**

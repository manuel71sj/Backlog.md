---
name: backlog-task-management
description: Use when you need to create, search, view, edit, or archive tasks in the project backlog. Also use for breaking down large features into atomic work units, managing task statuses, and tracking implementation progress.
---

# Backlog Task Management

This skill provides guidance for managing tasks using the Backlog.md CLI tool.

## CLI Commands Reference

**IMPORTANT: Backlog.md uses standard CLI commands, NOT slash commands.**

### Creating Tasks

```bash
backlog task create "Task title" -d "Description" --ac "First criteria,Second criteria" -l label1,label2
```

**Parameters:**
- `-d, --description` - Task description (the WHY)
- `--ac` - Acceptance criteria (comma-separated)
- `-l, --labels` - Labels (comma-separated)
- `-a, --assignee` - Assignee (e.g., @claude)
- `-s, --status` - Initial status
- `--priority` - Priority (high, medium, low)
- `--plan` - Implementation plan
- `--notes` - Initial notes
- `--dep` - Dependencies (comma-separated task IDs)
- `-p, --parent` - Parent task ID (for subtasks)
- `--draft` - Create as draft

### Listing Tasks

```bash
backlog task list --plain
backlog task list -s "To Do" --plain
backlog task list -a @claude --plain
backlog task list -p 42 --plain
```

**Always use `--plain` flag for AI-friendly text output.**

### Searching Tasks

```bash
backlog task search "query" --plain
```

### Viewing Task Details

```bash
backlog task 42 --plain
```

### Editing Tasks

```bash
backlog task edit 42 -s "In Progress" -a @claude
backlog task edit 42 --plan "Implementation approach"
backlog task edit 42 --ac "New criterion,Another one"
backlog task edit 42 --notes "Progress update"
backlog task edit 42 --dep task-1,task-2
```

### Archiving Tasks

```bash
backlog task archive 42
```

## Task Creation Guidelines

### Title
Use a clear brief title that summarizes the task.

### Description (the WHY)
Provide a concise summary of the task purpose and its goal. Do not add implementation details here. It should explain the purpose, the scope and context of the task.

### Acceptance Criteria (the WHAT)
List specific, measurable outcomes that define what means to reach the goal. Focus on **outcomes, behaviors, and verifiable requirements** rather than step-by-step implementation details.

**Key Principles:**
- **Outcome-Oriented:** Focus on the result, not the method
- **Testable/Verifiable:** Each criterion should be objectively testable
- **Clear and Concise:** Unambiguous language
- **Complete:** Collectively cover the scope of the task

**Good Examples:**
- "User can successfully log in with valid credentials"
- "System processes 1000 requests per second without errors"

**Bad Example:**
- "Add a new function `handleLogin()` in `auth.ts`" (This is implementation, not outcome)

## Task Structure Guidelines

### When to Use Subtasks
Use subtasks (parent-child relationship) when:
- Multiple tasks all modify the same component or subsystem
- Tasks are tightly coupled and share the same high-level goal
- Tasks represent sequential phases of the same feature

### When to Use Separate Tasks with Dependencies
Use separate tasks when:
- Tasks span different components or subsystems
- Tasks can be worked on independently by different developers
- Tasks have loose coupling with clear boundaries

## Task Quality Checklist

Before creating any task, verify:
- [ ] Title is clear and brief
- [ ] Description explains WHY without HOW
- [ ] Each acceptance criterion is outcome-focused and testable
- [ ] Task is atomic (single PR scope)
- [ ] No dependencies on future tasks

## Common Anti-patterns to Avoid

- Creating a single task called "Build feature X" with 10+ acceptance criteria
- Adding implementation steps to acceptance criteria
- Creating a task before understanding if it needs to be split
- Silently expanding scope without user approval

## Tips for AI Agents

- **Always use `--plain` flag** when listing or viewing tasks
- **Search before creating** to avoid duplicates
- **Keep tasks atomic** - each task should be completable in a single focused PR
- **Document everything** in the task record for future reference

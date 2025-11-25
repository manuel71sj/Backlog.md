<!-- BACKLOG.MD MCP GUIDELINES START -->

<CRITICAL_INSTRUCTION>

## BACKLOG WORKFLOW INSTRUCTIONS

This project uses Backlog.md MCP for all task and project management.

**CRITICAL RESOURCE**: Read `backlog://workflow/overview` to understand when and how to use Backlog for this project.

- **First time working here?** Read the overview resource IMMEDIATELY to learn the workflow
- **Already familiar?** You should have the overview cached ("## Backlog.md Overview (MCP)")
- **When to read it**: BEFORE creating tasks, or when you're unsure whether to track work

The overview resource contains:
- Decision framework for when to create tasks
- Search-first workflow to avoid duplicates
- Links to detailed guides for task creation, execution, and completion
- MCP tools reference

You MUST read the overview resource to understand the complete workflow. The information is NOT summarized here.

</CRITICAL_INSTRUCTION>

<!-- BACKLOG.MD MCP GUIDELINES END -->

## Project Overview

**Backlog.md** is a markdown-based task and project management CLI tool built with Bun and TypeScript. It provides:
- CLI interface for task management (`backlog` command)
- MCP (Model Context Protocol) server for AI agent integration
- Web UI for visual task management (kanban board)
- Git integration for version-controlled task tracking

**Version**: 1.21.0 | **License**: MIT | **Homepage**: https://backlog.md

## Directory Structure

```
backlog.md/
├── src/                      # Source code
│   ├── cli.ts               # Main CLI entry point (~100KB, comprehensive CLI)
│   ├── index.ts             # Public API exports
│   ├── board.ts             # Kanban board generation
│   ├── agent-instructions.ts # AI agent configuration helpers
│   │
│   ├── core/                # Core business logic
│   │   ├── backlog.ts       # Core class - main API entry point
│   │   ├── content-store.ts # Content/document management
│   │   ├── search-service.ts # Full-text search with Fuse.js
│   │   ├── sequences.ts     # Task sequencing/ordering
│   │   ├── statistics.ts    # Project statistics
│   │   └── task-loader.ts   # Task loading and caching
│   │
│   ├── mcp/                 # MCP server implementation
│   │   ├── server.ts        # createMcpServer() entry point
│   │   ├── tools/           # MCP tool handlers (tasks, documents, workflow)
│   │   ├── resources/       # MCP resource adapters
│   │   ├── validation/      # Input validation
│   │   └── utils/           # Response formatters
│   │
│   ├── file-system/         # File system operations
│   │   └── operations.ts    # FileSystem class for backlog I/O
│   │
│   ├── git/                 # Git operations
│   │   └── operations.ts    # GitOperations class
│   │
│   ├── markdown/            # Markdown parsing/serialization
│   │   ├── parser.ts        # Frontmatter + content parsing
│   │   └── serializer.ts    # Task/document to markdown
│   │
│   ├── types/               # TypeScript type definitions
│   │   └── index.ts         # Task, Document, Decision, Config types
│   │
│   ├── utils/               # Shared utilities
│   │   ├── task-builders.ts # Task creation/update builders
│   │   ├── id-generators.ts # ID generation utilities
│   │   └── editor.ts        # External editor integration
│   │
│   ├── web/                 # Web UI (React + Tailwind)
│   │   ├── App.tsx          # Main React application
│   │   ├── components/      # React components (Board, TaskCard, etc.)
│   │   ├── contexts/        # React context providers
│   │   └── styles/          # Tailwind CSS
│   │
│   ├── test/                # Test files (*.test.ts)
│   ├── commands/            # CLI subcommand implementations
│   ├── completions/         # Shell completion logic
│   ├── formatters/          # Output formatting
│   ├── guidelines/          # Embedded workflow documentation
│   └── ui/                  # Terminal UI components
│
├── backlog/                 # Project's own task data (dogfooding)
│   ├── tasks/               # Active task markdown files
│   ├── completed/           # Archived completed tasks
│   ├── docs/                # Project documentation
│   ├── decisions/           # Architectural decision records
│   ├── drafts/              # Draft documents
│   └── config.yml           # Backlog configuration
│
├── scripts/                 # Build and install scripts
├── completions/             # Shell completion files
└── .claude/agents/          # AI agent configurations
```

## Commands

### Development
- `bun i` - Install dependencies
- `bun test` - Run all tests
- `bun run build` - Build the CLI tool (CSS + binary)
- `bun run cli` - Run CLI directly without building
- `bun run mcp` - Start MCP server in development mode

### Testing & Quality
- `CLAUDECODE=1 bun test` - Run all tests with failures-only output (**RECOMMENDED** - full output is too long for Claude)
- `bun test <filename>` - Run specific test file
- `bun test src/**/*.test.ts` - Unit tests only
- `bun test src/test/mcp-*.test.ts` - MCP tests only
- `bun test --watch` - Run tests in watch mode
- `bunx tsc --noEmit` - Type-check code
- `bun run check .` - Run all Biome checks (format + lint)

**Development Strategy**: Test specific files during development, run full suite before commits.
**Important**: Always use `CLAUDECODE=1` when running full test suite - the default verbose output exceeds Claude's consumption limits.

### Performance Benchmarking
- `bun run benchmark` - Run performance benchmark on all test files
  - Runs each test file individually and measures execution time
  - Groups results by test prefix (mcp-, cli-, board-, etc.)
  - Generates `test-benchmark-report.json` with detailed timing data
  - Shows top 10 slowest tests and performance breakdown by category

### Pre-Commit Validation (REQUIRED)
**Claude MUST verify all pass before committing:**
```bash
bunx tsc --noEmit                      # TypeScript compilation
bun run check .                        # Lint/format
CLAUDECODE=1 bun test --timeout 180000 # Full test suite (failures-only output)
```

### Configuration
- `bun run cli config list` - View all configuration values
- `bun run cli config get <key>` - Get specific value (e.g. defaultEditor)
- `bun run cli config set <key> <value>` - Set with validation

## Core Architecture

### Core Class (`src/core/backlog.ts`)
The `Core` class is the main API entry point. All operations flow through it:
```typescript
import { Core } from "./core/backlog.ts";

const core = new Core(projectRoot);
await core.initializeProject("Project Name", withClaude);

// Task operations
const task = await core.createTask(taskData, autoCommit);
await core.updateTaskFromInput(taskId, updates, autoCommit);
const tasks = await core.listTasks(filter);

// Document operations
const docs = await core.listDocuments();
const doc = await core.getDocument(docId);
```

### Key Subsystems
- **FileSystem** (`src/file-system/operations.ts`): All backlog file I/O
- **GitOperations** (`src/git/operations.ts`): Git integration, auto-commit
- **SearchService** (`src/core/search-service.ts`): Fuse.js-powered search
- **ContentStore** (`src/core/content-store.ts`): Document management

### Data Types (`src/types/index.ts`)
```typescript
interface Task {
  id: string;
  title: string;
  status: TaskStatus;
  assignee: string[];
  priority?: "high" | "medium" | "low";
  labels: string[];
  dependencies: string[];
  description?: string;
  implementationPlan?: string;
  implementationNotes?: string;
  acceptanceCriteriaItems?: AcceptanceCriterion[];
  parentTaskId?: string;
  subtasks?: string[];
  // ... more fields
}

interface Document {
  id: string;
  title: string;
  type: "readme" | "guide" | "specification" | "other";
  rawContent: string;
  // ... more fields
}
```

## Code Standards

- **Runtime**: Bun 1.2.23+ with TypeScript 5
- **Formatting**: Biome with tab indentation and double quotes
- **Line Width**: 120 characters max
- **Linting**: Biome recommended rules (see `biome.json`)
- **Testing**: Bun's built-in test runner
- **Pre-commit**: Husky + lint-staged automatically runs Biome checks

The pre-commit hook automatically runs `biome check --write` on staged files. If linting errors are found, the commit will be blocked until fixed.

## Architecture Guidelines

- **Separation of Concerns**: CLI logic and utility functions are kept separate to avoid side effects during testing
- **Utility Functions**: Reusable utility functions (like ID generators) are placed in `src/utils/` directory
- **No Side Effects on Import**: Modules should not execute CLI code when imported by other modules or tests
- **Core API First**: All operations MUST use Core APIs (never direct filesystem/git from other layers)

## Testing Patterns

Tests use Bun's built-in test runner with isolated temporary directories:

```typescript
import { afterEach, beforeEach, describe, expect, it } from "bun:test";
import { Core } from "../core/backlog.ts";
import { createUniqueTestDir, safeCleanup } from "./test-utils.ts";

let TEST_DIR: string;

describe("Feature", () => {
  let core: Core;

  beforeEach(async () => {
    TEST_DIR = createUniqueTestDir("test-feature");
    core = new Core(TEST_DIR);
    await core.filesystem.ensureBacklogStructure();
    // Initialize git for tests that need it
    await $`git init -b main`.cwd(TEST_DIR).quiet();
  });

  afterEach(async () => {
    await safeCleanup(TEST_DIR);
  });

  it("should do something", async () => {
    // Test implementation
  });
});
```

**Test Utilities** (`src/test/test-utils.ts`):
- `createUniqueTestDir(prefix)` - Creates isolated test directory
- `safeCleanup(dir)` - Windows-safe cleanup with retry
- `retry(fn, attempts, delay)` - Retry with exponential backoff
- `getPlatformTimeout(base)` - Platform-aware timeouts

## MCP Architecture Principles

- **MCP is a Pure Protocol Wrapper**: Protocol translation ONLY - no business logic, no feature extensions
- **CLI Feature Parity**: MCP = strict subset of CLI capabilities
- **Core API Usage**: All operations MUST use Core APIs (never direct filesystem/git)
- **Shared Utilities**: Reuse exact same utilities as CLI (`src/utils/task-builders.ts`)
- **Local Development Only**: stdio transport only (see `/src/mcp/README.md`)

**Violations to Avoid**:
- Custom business logic in MCP handlers
- Direct filesystem or git operations
- Features beyond CLI capabilities

See MCP implementation in `/src/mcp/` for development details.

### MCP Testing
```bash
# Run MCP server for development
bun run mcp

# Run MCP tests
bun test src/test/mcp-*.test.ts
```

## Git Workflow

- **Branching**: Use feature branches when working on tasks (e.g. `tasks/task-123-feature-name`)
- **Committing**: Use the following format: `TASK-123 - Title of the task`
- **Github CLI**: Use `gh` whenever possible for PRs and issues

## CLI Multi-line Input (description/plan/notes)

The CLI preserves input literally; `\n` sequences in normal quotes are not converted. Use one of the following when you need real newlines:

- **Bash/Zsh (ANSI‑C quoting)**:
  - `backlog task edit 42 --notes $'Line1\nLine2'`
  - `backlog task edit 42 --plan $'1. A\n2. B'`
- **POSIX (printf)**:
  - `backlog task edit 42 --desc "$(printf 'Line1\nLine2')"`
- **PowerShell (backtick)**:
  - `backlog task edit 42 --desc "Line1\`nLine2"`

*Note: `"...\n..."` passes literal backslash+n, not newline*

## Using Bun

Default to using Bun instead of Node.js:

- Use `bun <file>` instead of `node <file>` or `ts-node <file>`
- Use `bun test` instead of `jest` or `vitest`
- Use `bun build` instead of `webpack` or `esbuild`
- Use `bun install` instead of `npm/yarn/pnpm install`
- Use `bun run <script>` instead of `npm run <script>`
- Bun automatically loads .env, so don't use dotenv
- Run `bunx tsc --noEmit` to perform TypeScript compilation checks

### Key Bun APIs
- `Bun.serve()` supports WebSockets, HTTPS, and routes. Don't use `express`
- `bun:sqlite` for SQLite. Don't use `better-sqlite3`
- `WebSocket` is built-in. Don't use `ws`
- Prefer `Bun.file` over `node:fs`'s readFile/writeFile
- `Bun.$\`command\`` instead of execa for shell commands

## Web UI Development

### Build Commands
- `bun run build:css` - Build Tailwind CSS
- `bun run build` - Build CSS + compile CLI binary

### Architecture
- **HTML Imports**: Use `Bun.serve()` with direct .tsx/.jsx imports (no bundler needed)
- **CSS**: Tailwind CSS processed via `@tailwindcss/cli`
- **React**: Components in `/src/web/components/`, contexts in `/src/web/contexts/`
- **Bundling**: Bun handles .tsx/.jsx transpilation automatically

### Key Components
- `Board.tsx` - Kanban board view
- `TaskCard.tsx` / `TaskDetailsModal.tsx` - Task display/editing
- `SideNavigation.tsx` - Main navigation
- `Statistics.tsx` - Project statistics dashboard

### Server Example
```typescript
import index from "./index.html"

Bun.serve({
  routes: {
    "/": index,
    "/api/users/:id": {
      GET: (req) => {
        return new Response(JSON.stringify({ id: req.params.id }));
      },
    },
  },
  development: { hmr: true, console: true }
})
```

Run with: `bun --hot ./index.ts`

## Configuration File (`backlog/config.yml`)

Key configuration options:
```yaml
project_name: "Project Name"
default_status: "To Do"
statuses: ["To Do", "In Progress", "Done"]
labels: []
milestones: []
date_format: yyyy-mm-dd hh:mm
default_editor: "code"           # Editor for task editing
auto_open_browser: true          # Auto-open web UI
default_port: 6420               # Web UI port
auto_commit: false               # Auto-commit on task changes
zero_padded_ids: 3               # ID padding (e.g., task-001)
check_active_branches: true      # Cross-branch task checking
```

## Simplicity Principle

At the end of every task implementation, take a moment to see if you can simplify it. When you are done implementing, you know much more about a task than when you started. At this point you can better judge retrospectively what can be the simplest architecture to solve the problem. If you can simplify the code, do it.

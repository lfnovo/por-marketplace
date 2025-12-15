# POR Dev Plugin

A Claude Code plugin that provides structured workflows for feature development, bug fixes, and chores following the **Product on Rails** methodology.

## Overview

This plugin helps you build features methodically by enforcing a structured approach to planning and implementation. It ensures thorough analysis before coding begins, reducing rework and improving code quality.

**Key Benefits:**
- Consistent planning process across all development tasks
- Clear separation between discovery, design, planning, and implementation
- Documented specs that serve as future reference
- Flexible workflows for different task complexities

## Installation

```bash
# Add the marketplace (if not already added)
claude plugin marketplace add /path/to/por-marketplace

# Install the plugin
claude plugin install por-dev-plugin@por-marketplace
```

## Workflows

The plugin provides two main workflows depending on task complexity:

### Fast Track (Quick Tasks)

For smaller, well-understood tasks where extensive planning isn't needed.

```
/prime → /fast:[bug|chore|feature] → /implement
```

| Command | Purpose |
|---------|---------|
| `/fast:bug <description>` | Plan a surgical bug fix with root cause analysis |
| `/fast:chore <description>` | Plan a simple maintenance task |
| `/fast:feature <description>` | Plan a new feature with phased implementation |

**Output:** Creates a single plan file at `specs/<task-name>.md`

### Complete Workflow (Complex Features)

For larger features requiring thorough analysis and architecture design.

```
/prime → /discover → /design → /plan → /implement
```

| Command | Purpose |
|---------|---------|
| `/discover <input>` | Create feature specification from requirements |
| `/design` | Design technical architecture |
| `/plan` | Generate detailed implementation tasks |

**Output:** Creates structured documentation in `specs/<feature-slug>/`:
- `spec.md` - Feature requirements and user stories
- `architecture.md` - Technical design and decisions
- `contracts.md` - API/data contracts (if applicable)
- `plan.md` - Step-by-step implementation tasks

## Command Reference

### `/prime`

**Always run first.** Primes the agent with codebase context by reading project structure, README, and available documentation.

```bash
/prime
```

### `/discover <input>`

Creates a feature specification from requirements. Input can be:
- Natural language description
- Path to a requirements file
- Project management card (via MCP tools)

```bash
/discover "Add user authentication with OAuth support"
/discover ./requirements/auth-feature.md
/discover LINEAR-123
```

**What it does:**
1. Creates a feature branch and `specs/<feature-slug>/` folder
2. Analyzes requirements to extract: actors, actions, data, constraints
3. Generates `spec.md` with user stories, functional requirements, and success criteria
4. Identifies clarification questions and presents them for review

### `/design`

Transforms requirements into technical architecture. Requires completed `spec.md`.

```bash
/design
```

**What it does:**
1. Researches unfamiliar libraries/APIs
2. Analyzes existing codebase patterns
3. Designs component structure and data flow
4. Generates `architecture.md` with technical decisions and rationale
5. Optionally generates `contracts.md` for API/data contracts

### `/plan`

Breaks architecture into actionable implementation tasks. Requires completed `architecture.md`.

```bash
/plan
```

**What it does:**
1. Organizes tasks by phase (Setup → Foundational → User Stories → Polish)
2. Identifies parallel execution opportunities
3. Estimates time per phase
4. Generates `plan.md` with trackable task list

### `/implement [--ff|--phases]`

Executes the implementation plan.

```bash
/implement              # Default: phased approach with review stops
/implement --ff         # Fast-forward: complete build without stops
/implement --phases     # Explicit phased approach
```

**Modes:**
- `--phases` (default): Stops after each phase for user review
- `--ff`: Builds completely, only stops for questions or blockers

**What it does:**
1. Reads and executes the plan step-by-step
2. Updates `plan.md` marking completed tasks
3. Reports summary and `git diff --stat` when done

### `/fast:bug <description>`

Quick planning for bug fixes.

```bash
/fast:bug "Login fails when email contains + character"
```

**Creates plan with:**
- Bug description and steps to reproduce
- Root cause analysis
- Surgical fix approach (minimal changes)
- Validation commands

### `/fast:chore <description>`

Quick planning for maintenance tasks.

```bash
/fast:chore "Upgrade dependencies to latest versions"
```

**Creates plan with:**
- Chore description
- Step-by-step tasks
- Validation commands

### `/fast:feature <description>`

Quick planning for smaller features.

```bash
/fast:feature "Add export to CSV button on reports page"
```

**Creates plan with:**
- Feature description and user story
- Phased implementation plan
- Testing strategy
- Acceptance criteria

### `/all-tools`

Lists all available Claude Code tools in the current session.

```bash
/all-tools
```

## Workflow Decision Guide

| Scenario | Recommended Workflow |
|----------|---------------------|
| Simple bug fix | `/prime` → `/fast:bug` → `/implement` |
| Dependency update | `/prime` → `/fast:chore` → `/implement` |
| Small UI feature | `/prime` → `/fast:feature` → `/implement` |
| New API endpoint | `/prime` → `/discover` → `/design` → `/plan` → `/implement` |
| Complex feature | `/prime` → `/discover` → `/design` → `/plan` → `/implement` |
| Refactoring | `/prime` → `/fast:chore` or complete workflow |

## Project Structure

After using the plugin, your project will have:

```
your-project/
├── specs/
│   ├── fix-login-bug.md           # Fast track plans
│   ├── upgrade-deps.md
│   └── user-authentication/       # Complete workflow
│       ├── spec.md
│       ├── architecture.md
│       ├── contracts.md
│       └── plan.md
└── ...
```

## Best Practices

1. **Always start with `/prime`** - Ensures the agent understands your codebase
2. **Use complete workflow for uncertainty** - When requirements are unclear or the feature is complex
3. **Answer clarification questions** - The discover phase identifies ambiguities early
4. **Review architecture before planning** - Catch design issues before implementation
5. **Use `--phases` for learning** - See how the agent approaches each phase
6. **Use `--ff` for confidence** - When you trust the plan and want faster execution

## Contributing

Contributions are welcome! Please ensure any changes maintain the structured workflow philosophy.

## License

MIT

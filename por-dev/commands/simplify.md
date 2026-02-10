---
description: Map the entire system, detect unnecessary complexity, and generate a simplification plan
argument-hint: [optional-scope: path, module, or "full"]
model: opus
---

# Simplify Command

This command performs a complete audit of the codebase to identify unnecessary complexity and generate a concrete, prioritized simplification plan.

## Philosophy

> "The best architecture is the one a junior dev can understand."
> Every abstraction must justify its existence with a **real, current** benefit — not a hypothetical future one.

## User Input

Arguments:
```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

- If a **path or module** is provided: scope the audit to that area only (Focused Mode)
- If `"full"` or empty: audit the entire project
- If a **previous report path** is provided: run in Re-audit Mode (compare with previous findings)

## Prerequisites

Before running this command:
- ✅ You should be in the root of the project
- ✅ The project should be in a buildable/runnable state
- ❌ Do NOT run this on a project you haven't explored at all — run a quick `ls` and `cat README.md` first

## Execution Flow

### Phase 0: Context Loading ⏳

**Purpose**: Leverage existing project documentation before doing any analysis from scratch.

#### Step 0.1: Check for CLAUDE.md Files

Search the project for existing `CLAUDE.md` files in the root and subdirectories.

**If CLAUDE.md files exist:**
1. Load all `CLAUDE.md` files found across the project
2. Extract: architecture overview, patterns, conventions, tech stack, module responsibilities
3. Use this as the baseline context for Phase 1 — do NOT redo work that's already documented
4. Note any gaps or outdated information to flag later

**If CLAUDE.md files do NOT exist:**
1. Ask the user: "No CLAUDE.md files found. I recommend generating them first for a more accurate audit. Want me to run `/generate-all-claude-mds` before proceeding, or should I map the system manually?"
2. If user agrees: execute `/generate-all-claude-mds` and then load the generated files
3. If user declines: proceed to Phase 1 and map the system from scratch

#### Step 0.2: Load Previous Audit (Re-audit Mode Only)

If a previous report path was provided:
1. Load the previous `audit.md`
2. Extract: previous health score, previously identified issues, action items
3. Use this to focus on deltas — what changed, what improved, what got worse

**Notes:**
- [Space for context loading findings]

---

### Phase 1: System Mapping ⏳

**Purpose**: Build a complete mental model of the system before analyzing anything. If CLAUDE.md files were loaded in Phase 0, use them as the foundation and only fill in gaps.

#### Step 1.1: Project Identity

Gather (skip what's already documented in CLAUDE.md):
- Language(s) and framework(s)
- Package manager and dependency file (`package.json`, `requirements.txt`, `Gemfile`, `go.mod`, etc.)
- Entry points (`main`, `index`, `app`, `server`, `manage.py`, etc.)
- Build system and scripts
- Test framework and test location

#### Step 1.2: Directory Structure

- Map the folder structure (2-3 levels deep, ignore `node_modules`, `.git`, `dist`, `build`, `__pycache__`, `.venv`)
- Identify the architectural pattern (MVC, Clean Architecture, Hexagonal, monolith, monorepo, microservices, etc.)
- Count: total files, total lines of code (approximate)

#### Step 1.3: Dependency Inventory

- List all external dependencies with their purpose
- Flag any that seem heavy for their usage
- Flag any that are duplicated in purpose
- Flag any that are abandoned/deprecated

#### Step 1.4: Architecture Map

Create an ASCII diagram showing:
- The layers/modules of the system
- How they connect to each other
- External integrations (databases, APIs, queues, etc.)
- Data flow direction

Example:
```
[Client] → [API Routes] → [Controllers] → [Services] → [Repositories] → [Database]
                                              ↓
                                        [External APIs]
```

#### Step 1.5: Critical Flows

Identify the 3-5 most important flows in the system (e.g., authentication, main CRUD, payment, etc.) and for each one, trace the path:
- Entry point → middleware → handler → service → data layer → response
- List every file touched in order

**Checkpoint**: ✋ Present the system map to the user. Ask: "Does this match your understanding? Anything missing?"

**Notes:**
- [Space for findings and corrections from user]

---

### Phase 2: Complexity Detection ⏳

**Purpose**: Systematically scan the codebase for patterns of unnecessary complexity.

Run through each category below. For every issue found, record:
- **What**: Description of the problem
- **Where**: Exact file path and line range
- **Why it matters**: Impact on maintainability, onboarding, or debugging

#### 2.1 — Premature Abstractions

Scan for:
- [ ] Interfaces/abstract classes/protocols with only ONE implementation
- [ ] Design patterns (Factory, Strategy, Observer, Mediator, etc.) used where a simple `if/else` or direct call would suffice
- [ ] Pass-through layers — classes/functions that receive a call and forward it to the next layer without adding any logic
- [ ] Deep inheritance hierarchies (3+ levels) where composition would be simpler
- [ ] Generic types/generics used in contexts where the type never actually varies
- [ ] "Base" classes that are inherited by a single child
- [ ] Event systems/pub-sub for communication between 2 components in the same process

#### 2.2 — Structural Over-Engineering

Scan for:
- [ ] Folders containing a single file
- [ ] Excessive separation for simple features (e.g., controller + service + repository + DTO + mapper + validator + factory for a basic CRUD)
- [ ] Multiple config files that could be consolidated
- [ ] Monorepo structure where a single project would suffice (or vice-versa)
- [ ] Barrel/index files that just re-export everything
- [ ] Unnecessary separation of "concerns" (e.g., splitting a 30-line component into 5 files)

#### 2.3 — Dependency Bloat

Scan for:
- [ ] Heavy libraries used for trivial features (e.g., `lodash` for a single `_.get`, `moment` for one date format)
- [ ] Multiple libraries that do the same thing (e.g., `axios` AND `node-fetch`, `dayjs` AND `moment`)
- [ ] Libraries that could be replaced by native language features (e.g., `uuid` in Node 19+, `lodash.merge` vs spread operator)
- [ ] Custom wrappers around libraries that already have a simple API
- [ ] Unused dependencies (installed but never imported)
- [ ] Dependencies with known vulnerabilities or no maintenance

#### 2.4 — Code Complexity

Scan for:
- [ ] Functions/methods longer than 50 lines
- [ ] Functions with more than 4 parameters
- [ ] Nesting deeper than 3 levels (nested if/for/try)
- [ ] Global state or unnecessary singletons
- [ ] Overly complex types (deeply nested generics, huge union types, conditional types that are hard to read)
- [ ] Inconsistent or excessively defensive error handling
- [ ] Dead code — functions, files, exports, routes that are never referenced
- [ ] Comments explaining "what" instead of "why" (sign of unclear code)
- [ ] Magic numbers/strings without named constants

#### 2.5 — Configuration & Infrastructure Complexity

Scan for:
- [ ] Too many environment variables needed to run locally
- [ ] Docker/CI/CD with unnecessary steps or layers
- [ ] Multiple config files that could be one (e.g., separate eslint, prettier, tsconfig for a simple project)
- [ ] Complex build pipeline with excessive transformations
- [ ] Setup scripts that should be simpler
- [ ] Over-configured linting/formatting rules

#### 2.6 — Test Complexity

Scan for:
- [ ] Excessive mocking that makes tests fragile and hard to understand
- [ ] Tests that test implementation details instead of behavior
- [ ] Test setup/fixtures more complex than the code being tested
- [ ] Test factories/builders that nobody fully understands
- [ ] Duplicated test utilities that could be shared
- [ ] Tests that require external services to run (when they shouldn't)

**Checkpoint**: ✋ Present the full list of findings to the user. Ask: "Any of these are intentional/justified? I'll adjust my analysis."

**Notes:**
- [Space for user feedback on findings]

---

### Phase 3: Classification & Prioritization ⏳

**Purpose**: Turn raw findings into a prioritized action plan.

#### Step 3.1: Classify Each Finding

For each issue from Phase 2, assign:

**Severity:**
| Level | Meaning |
|-------|---------|
| 🔴 Critical | Causes bugs, blocks onboarding, or significantly increases maintenance cost |
| 🟡 Moderate | Makes code harder to maintain but doesn't block anyone |
| 🟢 Minor | Cosmetic improvement, quality-of-life fix |

**Effort:**
| Level | Meaning |
|-------|---------|
| ⚡ Quick win | < 1 hour, no risk of breaking things |
| 🔧 Medium | 1-4 hours, low risk |
| 🏗️ Refactor | > 4 hours, requires planning and careful testing |

**Justified Complexity:**
Some complexity IS justified. Mark these as ✅ **Justified** with a clear explanation of why. Examples:
- "This abstraction has 3 implementations that are actively used"
- "This pattern is required by the framework"
- "This complexity handles a genuine edge case in production"

#### Step 3.2: Priority Score

Sort by: **High Impact + Low Effort = Do First**

Create a ranked list:
1. 🔴 + ⚡ (Critical quick wins — do these NOW)
2. 🔴 + 🔧 (Critical medium effort — schedule this week)
3. 🟡 + ⚡ (Moderate quick wins — batch these together)
4. 🟡 + 🔧 (Moderate medium — schedule when convenient)
5. 🔴 + 🏗️ (Critical refactors — plan carefully)
6. Everything else

**Notes:**
- [Space for priority adjustments]

---

### Phase 4: Simplification Plan ⏳

**Purpose**: Provide concrete, actionable simplification proposals with before/after code.

For each finding (in priority order), generate a simplification card:

```markdown
### SIM-XXX: [Title]

**Where:** `path/to/file.ext` (lines X-Y)
**Severity:** 🔴/🟡/🟢
**Effort:** ⚡/🔧/🏗️
**Category:** [Premature Abstraction | Structural | Dependency | Code | Config | Test]

#### Problem
[Clear description of what is unnecessarily complex and WHY it's a problem.
Include the real impact: "New devs spend 30 min understanding this flow that should take 5 min"]

#### Current State (Before)
[Summarized code or structure showing the complexity — NOT the full file, just enough to understand]

#### Proposed State (After)
[Concrete code or structure showing the simplified version]

#### Migration Steps
1. [ ] Step 1
2. [ ] Step 2
3. [ ] Run tests
4. [ ] Validate in staging/locally

#### Risks
[What could break and how to mitigate. If no risk, say "Low risk — isolated change"]

#### Lines Removed (estimate)
~XX lines
```

**Checkpoint**: ✋ Present the plan. Ask: "Want me to adjust any proposals before I generate the final report?"

**Notes:**
- [Space for user adjustments]

---

### Phase 5: Report Generation ⏳

**Purpose**: Generate the final audit report as a permanent artifact.

#### Step 5.1: Generate audit.md

Create `./specs/complexity-audit/audit.md` (or `./specs/complexity-audit-<scope>/audit.md` for focused audits):

```markdown
# Complexity Audit Report

**Date**: [Current date]
**Scope**: [Full project | Module: path/to/module]
**Branch**: [current branch]
**Auditor**: Claude (AI-assisted audit)

## Executive Summary

**Health Score**: [X/10] — [Healthy | Needs Attention | Needs Intervention | Critical]

```
📊 AUDIT DASHBOARD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total files analyzed:             ___
Total issues found:               ___
  🔴 Critical:                    ___
  🟡 Moderate:                    ___
  🟢 Minor:                       ___
  ✅ Justified complexity:        ___
Quick wins available:             ___
Estimated lines removable:       ~___
Estimated deps removable:         ___
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Key Findings

[2-3 paragraphs summarizing:
- The biggest source of unnecessary complexity
- The top 3 quick wins that would have the most impact
- Whether any structural refactoring is recommended
- Overall assessment of code health]

## System Map

[Copy from Phase 1 — architecture diagram, layers, critical flows]

## Findings by Category

### Premature Abstractions (X issues)
[List findings with SIM-XXX references]

### Structural Over-Engineering (X issues)
[List findings]

### Dependency Bloat (X issues)
[List findings]

### Code Complexity (X issues)
[List findings]

### Configuration Complexity (X issues)
[List findings]

### Test Complexity (X issues)
[List findings]

### Justified Complexity (X items)
[List items that are complex but justified, with explanations]

## Prioritized Action Plan

### 🚀 Immediate (This Week) — Quick Wins
| ID | Title | Severity | Effort | Lines Saved |
|----|-------|----------|--------|-------------|
| SIM-001 | ... | 🔴 | ⚡ | ~XX |
| SIM-002 | ... | 🔴 | ⚡ | ~XX |

### 📋 Short Term (This Sprint) — Medium Effort
| ID | Title | Severity | Effort | Lines Saved |
|----|-------|----------|--------|-------------|
| SIM-005 | ... | 🟡 | 🔧 | ~XX |

### 🗓️ Planned (Next Sprint) — Refactors
| ID | Title | Severity | Effort | Lines Saved |
|----|-------|----------|--------|-------------|
| SIM-010 | ... | 🔴 | 🏗️ | ~XX |

## Detailed Simplification Cards

[All SIM-XXX cards from Phase 4, in priority order]

## Recommendations

### Do Now
1. [Top priority action]
2. [Second priority]
3. [Third priority]

### Stop Doing
1. [Pattern to avoid going forward]
2. [Another anti-pattern to stop]

### Start Doing
1. [Good practice to adopt]
2. [Another good practice]

### Principles for the Team
1. **YAGNI** — You Aren't Gonna Need It. Don't build for hypothetical futures.
2. **Rule of Three** — Don't abstract until you have 3 real use cases.
3. **Boring is Good** — Prefer simple, obvious code over clever, compact code.
4. **Delete > Refactor > Write** — The best code is the code you don't have.
5. **Layers Must Earn Their Keep** — Every layer of abstraction must add real value.

## Re-audit Checklist

Use this for periodic re-audits:
- [ ] Run `/simplify` again in [4-8 weeks]
- [ ] Compare with this report
- [ ] Check if quick wins were implemented
- [ ] Look for new sources of complexity
- [ ] Update health score
```

#### Step 5.2: Final Presentation

Present to the user:

1. **Health Score**: [X/10] with brief justification
2. **Top 3 Quick Wins**: The changes that will have the most immediate impact
3. **Files Generated**: Link to `audit.md`
4. **Recommended Next Action**: What to do right now

```
✅ Complexity Audit Complete!

📊 Health Score: 6/10 — Needs Attention

🚀 Top 3 Quick Wins:
1. SIM-001: Remove pass-through UserService layer (~80 lines) — ⚡ 30 min
2. SIM-003: Replace lodash with native methods (~15 lines + 1 dep) — ⚡ 20 min
3. SIM-007: Inline single-use DTOs (~45 lines) — ⚡ 45 min

📄 Full report: ./specs/complexity-audit/audit.md

💡 Recommended: Start with the 3 quick wins above.
   Then schedule SIM-010 (refactor auth layer) for next sprint.

Want me to implement any of the quick wins now?
```

## Guidelines

### What IS Unnecessary Complexity

- Abstractions with no current benefit (only hypothetical future use)
- Layers that just pass data through without transformation
- Patterns used "because best practices" without a real need
- Code that's hard to follow but doesn't need to be
- Dependencies that do less than 100 lines of custom code would

### What IS NOT Unnecessary Complexity

- Abstractions actively used by multiple consumers
- Separation required by the framework or platform
- Error handling for real production scenarios
- Type safety that catches real bugs
- Tests that validate real behavior
- Config that exists for real deployment scenarios

### Tone

- Be direct but respectful — you're helping, not judging
- Acknowledge good patterns when you see them
- Explain WHY something is complex, not just THAT it is
- Provide concrete alternatives, never vague suggestions
- Remember: the developer who wrote it had reasons — try to understand them before criticizing

### Scope Control

- **Full audit**: Every directory, every file, every dependency
- **Focused audit**: Only the specified path/module, but still check its integration points
- **Re-audit**: Compare with previous report, focus on deltas

## Error Handling

**If project has no clear structure:**
```
WARNING: I couldn't identify a clear project structure.

This might mean:
1. The project is very small (< 10 files) — may not need this audit
2. The project uses an unconventional structure — please guide me
3. The project is not in a standard language/framework — please tell me the stack

How would you like to proceed?
```

**If scope path doesn't exist:**
```
ERROR: The path provided does not exist: [path]

Available top-level directories:
- [list directories]

Please provide a valid path or run without arguments for a full audit.
```

**If previous report not found (Re-audit Mode):**
```
WARNING: Previous audit report not found at [path].

Running as a fresh audit instead. The report will be saved for future re-audits.
```

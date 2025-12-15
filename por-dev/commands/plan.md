---
description: Generate detailed implementation tasks organized by user story
argument-hint: [optional-context]
---

# Tasks Command

This command breaks down the architecture into concrete, actionable implementation tasks organized by user story and phase.

## User Input

Arguments:
```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Prerequisites

Before running this command:
- ✅ Feature specs must exist at `./specs/<feature_slug>/specs.md`
- ✅ Architecture must exist at `./specs/<feature_slug>/architecture.md`
- ✅ You should be on the correct feature branch
- ❌ Do NOT run this if architecture has unresolved open questions

## Execution Flow

### Step 0: Load Context

1. Identify the current feature by checking:
   - Current git branch name (if using git)
   - Ask user to specify feature if unclear

2. Load required files from `./specs/<feature_slug>/`:
   - **Required**: `specs.md` (for user stories with priorities)
   - **Required**: `architecture.md` (for tech decisions and components)
   - **Optional**: `contracts.md` (for API endpoints and data models)

3. Verify architecture is complete:
   - No unresolved "Open Questions" section items
   - All user stories have component mappings
   - File structure is defined
   - If incomplete, STOP and tell user to resolve architecture first

### Step 1: Extract Information

From the loaded documents, extract:

**From specs.md:**
- All user stories with priorities (P1, P2, P3, etc.)
- For each user story: title, goal, acceptance criteria
- Edge cases and special requirements

**From architecture.md:**
- Tech stack and dependencies
- Component structure (new files, modified files)
- User story mapping (which files belong to which story)
- Integration points and dependencies
- File structure and naming conventions

**From contracts.md (if exists):**
- Data models/entities
- API endpoints
- Validation rules
- Request/response formats

### Step 2: Organize Tasks by Phase

Break down the implementation into phases following this structure:

**Phase 1: Setup** (~1-2h)
- Project initialization
- Dependency installation
- Configuration setup
- Development environment prep

**Phase 2: Foundational** (~2-4h)
- Core infrastructure that BLOCKS all user stories
- Examples: Database setup, auth framework, base classes, routing infrastructure
- CRITICAL: Nothing user-story-specific here - only blocking prerequisites

**Phase 3+: User Stories** (one phase per story, in priority order)
- Each user story gets its own phase
- Organized by priority (P1 first, then P2, then P3, etc.)
- Each phase should be ~2-4 hours of work
- Include: models, services, endpoints, UI, tests (if requested)

**Final Phase: Polish**
- Documentation
- Code cleanup
- Performance optimization
- Cross-cutting concerns

### Step 3: Generate Tasks

For each phase, generate specific tasks following these rules:

**Task Format:**
```
- [ ] T001 [P] [US1] Specific task description with file path
```

**Components:**
- `[ ]` or `[x]`: Checkbox for completion tracking
- `T001`: Unique sequential ID
- `[P]`: OPTIONAL marker for tasks that can run in parallel (different files, no dependencies)
- `[US1]`: User story reference (omit for Setup/Foundational/Polish phases)
- Description: Concrete action with specific file path when relevant

**Task Granularity:**
- Each task should be completable in 15-30 minutes
- Include enough detail that an LLM can execute it
- Specify file paths when creating/modifying files
- Be specific but not excessively verbose

**Parallelization Rules:**
- Mark [P] only if tasks touch different files
- Same file = sequential (no [P] marker)
- Different files with no logical dependency = [P]
- Tests can often be [P] with each other

**Test Tasks (OPTIONAL):**
- Only include if explicitly requested in specs or user asks
- Tests should come BEFORE implementation (TDD approach)
- Each test task should verify it FAILS before implementation

### Step 4: Estimate Time

For each phase, provide a realistic time estimate:
- Setup: Usually 1-2h
- Foundational: 2-4h (can be longer for complex setups)
- User Stories: 2-4h each (adjust based on complexity)
- Polish: 1-2h

Time estimates help with:
- Session planning (knowing when to take breaks)
- Progress tracking
- Resource allocation

### Step 5: Map Dependencies

Document explicit dependencies between phases:
- Which phases must complete before others can start
- Which user stories are independent vs dependent
- Parallel opportunities (which stories can be worked simultaneously)

### Step 6: Generate plan.md

Create `./specs/<feature_slug>/plan.md` with this structure (the content itself is just for your reference, keep the structure):

```markdown
# Tasks: [FEATURE NAME]

**Branch**: [branch-name]
**Specs**: [link to specs.md]
**Architecture**: [link to architecture.md]
**Status**: Not Started ⏳

---

## Phase 1: Setup ⏳ (~2h)

**Purpose**: Initialize project structure and dependencies

- [ ] T001 [P] Create project structure per architecture.md
- [ ] T002 [P] Install dependencies: [list key dependencies]
- [ ] T003 Configure [linting/formatting tools]
- [ ] T004 Setup development environment variables

**Notes:**
- [Space for learnings and comments during implementation]

---

## Phase 2: Foundational ⏳ (~3h)

**Purpose**: Core infrastructure that BLOCKS all user stories

⚠️ **CRITICAL**: No user story work can begin until this phase completes

- [ ] T005 Setup database schema and migrations in [path]
- [ ] T006 [P] Implement authentication middleware in [path]
- [ ] T007 [P] Create base error handling in [path]
- [ ] T008 Setup logging infrastructure in [path]

**Checkpoint**: ✋ Foundation ready - user stories can now proceed

**Notes:**
- [Space for learnings and comments]

---

## Phase 3: User Story 1 - [Title] (P1) ⏳ 🎯 MVP (~4h)

**Goal**: [Brief description from specs.md]

**Independent Test**: [How to verify this story works standalone]

### Tasks

- [ ] T009 [P] [US1] Create [Entity] model in src/models/[entity].py
- [ ] T010 [P] [US1] Create [Entity2] model in src/models/[entity2].py
- [ ] T011 [US1] Implement [Service] in src/services/[service].py
- [ ] T012 [US1] Create [endpoint/feature] in src/[path]/[file].py
- [ ] T013 [US1] Add validation for [entity] fields
- [ ] T014 [US1] Add error handling for [scenario]
- [ ] T015 [US1] Add logging for [operations]

**Checkpoint**: ✋ User Story 1 complete and independently testable

**Notes:**
- [Space for learnings and comments]

---

## Phase 4: User Story 2 - [Title] (P2) ⏳ (~3h)

**Goal**: [Brief description from specs.md]

**Independent Test**: [How to verify this story works standalone]

### Tasks

- [ ] T016 [P] [US2] Create [Entity] model in src/models/[entity].py
- [ ] T017 [US2] Implement [Service] in src/services/[service].py
- [ ] T018 [US2] Create [endpoint/feature] in src/[path]/[file].py
- [ ] T019 [US2] Integrate with User Story 1 (if needed)

**Checkpoint**: ✋ User Stories 1 AND 2 both work independently

**Notes:**
- [Space for learnings and comments]

---

[Repeat for each user story: Phase 5 for US3, Phase 6 for US4, etc.]

---

## Phase N: Polish & Documentation ⏳ (~2h)

**Purpose**: Final touches and cross-cutting improvements

- [ ] TXXX [P] Update README.md with new feature documentation
- [ ] TXXX [P] Add inline code comments for complex logic
- [ ] TXXX Code cleanup and refactoring
- [ ] TXXX Performance optimization review
- [ ] TXXX Security review checklist

**Notes:**
- [Space for learnings and comments]

---

## Dependencies & Execution Order

### Phase Dependencies

```
Setup (Phase 1)
    ↓
Foundational (Phase 2) ← BLOCKS everything below
    ↓
    ├─→ User Story 1 (Phase 3) - Can start
    ├─→ User Story 2 (Phase 4) - Can start in parallel OR after US1
    └─→ User Story 3 (Phase 5) - Can start in parallel OR after US1/US2
    ↓
Polish (Final Phase) - After all desired stories complete
```

### User Story Independence

- **US1 (P1)**: No dependencies on other stories ✅ MVP
- **US2 (P2)**: [Independent OR depends on US1 - specify]
- **US3 (P3)**: [Independent OR depends on US1/US2 - specify]

### Parallel Opportunities

**Within a phase:**
- Tasks marked [P] can run simultaneously
- Example: Multiple model files can be created in parallel

**Across phases:**
- After Foundational completes, ALL user stories CAN run in parallel
- Recommended: Complete US1 first (MVP), then parallelize US2+US3

---

## Implementation Strategy

### 🎯 MVP First (Recommended)

1. Complete Phase 1: Setup ✅
2. Complete Phase 2: Foundational ✅
3. Complete Phase 3: User Story 1 ✅
4. **STOP and VALIDATE** - Test US1 independently
5. Deploy/demo if ready
6. Then proceed to US2, US3, etc.

### 📈 Incremental Delivery

Each user story adds value independently:
- After US1: Deploy MVP ✅
- After US2: Deploy US1+US2 ✅
- After US3: Deploy US1+US2+US3 ✅

### 👥 Parallel Team Strategy

With multiple developers after Foundational:
- Developer A: User Story 1
- Developer B: User Story 2  
- Developer C: User Story 3
- Stories integrate seamlessly when complete

---

## Progress Tracking

**Emoji Legend:**
- ⏳ Not Started
- ⏰ In Progress
- ✅ Completed

**Update this file as you work:**
1. Change phase status emoji (⏳ → ⏰ → ✅)
2. Check off tasks as completed: `- [x]`
3. Add notes/learnings in the Notes sections
4. Update time estimates if needed

---

## Task Execution Tips

**Before starting a task:**
- Read the task description carefully
- Check if any [P] sibling tasks can run in parallel
- Verify prerequisites are complete

**While executing:**
- Follow file paths exactly as specified
- Match existing code patterns and conventions
- Test incrementally (don't wait until phase end)

**After completing a task:**
- Mark it complete: `- [x]`
- Commit if it's a logical unit
- Add any learnings to Notes section

**At checkpoints:**
- Validate the entire phase works as expected
- Test user story independently
- Get user approval before continuing

---

## Notes

**Key Conventions:**
- [P] = Can run in parallel (different files)
- [US1] = Belongs to User Story 1
- File paths should be exact and absolute when possible
- Commit after each task or logical grouping
- Stop at checkpoints to validate

**Remember:**
- Each user story should be independently completable
- Foundational phase blocks everything - don't skip it
- MVP = Just User Story 1 (P1)
- Add learnings to Notes sections as you discover them
```

### Step 7: Final Report

Present to the user:

1. **Task Summary:**
   - Total number of tasks: [X]
   - Number of phases: [Y]
   - Tasks per user story breakdown
   - Estimated total time: [Z hours]

2. **Files generated:**
   - Link to `plan.md`

3. **MVP Definition:**
   - Clearly state what constitutes the MVP (usually just P1 user story)
   - Estimated time to MVP: [X hours]

4. **Parallel Opportunities:**
   - Highlight which phases can be parallelized
   - Note tasks marked [P] within phases

5. **Next Steps:**
   - Suggest reviewing plan.md
   - Recommend starting with Phase 1 (Setup)
   - Mention checkpoints for validation
   - Ask if they want to proceed to `/work` or adjust tasks

## Guidelines

### Task Generation Guidelines

**Be Specific:**
- ❌ "Implement user authentication"
- ✅ "Implement JWT authentication middleware in src/auth/jwt.py"

**Be Actionable:**
- Each task should have a clear deliverable
- Include file paths when creating/modifying code
- Specify what needs to be tested

**Be Realistic:**
- Each task: 15-30 minutes
- Each phase: 2-4 hours
- Don't create micro-tasks (too granular)
- Don't create mega-tasks (too broad)

**Be Independent:**
- User stories should be independently completable
- Mark dependencies explicitly when they exist
- Prefer independence over tight coupling

### Time Estimation Guidelines

**Consider:**
- Complexity of the task
- Need for research/learning
- Testing and debugging time
- Integration complexity

**Default Estimates:**
- Simple CRUD: 2-3h per user story
- API endpoints: 3-4h per user story
- Complex business logic: 4-6h per user story
- UI components: 2-4h per user story

**Adjust based on:**
- Team experience with tech stack
- Code familiarity
- Availability of examples/patterns

### Documentation Guidelines

**Notes Sections:**
- Use for learnings during implementation
- Document unexpected issues
- Record decisions made on-the-fly
- Capture "gotchas" for future reference

**Status Tracking:**
- Update emojis as phases progress
- Check off tasks as completed
- Keep the file current (living document)

## Error Handling

**If specs.md is missing:**
```
ERROR: No specs found at ./specs/<feature_slug>/specs.md

Please run `/specify` first to create the feature specification.
```

**If architecture.md is missing:**
```
ERROR: No architecture found at ./specs/<feature_slug>/architecture.md

Please run `/plan` first to create the technical architecture.
```

**If architecture has open questions:**
```
WARNING: Architecture has unresolved open questions:
- [List them]

Resolve these before generating tasks, or tasks may be incomplete.
Proceed anyway? (yes/no)
```

**If no user stories found:**
```
ERROR: No user stories found in specs.md

User stories are required to organize tasks by priority.
Please update specs.md with user stories before running /tasks.
```

## Example Flow

```
User: /tasks

Claude:
I'll break down the architecture into actionable tasks.

[Loads specs.md and architecture.md from ./specs/001-api-endpoints/]

Found:
- 3 user stories (P1, P2, P3)
- Tech stack: Python 3.11 + FastAPI
- 5 new files to create, 2 files to modify

Generating tasks organized by user story...

✅ Tasks created!

📄 File: ./specs/001-api-endpoints/plan.md

📊 Summary:
- Total tasks: 24
- Phases: 6 (Setup, Foundational, 3 User Stories, Polish)
- Estimated time: ~16 hours
- MVP (US1 only): ~7 hours

🎯 MVP Definition:
User Story 1 (P1): "API Authentication" - Allows users to authenticate via JWT

🔀 Parallel Opportunities:
- Within Phase 2: Tasks T005-T007 can run in parallel
- After Phase 2: All user stories can proceed in parallel
- Within each user story: Model creation tasks marked [P]

💡 Recommended Approach:
1. Start with Phase 1 (Setup) - ~2h
2. Complete Phase 2 (Foundational) - ~3h ⚠️ This blocks everything
3. Focus on Phase 3 (US1 - MVP) - ~4h
4. Validate US1 works independently ✋
5. Then proceed to US2 and US3 in any order

Ready to start implementation? Run `/implement` when you're ready!
```
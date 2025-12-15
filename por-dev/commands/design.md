---
description: Execute technical planning and architecture design for the feature
argument-hint: [optional-context]
model: opus
---

# Design Command

This command takes the requirements from `specs/<feature_slug>/spec.md` and transforms them into a concrete technical architecture by solving all its open questions and implementation ambiguities.

## User Input

Arguments:
```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Prerequisites

Before running this command:
- ✅ Feature specs must exist at `./specs/<feature_slug>/specs.md`
- ✅ You should be on the correct feature branch
- ❌ Do NOT run this if specs are incomplete or have unresolved clarifications

## Execution Flow

### Step 0: Load Context

1. Identify the current feature by checking:
   - Current git branch name (if using git)
   - Ask user to specify feature if unclear

2. Load the specs file at `./specs/<feature_slug>/specs.md`

3. Verify specs are complete:
   - No `[NEEDS CLARIFICATION]` markers remaining
   - All user stories defined with priorities
   - Success criteria are clear
   - If incomplete, STOP and tell user to run `/discover` first

### Step 1: Technical Research

Based on the requirements in specs.md, identify what you need to research:

**Research Triggers:**
- New libraries/frameworks mentioned that you're not familiar with
- Integration with external services/APIs
- Unfamiliar patterns or architectural approaches
- Performance requirements that need specific solutions
- Security/compliance requirements needing validation

**Research Tools Available:**
- **WebSearch**: For general technical information, library documentation, best practices
- **Context7**: For library-specific documentation and examples
- **Perplexity**: For recent technical decisions, comparisons, and up-to-date practices

**Research Process:**
1. List what needs research (be specific: "How to implement rate limiting in xxxx framework" not "research rate limiting")
2. For each item, use the appropriate tool and synthesize findings
3. Document key decisions as you research (don't wait until the end)

**Important:** Don't research things you already know well. Focus on gaps in your knowledge that would affect implementation quality.

### Step 2: Codebase Analysis

Understand the existing codebase structure and patterns:

**Discovery Questions:**
- What is the existing tech stack? (language, frameworks, key libraries)
- What are the established patterns? (file structure, naming conventions, architectural patterns)
- Where do similar features live in the codebase?
- What existing components can be reused?
- What are the testing patterns?

**Analysis Tools:**
- Use available MCP tools to search and read code
- Look for similar features to understand patterns
- Identify the main directories and their purposes
- Note any conventions (naming, structure, imports)

**Tips:**
- Search for keywords from your specs to find related code
- Look at recent commits/PRs for active patterns
- Check for README files explaining structure
- Identify configuration files and their purposes

### Step 3: Architecture Design

Now design the architecture for this feature:

**What to Define:**

1. **Component Structure:**
   - What new files/modules need to be created?
   - What existing files need modification?
   - How do components relate to each other?
   - Where does each user story map to in the code?

2. **Technical Decisions:**
   - Which libraries/frameworks to use (with rationale)
   - Which patterns to follow (with justification)
   - How to handle edge cases
   - Performance considerations
   - Security considerations

3. **Integration Points:**
   - How does this feature integrate with existing code?
   - What APIs/interfaces need to be defined?
   - What side effects or dependencies exist?

4. **Data Flow:**
   - How does data move through the system?
   - What transformations occur?
   - Where is state managed?

**Architecture Principles:**
- Follow existing codebase patterns (don't reinvent)
- Prefer simplicity over cleverness
- Consider testability
- Think about maintenance and debugging
- Address non-functional requirements (performance, security, etc.)

### Step 4: Generate architecture.md

Create `./specs/<feature_slug>/architecture.md` with this structure:

```markdown
# Architecture: [FEATURE NAME]

**Feature**: [Feature name]
**Date**: [Current date]
**Branch**: [feature branch name]
**Specs**: [Link to specs.md]

## Summary

[2-3 sentence overview: what we're building and the core technical approach]

## Technical Context

**Language/Stack**: [e.g., Python 3.11 + FastAPI, TypeScript + React, etc.]
**Key Dependencies**: [Libraries/frameworks being used or added]
**Storage**: [Database, files, cache - if applicable]
**Testing**: [Testing approach and tools]
**Platform**: [Where this runs - server, browser, mobile, etc.]

## Technical Decisions

### Decision 1: [Decision Title]

**What**: [What was decided]
**Why**: [Rationale for this decision]
**Alternatives Considered**: [What else was considered and why rejected]
**Trade-offs**: [What we gain vs what we lose]

[Repeat for each major technical decision]

## Architecture Overview

[High-level description of how the system works before and after this change]

### Component Structure

**New Files/Modules:**
```
path/to/new/file.ext - Purpose and responsibility
path/to/another/file.ext - Purpose and responsibility
```

**Modified Files:**
```
path/to/existing/file.ext - What changes and why
```

### Component Relationships

[Describe how components interact - can use bullet points or simple diagrams]

### Data Flow

[Describe how data moves through the system for key user stories]

## Implementation Approach

### User Story Mapping

**US1 (P1): [Story Title]**
- Files involved: [list]
- Key components: [list]
- Dependencies: [if any]
- Testing approach: [how to verify]

**US2 (P2): [Story Title]**
[Same structure]

### File Structure

```
[Show the relevant part of the directory structure with new/modified files marked]

project/
├── src/
│   ├── [existing]/
│   ├── [new-module]/          # NEW
│   │   ├── __init__.py       # NEW
│   │   └── core.py           # NEW
│   └── [existing-file.py]    # MODIFIED
└── tests/
    └── test_new_feature.py   # NEW
```

## Integration Points

[Describe how this feature integrates with existing code]

- **Integration with [Component A]**: [How and why]
- **New APIs/Interfaces**: [What's exposed]
- **Dependencies**: [What this feature depends on]

## Technical Constraints

- [Constraint 1: e.g., Must maintain backward compatibility with v1 API]
- [Constraint 2: e.g., Response time must be <200ms]
- [Constraint 3: e.g., Must work offline]

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| [Risk description] | [High/Med/Low] | [How we'll handle it] |

## Open Questions

[If any technical questions remain that need resolution before implementation]

- [ ] Question 1
- [ ] Question 2

## Next Steps

1. Review this architecture with stakeholders
2. Resolve any open questions
3. Run `/tasks` to break down into implementation tasks
```

### Step 5: Generate contracts.md (Optional)

**Only create this file if the feature involves:**
- External APIs or services
- Data models with validation rules
- Integration contracts with other systems
- Complex request/response formats

If needed, create `./specs/<feature_slug>/contracts.md`:

```markdown
# Contracts: [FEATURE NAME]

**Feature**: [Feature name]
**Date**: [Current date]

## Data Models

### [Entity Name]

**Purpose**: [What this represents]

**Attributes:**
- `field_name` (type): Description, validation rules
- `another_field` (type): Description

**Example:**
```json
{
  "field_name": "value",
  "another_field": 123
}
```

[Repeat for each entity]

## API Contracts

### [Endpoint Name]

**Endpoint**: `POST /api/path`
**Purpose**: [What this does]

**Request:**
```json
{
  "param1": "value",
  "param2": 123
}
```

**Response (Success - 200):**
```json
{
  "result": "success",
  "data": {}
}
```

**Response (Error - 400):**
```json
{
  "error": "Error message"
}
```

**Validation Rules:**
- param1: Required, must be non-empty string
- param2: Required, must be positive integer

[Repeat for each endpoint/contract]

## Integration Contracts

[If integrating with external services, document the contract/expectations]

### [Service Name]

**What we expect:**
- Input format: [describe]
- Output format: [describe]
- Error handling: [describe]

## State Transitions

[If there are complex state machines or workflows, document them]

[Current State] --[Action]--> [New State]
```

### Step 6 Final Review & Report

Present to the user:

1. **Summary of architecture:**
   - Core technical approach
   - Major decisions made
   - Key components involved

2. **Files generated:**
   - Link to `architecture.md`
   - Link to `contracts.md` (if created)
   - Agent file updated (CLAUDE.md or AGENTS.md)

3. **Key decisions to review:**
   - List the most important architectural decisions
   - Ask if user wants to discuss or adjust anything

4. **Next steps:**
   - Suggest reviewing the architecture.md
   - Ask if they want to proceed to `/tasks` or iterate on architecture
   - Mention any open questions that need resolution

## Guidelines

### Research Guidelines

**When to use each tool:**
- **WebSearch**: General information, official docs, Stack Overflow patterns
- **Context7**: Library-specific docs, API references, code examples
- **Perplexity**: Recent best practices, "what's the latest way to...", comparisons

**How to research efficiently:**
- Be specific in search queries
- Look for official documentation first
- Cross-reference multiple sources for important decisions
- Document your findings as you go (don't wait until end)

### Architecture Guidelines

**Keep it pragmatic:**
- Don't over-engineer
- Follow existing patterns in the codebase
- Consider the team's skill level and familiarity
- Choose boring, proven technologies over shiny new ones

**Think about the future:**
- How easy is this to test?
- How easy is this to debug?
- How easy is this to extend?
- How easy is this to maintain?

**Address non-functionals:**
- Performance requirements from specs
- Security considerations
- Accessibility needs (if applicable)
- Error handling and resilience

### Documentation Guidelines

**Write for humans:**
- Use clear, simple language
- Explain WHY, not just WHAT
- Include examples where helpful
- Use diagrams/ASCII art if it helps clarity

**Be concise but complete:**
- Every section should add value
- Remove boilerplate that doesn't apply
- Focus on decisions and trade-offs
- Link to external docs rather than copying them

**Make it actionable:**
- Anyone should be able to implement from this
- Include enough detail for task breakdown
- Highlight dependencies and prerequisites
- Call out risks and gotchas

## Error Handling

**If specs.md is missing:**
```
ERROR: No specs found at ./specs/<feature_slug>/specs.md

Please run `/discover` first to create the feature specification.
```

**If specs have unresolved clarifications:**
```
ERROR: Specs contain [NEEDS CLARIFICATION] markers:
- [List the markers]

Please resolve these before proceeding to architecture.
You can:
1. Run `/discover` again to address clarifications
2. Manually edit specs.md to resolve them
```

Once you are done and the architecture is complete, run the /plan command. 

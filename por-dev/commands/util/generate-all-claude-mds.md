# Generate CLAUDE.md Documentation

Generate CLAUDE.md files to provide AI coding assistants with codebase context.

## Purpose

CLAUDE.md files exist solely to help AI developers understand the codebase when working on files in each directory. They are NOT:
- Human documentation (README, docs/)
- Tutorials or guides
- API references
- Installation instructions

They ARE:
- Contextual maps for AI navigating the codebase
- Integration guides showing how modules connect
- Pattern documentation for consistent code generation
- Gotcha lists preventing common AI mistakes

## What to Include

### Essential Content
- **Module purpose**: One sentence explaining what this code does
- **File catalog**: What each file contains and its role
- **Key patterns**: How things are done here (naming, structure, error handling)
- **Integration points**: How this module connects to others
- **Gotchas**: Traps that would cause AI to generate incorrect code
- **Anti-patterns**: What NOT to do in this codebase

### Exclude
- Version numbers, dates, "last updated"
- Installation/setup instructions
- CLI usage examples (unless explaining internal implementation)
- Human-readable tutorials
- Badges, links to external docs
- Anything a human needs but code context doesn't require

## File Template

```markdown
# [Module Name]

[One sentence: what this module does]

## Files

- **`file.py`**: [What it contains, key classes/functions]
- **`other.py`**: [What it contains]

## Patterns

- **[Pattern name]**: [How it's implemented here, when to use it]

## Integration

- Imports from: `module.x`, `module.y`
- Used by: `other_module.z`
- Data flow: [Brief description of how data moves through]

## Gotchas

- [Specific trap that would cause incorrect code generation]
- [Another gotcha]

## When Adding Code

- [Constraint or convention to follow]
- [Another convention]
```

## Hierarchy Rules

### Execution Order: Leaves → Trunk

Build from deepest directories up to root:

1. **Leaf files** (deepest): Document every file, all patterns, all gotchas
2. **Parent directories**: Focus on how children integrate, reference children for details
3. **Root** (last): High-level map only, everything else in children

### Content Distribution

| Level | Contains | Avoids |
|-------|----------|--------|
| Leaf | All file details, all patterns, all gotchas | - |
| Mid | Integration between children, cross-cutting patterns | Repeating child content |
| Root | Directory map, project-wide patterns only | Implementation details |

### Reference Pattern

Parent files should say:
```markdown
## Submodules

- **[child/](child/CLAUDE.md)**: [What it handles]

See child CLAUDE.md for implementation details.
```

NOT duplicate the child's content.

## Anti-Patterns

**DON'T include:**
- `## Quick Start` or `## Getting Started`
- `## Installation`
- `## Usage Examples` (unless showing internal patterns)
- `## Version` or version numbers
- `## Contributing` or `## License`
- `Current: v1.2.0` or any version strings
- `Last Updated: January 2025`
- Links to external documentation
- Badges or status indicators

**DON'T duplicate:**
- Same file description in parent and child
- Same gotcha at multiple levels
- Same pattern explanation repeated

**DO include:**
- What would cause AI to write incorrect code
- How modules actually connect (not how users use them)
- Naming conventions that must be followed
- Edge cases in the implementation

## Placement Strategy

Create CLAUDE.md in:
- Project root (always)
- Directories with substantial implementation code (3+ non-trivial files)
- Directories with complex internal logic worth documenting

Skip CLAUDE.md for:
- **Pass-through directories**: Folders that just contain `__init__.py` + subfolders (e.g., `src/package/` when all code is in `src/package/core/`, `src/package/utils/`)
- **Simple re-export levels**: If a directory only has `__init__.py` that re-exports from children, skip it - document in root instead
- Single-file directories
- Generated code
- Pure configuration directories
- `node_modules`, `__pycache__`, `.git`, etc.

### Pass-Through Directory Rule

If a directory structure looks like:
```
src/mypackage/
├── __init__.py        # Just re-exports
├── one_simple_file.py # Could be mentioned in root
├── core/              # Has real implementation
├── utils/             # Has real implementation
└── api/               # Has real implementation
```

Then skip `src/mypackage/CLAUDE.md`. Instead:
- Root CLAUDE.md references `core/`, `utils/`, `api/` directly
- Mention `__init__.py` and `one_simple_file.py` briefly in root

This avoids CLAUDE.md files that are just "see children for details".

## Execution

1. Map directory structure, identify documentation points
2. Start with deepest directories (leaves)
3. Work up, referencing children instead of repeating
4. Finish with root as navigation hub
5. Review: remove any human-oriented content that crept in

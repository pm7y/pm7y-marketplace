# Plugin Improvements Design

**Date:** 2026-01-22
**Status:** Approved

## Context

This marketplace plugin (`pm7y-claude-code`) is a personal productivity toolkit for a mixed-stack environment (.NET backend + React/TypeScript frontend). The main pain point is Claude over-complicating code, particularly CSS/SCSS, and ignoring existing patterns in the codebase.

### Mental Model

- **Skills** = Claude auto-invokes based on context
- **Commands** = User forces invocation when they want deterministic control

Commands are thin wrappers around skills - this is intentional, not redundant.

## Changes

### 1. Cleanup: Commit Pending Changes

The git status shows:
- `pm7y-ralph-tasks` agent and command deleted (consolidated into `pm7y-ralph-planner`)
- Several files modified

**Action:** Commit these changes with a clear message about the Ralph consolidation.

### 2. Cleanup: Rewrite README.md

Current README references:
- `plugins/<plugin-name>/agents/` structure that doesn't exist
- Old skill paths like `/skills/diagram-skills/mermaid-diagram`
- Outdated Available Skills/Agents tables

**Action:** Rewrite to reflect actual structure:
```
pm7y-marketplace/
├── .claude-plugin/
│   └── plugin.json
├── agents/
├── commands/
├── skills/
└── docs/
```

### 3. New Skill: pm7y-scss-patterns

**Purpose:** Discover existing SCSS/CSS patterns *before* Claude writes new styles.

**Triggers:** Claude detects it's about to write CSS/SCSS.

**Discovers:**
- Existing mixins and their purposes
- Variables (colors, spacing, typography)
- Utility classes
- Component patterns
- Design tokens

**Output:** Concise "use these, don't reinvent" summary that Claude references while writing.

**Tools:** Read, Glob, Grep

### 4. New Skill: pm7y-component-patterns

**Purpose:** Discover existing React component patterns *before* Claude writes new components.

**Triggers:** Claude detects it's about to create a React component.

**Discovers:**
- Component file structure conventions
- State management approach (hooks, context, etc.)
- Naming conventions
- Common patterns (how forms are built, how lists are rendered, etc.)
- Prop patterns and TypeScript conventions

**Output:** Concise pattern summary Claude references while writing.

**Tools:** Read, Glob, Grep

### 5. New Skill: pm7y-simplify

**Purpose:** Explicitly simplify code. Counteracts Claude's tendency to over-engineer.

**Triggers:** User invokes via command, or Claude detects request to simplify/reduce complexity.

**Approach:**
- Remove unnecessary abstractions
- Inline single-use functions/variables
- Reduce nesting depth
- Prefer direct solutions over "flexible" ones
- Apply YAGNI ruthlessly

**Constraints:**
- Must not change external behavior
- Must preserve all functionality
- Focus on the specific code mentioned, don't expand scope

**Tools:** Read, Edit

### 6. New Commands (Wrappers)

Each new skill gets a matching command for deterministic invocation:

| Command | Invokes Skill | Purpose |
|---------|---------------|---------|
| `/pm7y-scss-patterns` | pm7y-scss-patterns | Force SCSS pattern discovery |
| `/pm7y-component-patterns` | pm7y-component-patterns | Force React pattern discovery |
| `/pm7y-simplify` | pm7y-simplify | Force code simplification |

### 7. Enhanced: pm7y-css-review

**Current:** Reviews CSS/SCSS for over-specificity, missed reuse, over-engineered abstractions.

**Enhancement:** Add explicit "existing pattern exists" violation detection:
- "You created `@mixin flex-center` but `@mixin center-flex` already exists"
- "You defined `$primary-blue` but `$color-primary` serves the same purpose"
- "This utility class duplicates `.flex-center` from utilities.scss"

**Integration:** Can invoke `pm7y-scss-patterns` internally to discover what exists, then compare against new code.

## Organization

Keep flat structure for both commands and skills:

```
commands/
├── pm7y-scss-patterns.md
├── pm7y-component-patterns.md
├── pm7y-simplify.md
├── pm7y-css-review.md
└── ... (existing commands)

skills/
├── pm7y-scss-patterns/
│   └── SKILL.md
├── pm7y-component-patterns/
│   └── SKILL.md
├── pm7y-simplify/
│   └── SKILL.md
├── pm7y-css-review/
│   └── SKILL.md (enhanced)
└── ... (existing skills)
```

Use naming conventions (`pm7y-patterns-*`, `pm7y-review-*`) if grouping becomes desirable later.

## Workflow

After these changes, the CSS workflow becomes:

```
User asks for CSS work
  → Claude auto-invokes pm7y-scss-patterns (discovers what exists)
  → Claude writes CSS using discovered patterns
  → pm7y-css-review validates (catches any misses)
```

Same pattern for React components with `pm7y-component-patterns`.

## Implementation Order

1. Commit pending git changes (quick cleanup)
2. Rewrite README.md (quick cleanup)
3. Create pm7y-scss-patterns skill + command
4. Create pm7y-component-patterns skill + command
5. Create pm7y-simplify skill + command
6. Enhance pm7y-css-review with pattern violation detection
7. Bump version in plugin.json

## Version

Per VERSIONING.md, this is a minor version bump (new features):
- Current: 1.24.0
- After: 1.25.0

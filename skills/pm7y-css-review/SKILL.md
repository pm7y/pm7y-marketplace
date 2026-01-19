---
name: pm7y-css-review
description: Reviews CSS/SCSS changes in the current branch for over-specificity, missed reuse opportunities, and over-engineered abstractions. Outputs prioritized tasks to STYLE_TASKS.md. Use when reviewing CSS changes before commit or PR, or to audit existing styles. Supports @file syntax to target specific files.
allowed-tools: Read, Write, Grep, Glob, Bash
---

# CSS/SCSS Review Skill

Reviews CSS/SCSS changes for unnecessary complexity and missed reuse opportunities.

---

## Overview

This skill analyzes CSS/SCSS files for three categories of issues:

- **Over-specificity** - Complex selectors that could be simpler
- **Missing reuse** - New styles that duplicate existing utilities
- **Over-engineered abstractions** - Unnecessary mixins/variables/extends

**When to use:**

- Before committing CSS/SCSS changes
- During PR review of style changes
- Auditing existing stylesheets for cleanup
- After rapid UI development to assess CSS debt

---

## Usage

```
/pm7y-css-review                      # Review all CSS/SCSS changes in branch
/pm7y-css-review @path/to/file.scss   # Review specific file only
```

---

## Review Process

### Step 1: Determine Scope

Parse arguments to determine which files to review:

**If @file argument provided:**
- Extract the file path after the @ symbol
- Verify the file exists and is CSS/SCSS
- Review only that file

**If no arguments (default):**
- Run `git diff main...HEAD --name-only` to find files changed in branch
- Also check `git diff --name-only` for staged/unstaged changes
- Filter to only `.css` and `.scss` files
- If no CSS/SCSS files changed, report "No CSS/SCSS changes found" and stop

### Step 2: Build Style Inventory

Scan the project to understand existing styles:

**Find all style files:**
```bash
# Find all CSS and SCSS files in project (exclude node_modules, dist, build)
find . -type f \( -name "*.css" -o -name "*.scss" \) \
  -not -path "*/node_modules/*" \
  -not -path "*/dist/*" \
  -not -path "*/build/*" \
  -not -path "*/.next/*"
```

**Extract existing patterns:**

For each style file, identify and record:
- **CSS classes** - All `.class-name` selectors
- **SCSS variables** - All `$variable-name` definitions
- **SCSS mixins** - All `@mixin mixin-name` definitions
- **Common property-value pairs** - Frequently used declarations

Store this inventory mentally for comparison during analysis.

### Step 3: Detect Frameworks

Check for CSS frameworks to understand available utilities:

**Tailwind CSS:**
- Check for `tailwind.config.js` or `tailwind.config.ts`
- Look for `@tailwind` directives in CSS files
- If found, note common utilities: flex, grid, spacing (p-*, m-*), colors, etc.

**Bootstrap:**
- Check for Bootstrap in `package.json` dependencies
- Look for Bootstrap imports in SCSS files
- If found, note utility classes: d-flex, justify-content-*, text-*, etc.

**Custom utility systems:**
- Look for files named `utilities.css`, `helpers.scss`, `_utils.scss`
- Identify utility class naming patterns

### Step 4: Analyze Changed Files

For each file in scope, read the content and check for issues:

#### Over-specificity Detection

| Issue | Pattern | Severity |
|-------|---------|----------|
| Deep nesting | Selectors with > 3 levels (e.g., `.a .b .c .d`) | Medium |
| ID selectors | `#id` in selectors | Medium |
| `!important` | Any `!important` declaration | Medium |
| Qualified selectors | Element + class (e.g., `div.button`) | Low |
| Over-qualified | Multiple classes chained (e.g., `.btn.btn-primary.btn-large`) | Low |

#### Missing Reuse Detection

| Issue | Pattern | Severity |
|-------|---------|----------|
| Duplicate utility | Declaration matches existing utility class | High |
| Framework duplicate | Declaration available as framework utility | High |
| Repeated values | Magic numbers used instead of variables | Medium |
| Similar blocks | Near-identical declaration blocks elsewhere | Medium |

#### Over-engineered Abstraction Detection

| Issue | Pattern | Severity |
|-------|---------|----------|
| Single-use mixin | `@mixin` with only one `@include` | Medium |
| Single-use variable | `$variable` used only once (except colors) | Medium |
| `@extend` usage | Any use of `@extend` | Low |
| Deep SCSS nesting | > 3 levels of SCSS nesting | Low |

### Step 5: Generate STYLE_TASKS.md

Create the output file with this exact format:

```markdown
# CSS/SCSS Review Tasks

*Generated: [YYYY-MM-DD]*
*Branch: [current branch name]*
*Files reviewed: [count]*

---

## [filepath]

### High Priority

- [ ] **#N** (Line X): [Issue description]. [Recommendation].

### Medium Priority

- [ ] **#N** (Line X): [Issue description]. [Recommendation].

### Low Priority

- [ ] **#N** (Line X): [Issue description]. [Recommendation].

---

## [next filepath]

...
```

**Formatting rules:**
- Task numbers are sequential across entire document (not per file)
- Group tasks by file, then by severity within each file
- Omit empty priority sections (don't show "High Priority" with no items)
- Include the specific line number(s) where the issue occurs
- Provide concrete recommendation with existing class/variable names when applicable

### Step 6: Report Summary and Stop

After writing STYLE_TASKS.md, output a brief summary:

```
CSS Review Complete

Files reviewed: N
Issues found: X (Y high, Z medium, W low)

Tasks written to STYLE_TASKS.md
```

Then STOP. Do not attempt to fix any issues.

---

## Issue Examples

### Over-specificity Examples

**Deep nesting:**
```scss
// Bad - 4 levels
.header .nav .menu .item a { color: blue; }

// Recommendation: Simplify to
.header-nav-link { color: blue; }
```

**ID selector:**
```scss
// Bad
#main-content .sidebar { width: 300px; }

// Recommendation: Use class instead
.main-content .sidebar { width: 300px; }
```

**!important:**
```scss
// Bad
.modal { z-index: 1000 !important; }

// Recommendation: Fix specificity issue at source
```

### Missing Reuse Examples

**Duplicate utility:**
```scss
// Bad - if .flex-center exists
.card { display: flex; justify-content: center; align-items: center; }

// Recommendation: Use existing .flex-center class
```

**Framework duplicate (Tailwind):**
```scss
// Bad - when using Tailwind
.button { margin-left: auto; margin-right: auto; }

// Recommendation: Use Tailwind's mx-auto class
```

### Over-engineered Examples

**Single-use mixin:**
```scss
// Bad
@mixin card-shadow {
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.card { @include card-shadow; }

// Recommendation: Inline the styles
.card { box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
```

**Single-use variable:**
```scss
// Bad
$card-padding: 16px;
.card { padding: $card-padding; }

// Recommendation: Use value directly or existing spacing variable
```

---

## Critical Rules

### Rule 1: Inventory Before Analysis

ALWAYS build the style inventory before analyzing changed files. Without knowing what exists, you cannot identify reuse opportunities.

### Rule 2: Framework Awareness

ALWAYS check for CSS frameworks. Tailwind and Bootstrap provide extensive utilities that should be preferred over custom CSS.

### Rule 3: Line Numbers Required

EVERY issue MUST include specific line number(s). Vague references like "in this file" are not acceptable.

### Rule 4: Actionable Recommendations

EVERY issue MUST include a concrete recommendation. Name specific existing classes, variables, or utility names when suggesting reuse.

### Rule 5: Stop After Output

After writing STYLE_TASKS.md, STOP. Do not modify any CSS files. Do not attempt to fix issues. The user will decide what to fix.

---

## Validation Checklist

Before finalizing:

- [ ] Parsed arguments correctly (@file or branch diff)
- [ ] Built style inventory from all project CSS/SCSS files
- [ ] Checked for Tailwind, Bootstrap, or custom utility systems
- [ ] Analyzed all files in scope
- [ ] Every issue has line number(s)
- [ ] Every issue has concrete recommendation
- [ ] Task numbers are sequential across document
- [ ] Tasks grouped by file, then by severity
- [ ] STYLE_TASKS.md written to project root
- [ ] Summary output provided
- [ ] DID NOT attempt to fix any issues

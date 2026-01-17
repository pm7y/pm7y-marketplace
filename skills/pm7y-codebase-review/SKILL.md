---
name: pm7y-codebase-review
description: Reviews a codebase for style consistency, patterns, idioms, and adherence to KISS, DRY, POLA, and YAGNI principles. Automatically detects languages/frameworks present and applies appropriate analysis. Use when performing code audits, before major refactoring, or to establish coding standards. Outputs timestamped findings to CODEBASE_REVIEW.md with actionable tasks.
allowed-tools: Read, Write, Edit, Grep, Glob, Task, WebFetch, mcp__context7__resolve-library-id, mcp__context7__query-docs
---

# Codebase Review Skill

Reviews codebases for consistency, patterns, and adherence to software engineering principles.

---

## Overview

This skill performs comprehensive codebase analysis focusing on:

- **Style & pattern consistency** - Implicit and explicit conventions
- **Language idioms** - Following best practices for each language
- **Principle adherence** - KISS, DRY, POLA, YAGNI
- **Complexity reduction** - Identifying unnecessary complexity
- **Duplication detection** - Finding repeated code patterns

**When to use:**

- Periodic codebase health checks
- Before major refactoring efforts
- Establishing or documenting coding standards
- Onboarding new team members to codebase conventions
- After rapid feature development to assess technical debt

---

## Review Depth Options

The review supports different depth levels via command arguments:

| Depth | Scope | Use When |
|-------|-------|----------|
| `--quick` | Sample key files, high-level patterns | Quick health check |
| `--standard` | Representative files from each area | Regular reviews (default) |
| `--comprehensive` | Full codebase scan, detailed analysis | Before major refactoring |

---

## Review Process

### Step 1: Study Documentation

Before analyzing code, read and understand:

1. **README.md** - Project overview, architecture decisions
2. **CLAUDE.md** - Codebase conventions (if exists)
3. **CONTRIBUTING.md** - Contribution guidelines
4. **Architecture docs** - Any docs in `/docs` or similar
5. **.editorconfig** - Formatting preferences
6. **Linter configs** - ESLint, Prettier, StyleCop, .pylintrc, .rubocop.yml, etc.

Use these to understand the project's stated conventions before comparing to actual code.

### Step 2: Detect Languages and Frameworks

Scan the codebase to identify all languages and frameworks present. Look for:

| Language/Framework | Indicators |
|-------------------|------------|
| **JavaScript/TypeScript** | `package.json`, `.js`, `.ts`, `.jsx`, `.tsx` files |
| **React** | React in package.json dependencies, JSX/TSX files, hooks |
| **Node.js** | `package.json` with node dependencies, no browser frameworks |
| **C#/.NET** | `.sln`, `.csproj`, `.cs` files, `Program.cs` |
| **Python** | `requirements.txt`, `pyproject.toml`, `setup.py`, `.py` files |
| **Go** | `go.mod`, `go.sum`, `.go` files |
| **Rust** | `Cargo.toml`, `Cargo.lock`, `.rs` files |
| **Java** | `pom.xml`, `build.gradle`, `.java` files |
| **Ruby** | `Gemfile`, `.rb` files, `Rakefile` |
| **PHP** | `composer.json`, `.php` files |
| **Kotlin** | `build.gradle.kts`, `.kt` files |
| **Swift** | `Package.swift`, `.swift` files, `.xcodeproj` |

**Record detected languages** for use in the report structure and to guide analysis focus.

### Step 3: Analyze Code by Language

For each detected language, apply language-specific analysis. Use Context7 to look up current best practices for the detected languages/frameworks.

---

#### React/TypeScript Analysis

*Apply when React/TypeScript detected.*

**Component Patterns:**
- Functional vs class components (prefer functional)
- Hook usage patterns (custom hooks, composition)
- State management approach (local, context, external)
- Component file structure (co-location, barrel exports)

**TypeScript Idioms:**
- Type inference vs explicit types
- Interface vs type aliases
- Utility types usage
- Strict mode adherence

**Style Consistency:**
- Naming conventions (PascalCase components, camelCase functions)
- Import ordering and grouping
- Export patterns (default vs named)
- File naming conventions

**React Best Practices:**
- Prop drilling vs composition
- useEffect dependencies and cleanup
- Memoization (useMemo, useCallback, React.memo)
- Error boundaries
- Accessibility patterns

---

#### C#/.NET Analysis

*Apply when C#/.NET detected.*

**Architecture Patterns:**
- Layer separation (Controllers, Services, Repositories)
- Dependency injection usage
- Interface abstractions
- CQRS or traditional patterns

**C# Idioms:**
- Nullable reference types
- Async/await patterns
- LINQ usage (query vs method syntax)
- Record types vs classes
- Pattern matching

**Style Consistency:**
- Naming conventions (PascalCase, _camelCase for fields)
- File organization (one class per file)
- Namespace structure
- XML documentation

**.NET Best Practices:**
- Exception handling patterns
- Configuration management
- Logging patterns
- Validation approaches

---

#### Other Languages (Dynamic Analysis)

*Apply when languages other than React/TypeScript or C#/.NET are detected.*

For languages not covered above, use Context7 to look up current best practices:

1. **Identify the language** - Use the detection table from Step 2
2. **Query Context7** - Look up idioms, style guides, and best practices for that language
3. **Apply universal checks** - See "Generic Analysis" below
4. **Focus on consistency** - Even without deep language knowledge, inconsistencies are identifiable

**Common analysis patterns for any language:**
- Code organization and module structure
- Naming convention consistency
- Error/exception handling patterns
- Testing patterns and coverage
- Dependency management
- Documentation quality

---

#### Generic Analysis (Any Language)

*Always apply these checks regardless of language.*

**Universal Patterns:**
- Consistent naming within the codebase
- File/directory organization patterns
- Comment quality and accuracy
- Test organization and coverage
- Configuration management
- Environment handling
- Logging patterns
- Error handling consistency

### Step 5: Apply Engineering Principles

Evaluate code against each principle:

**KISS (Keep It Simple, Stupid):**
- Overly complex solutions for simple problems
- Unnecessary abstraction layers
- Convoluted control flow
- Over-engineered patterns

**DRY (Don't Repeat Yourself):**
- Duplicated code blocks
- Similar functions that could be generalized
- Repeated configuration
- Copy-pasted logic with minor variations

**POLA (Principle of Least Astonishment):**
- Surprising function behaviors
- Misleading names
- Unexpected side effects
- Non-obvious API designs

**YAGNI (You Aren't Gonna Need It):**
- Unused code or features
- Premature abstractions
- Over-configurable systems
- Speculative generality

See [principles-reference.md](principles-reference.md) for detailed examples.

### Step 6: Use Context7 for Documentation

When evaluating idioms or best practices, use Context7 MCP tools to look up:

- Current React patterns and hooks documentation
- C# language feature documentation
- .NET framework best practices
- Package-specific usage patterns

This ensures recommendations align with current (not outdated) best practices.

### Step 7: Generate Report

Create or append to `CODEBASE_REVIEW.md` with this structure:

```markdown
## Review: [Date and Time]

### Summary

Brief overview of findings (2-3 sentences).

### Detected Technologies

- **Languages**: [List detected languages, e.g., TypeScript, Python, Go]
- **Frameworks**: [List detected frameworks, e.g., React, FastAPI, Gin]
- **Build Tools**: [List build tools, e.g., npm, pip, cargo]

### [Language/Area] Analysis

*Repeat this section for each detected language or logical area (e.g., "TypeScript/React Analysis", "Python Backend Analysis", "Go Services Analysis").*

#### Patterns Identified
- [Pattern 1]: Description and where used
- [Pattern 2]: Description and where used

#### Inconsistencies Found
- [Issue 1]: Description, files affected, recommendation
- [Issue 2]: Description, files affected, recommendation

#### Principle Violations
- **[PRINCIPLE]**: Description of violation
  - Files: `file1.ext`, `file2.ext`
  - Recommendation: How to fix

### Cross-Cutting Concerns

*Issues that span multiple languages or the entire codebase.*

- [Issue]: Description and recommendation

### Actionable Tasks

Priority-ordered list of improvements:

- [ ] **High**: [Task description] - Files: [list]
- [ ] **High**: [Task description] - Files: [list]
- [ ] **Medium**: [Task description] - Files: [list]
- [ ] **Low**: [Task description] - Files: [list]

### Positive Observations

Things done well that should be maintained:
- [Good practice 1]
- [Good practice 2]
```

---

## Critical Rules

### Rule 1: Documentation First

ALWAYS read available documentation before analyzing code. Understanding stated conventions prevents false positives.

### Rule 2: Separate Analyses by Language/Area

ALWAYS analyze different languages and logical areas separately. Each language has its own idioms, patterns, and best practices.

### Rule 3: Context Over Rules

Apply principles pragmatically. Small amounts of duplication may be acceptable. Simple code may look "boring" but is often correct. Consider the context before flagging violations.

### Rule 4: Actionable Output

Every finding MUST have:
- Specific files affected
- Clear description of the issue
- Concrete recommendation for improvement
- Priority level (High/Medium/Low)

### Rule 5: Preserve Existing Content

When writing to `CODEBASE_REVIEW.md`:
- Create the file if it doesn't exist
- Append new review as a new section
- Never overwrite previous reviews
- Use clear date/time headers

---

## Output Format

The report MUST be written to `CODEBASE_REVIEW.md` in the repository root.

**File structure:**
```markdown
# Codebase Review Log

This file contains periodic codebase reviews with findings and actionable improvements.

---

## Review: 2025-01-17 14:30 UTC

[Review content...]

---

## Review: 2025-01-10 09:15 UTC

[Previous review content...]
```

---

## Validation Checklist

Before finalizing the review:

- [ ] Read all available documentation (README, CLAUDE.md, etc.)
- [ ] Detected and documented all languages/frameworks present
- [ ] Analyzed patterns and idioms for each detected language
- [ ] Applied KISS principle evaluation
- [ ] Applied DRY principle evaluation
- [ ] Applied POLA principle evaluation
- [ ] Applied YAGNI principle evaluation
- [ ] Used Context7 for current best practice verification (if needed)
- [ ] All findings have specific file references
- [ ] All findings have actionable recommendations
- [ ] Tasks are prioritized (High/Medium/Low)
- [ ] Positive observations included
- [ ] Report appended to CODEBASE_REVIEW.md (not overwritten)

---

## Examples

### Example: DRY Violation

**Finding:**
```markdown
#### Principle Violations
- **DRY**: Duplicate validation logic
  - Files: `UserController.cs:45-60`, `AdminController.cs:32-47`
  - Issue: Same email validation regex and error handling duplicated
  - Recommendation: Extract to `ValidationHelpers.ValidateEmail()` method
```

### Example: KISS Violation

**Finding:**
```markdown
#### Principle Violations
- **KISS**: Over-abstracted service layer
  - Files: `Services/UserService.cs`, `Services/IUserService.cs`, `Services/UserServiceBase.cs`, `Services/UserServiceExtensions.cs`
  - Issue: Single responsibility split across 4 files with unnecessary base class and extensions
  - Recommendation: Consolidate to single `UserService.cs` with interface
```

### Example: Pattern Inconsistency

**Finding:**
```markdown
#### Inconsistencies Found
- **Hook naming**: Mixed conventions
  - Files: `useAuth.ts`, `useFetchData.ts`, `UseCart.tsx`
  - Issue: Inconsistent casing (camelCase vs PascalCase) and file extensions (.ts vs .tsx for non-component hooks)
  - Recommendation: Standardize to camelCase with .ts extension for hooks
```

---

## Reference Documents

For detailed guidance on principles and patterns:

- [principles-reference.md](principles-reference.md) - Detailed KISS, DRY, POLA, YAGNI examples

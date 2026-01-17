# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## ⚠️ Critical Context

**This repository is a private Claude Code marketplace plugin.** It is installed as a plugin via Claude Code's marketplace feature and provides skills, commands, and agents that are invoked in OTHER codebases where this repo's source code is NOT available.

When making changes, always consider:
- **Skills/commands/agents must be self-contained** - they cannot reference local files from this repo at runtime since they execute in different projects
- **All necessary context must be in the skill/command/agent file itself** - or fetched dynamically (e.g., via web search, MCP servers)
- **Test changes by thinking about invocation from another codebase** - will the skill/agent have everything it needs?
- **Reference files in `/skills/<name>/` are embedded at plugin install time** - they're available to skills that reference them relatively

## Repository Purpose

This is a private Claude Code marketplace containing reusable agents, skills, and plugins. There are no build commands, test suites, or dependencies to install - this is a documentation-only repository.

## Directory Structure

```
pm7y-marketplace/
├── .claude-plugin/
│   ├── marketplace.json       # Marketplace catalog (lists plugins)
│   └── plugin.json            # Root plugin manifest
├── .mcp.json                  # MCP server configurations
├── agents/                    # Agent definitions (auto-discovered)
│   └── <agent-name>.md        # Agent file with YAML frontmatter
├── commands/                  # Command definitions (auto-discovered)
│   └── <command-name>.md      # Command file with YAML frontmatter
├── skills/
│   └── <skill-name>/          # Skill folder (auto-discovered)
│       ├── SKILL.md           # Skill definition
│       └── [resources]        # Supporting files
└── scratch/                   # Work in progress
```

## Content Types

### Skills (`/skills/<skill-name>/`)
Production-ready capabilities. Each skill folder must contain `SKILL.md` with YAML frontmatter:
```yaml
---
name: skill-name
description: User-facing description with examples
allowed-tools: Write, Edit, Read
---
```
Skills extend Claude's abilities for specific tasks and may include supporting reference files.

Example: `/skills/mermaid-diagram/`

### Agents (`/agents/`)
Task-focused AI workers. Each agent has YAML frontmatter:
```yaml
---
name: agent-name
description: Description with XML examples showing when to invoke
model: opus
---
```
Supported frontmatter fields: `name`, `description`, `tools`, `disallowedTools`, `model`, `permissionMode`, `skills`, `hooks`.
Agent descriptions should include `<example>` blocks demonstrating invocation patterns.

Example: `/agents/pm7y-dotnet-10-upgrader.md`

### Work in Progress (`/scratch/`)
Items awaiting review and publication before moving to their production location.

## File Format

All skills and agents use YAML frontmatter followed by markdown content. The frontmatter fields are:
- `name` (required): Identifier used for invocation
- `description` (required): When to use this skill/agent, with examples
- `model`: Recommended model (opus, sonnet, haiku)
- `tools`: Comma-separated tool names (agents only)
- `allowed-tools`: Tool restrictions (skills only)

## Conventions

- Keep descriptions actionable and specific, not generic
- Include real examples from actual usage where possible
- Reference supporting files relatively (e.g., `[reference](./reference.md)`)
- Use validation checklists for skills that require careful output verification

## Maintaining Documentation

When making changes to this repository, keep documentation in sync:

1. **Directory structure changes** → Update the "Directory Structure" section in this file
2. **New/removed components** → Update version per VERSIONING.md rules
3. **Schema changes** → Update `.claude/rules/PLUGIN_SCHEMA.md`
4. **Discovered useful links** → Add to the References section below
5. **New conventions or patterns** → Document them in the relevant section

## References

> **Note:** When you discover valuable documentation links during research or troubleshooting, add them to this section to build institutional knowledge.

- [Plugins Reference](https://code.claude.com/docs/en/plugins-reference) - Official plugin.json schema and component documentation
- [Claude Code Marketplace Schema](https://code.claude.com/docs/en/plugin-marketplaces#marketplace-schema)
- [Create Custom Subagents](https://code.claude.com/docs/en/sub-agents) - Agent markdown file format and frontmatter schema
- [Skills vs Agents](https://deepwiki.com/mhattingpete/claude-skills-marketplace/3.1-skills-vs-agents)
- [Plugin Architecture](https://deepwiki.com/mhattingpete/claude-skills-marketplace/3.2-plugin-architecture)

# pm7y Marketplace

A private Claude Code marketplace containing reusable agents, skills, and commands.

## Installation

Add this marketplace to Claude Code:

```bash
/plugin marketplace add pm7y/pm7y-marketplace
```

Then install the plugin:

```bash
/plugin install pm7y-claude-code@pm7y-marketplace
```

### Other Marketplace Commands

```bash
/plugin marketplace update    # Refresh marketplace with latest changes
/plugin marketplace list      # List all added marketplaces
/plugin marketplace remove pm7y-marketplace  # Remove this marketplace
```

## Repository Structure

```
pm7y-marketplace/
├── .claude-plugin/
│   ├── marketplace.json       # Marketplace catalog
│   └── plugin.json            # Plugin manifest (name, version, etc.)
├── .mcp.json                  # MCP server configurations
├── agents/                    # Agent definitions (auto-discovered)
│   └── <agent-name>.md
├── commands/                  # Command definitions (auto-discovered)
│   └── <command-name>.md
├── skills/                    # Skill definitions (auto-discovered)
│   └── <skill-name>/
│       ├── SKILL.md           # Skill definition with YAML frontmatter
│       └── [resources]        # Optional supporting files
├── scratch/                   # Work in progress
├── CLAUDE.md                  # Claude Code instructions
└── README.md                  # This file
```

## Available Skills

| Name | Description |
|------|-------------|
| [pm7y-api-migration-analyzer](skills/pm7y-api-migration-analyzer/) | Analyzes API codebases for Azure subscription migration, producing dependency and risk assessments |
| [pm7y-codebase-review](skills/pm7y-codebase-review/) | Reviews codebase for style consistency, patterns, and KISS/DRY/POLA/YAGNI adherence |
| [pm7y-css-review](skills/pm7y-css-review/) | Reviews CSS/SCSS for over-specificity, missed reuse, and over-engineered abstractions |
| [pm7y-dotnet-upgrade](skills/pm7y-dotnet-upgrade/) | Analyzes .NET 8 codebases for upgrading to .NET 10 |
| [pm7y-mermaid-diagram](skills/pm7y-mermaid-diagram/) | Creates syntactically correct mermaid diagrams with validation and error prevention |
| [pm7y-ralph-loop](skills/pm7y-ralph-loop/) | Sets up cross-platform Ralph Wiggum scripts for autonomous Claude execution |
| [pm7y-youtube-transcript](skills/pm7y-youtube-transcript/) | Downloads YouTube video transcripts and creates structured summaries |

## Available Commands

| Command | Description |
|---------|-------------|
| `/pm7y-api-migration` | Invoke the API migration analyzer skill |
| `/pm7y-css-review` | Review CSS/SCSS changes in the current branch |
| `/pm7y-dotnet-upgrade` | Analyze a .NET 8 codebase for .NET 10 upgrade |
| `/pm7y-mermaid` | Create a mermaid diagram |
| `/pm7y-ralph-loop` | Set up Ralph loop scripts for autonomous execution |
| `/pm7y-ralph-planner` | Create a TASKS.md file for ralph loop execution |
| `/pm7y-random-movie` | Get a random movie suggestion |
| `/pm7y-review-codebase` | Review codebase for patterns and principles |

## Available Agents

| Agent | Description |
|-------|-------------|
| [pm7y-ralph-planner](agents/pm7y-ralph-planner.md) | Interactive planner that creates TASKS.md files for autonomous ralph loop execution |

## Component Reference

### Skills

Skills are specialized capabilities that extend Claude's abilities. Each skill lives in `skills/<name>/SKILL.md` with YAML frontmatter:

```yaml
---
name: pm7y-skill-name
description: |
  Brief description and trigger examples
allowed-tools: Tool1, Tool2
---
```

### Commands

Commands are slash commands that invoke skills or perform actions. Each command lives in `commands/<name>.md` with YAML frontmatter:

```yaml
---
name: pm7y-command-name
description: Brief description
skill: pm7y-skill-name
---
```

### Agents

Agents are task-focused AI workers. Each agent lives in `agents/<name>.md` with YAML frontmatter:

```yaml
---
name: pm7y-agent-name
description: When to use this agent
model: opus
tools: Tool1, Tool2
---
```

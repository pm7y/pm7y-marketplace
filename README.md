# pm7y Marketplace

A private Claude Code marketplace for building and sharing reusable agents and skills.

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

## Overview

This repository serves as a centralized collection of:

- **Skills** - Specialized capabilities that extend Claude's abilities for specific tasks
- **Agents** - Task-focused AI workers for automation and development workflows

## Structure

```
pm7y-marketplace/
├── .claude-plugin/
│   └── marketplace.json       # Marketplace catalog
├── skills/
│   └── <category>/            # Skills grouped by category
│       └── <skill-name>/
├── plugins/
│   └── <plugin-name>/         # Agents grouped by plugin
│       └── agents/
└── scratch/                   # Work in progress
```

## Available Skills

| Name | Path | Description |
|------|------|-------------|
| Mermaid Diagram | `/skills/diagram-skills/mermaid-diagram` | Comprehensive mermaid diagram creation with reference docs for all diagram types, validation checklists, and error prevention patterns |

## Available Agents

| Name | Path | Description |
|------|------|-------------|
| .NET 10 Upgrader | `/plugins/dotnet-agents/agents/dotnet-10-upgrader.md` | Phased .NET 8 to .NET 10 migration handling target frameworks, NuGet packages, breaking changes, and centralized build configuration |

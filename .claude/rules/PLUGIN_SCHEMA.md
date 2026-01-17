# Plugin Schema Specification

This document defines the expected schema for all plugins in the marketplace.

## Directory Structure

Each plugin must follow this structure:

```
plugin-name/
├── .claude-plugin/           # Required: Metadata directory
│   └── plugin.json          # Required: Plugin manifest
├── commands/                 # Optional: Command definitions
│   ├── command1.md
│   └── command2.md
├── agents/                   # Optional: Agent definitions
│   ├── agent1.md
│   └── agent2.md
├── skills/                   # Optional: Agent Skills
│   ├── skill-name/
│   │   └── SKILL.md
│   └── another-skill/
│       ├── SKILL.md
│       └── scripts/
├── hooks/                    # Optional: Hook configurations
│   ├── hooks.json           # Main hook config
│   └── additional-hooks.json
├── .mcp.json                # Optional: MCP server definitions
├── scripts/                 # Optional: Hook and utility scripts
│   ├── script1.sh
│   └── script2.py
├── LICENSE                  # Optional: License file
├── CHANGELOG.md             # Optional: Version history
└── README.md                # Optional: Documentation
```

## Required Files

### `.claude-plugin/plugin.json`

The plugin manifest file is **required** for all plugins. It must contain valid JSON with the following structure:

#### Required Fields

- **`name`** (string): Plugin identifier (kebab-case recommended)
- **`version`** (string): Semantic version (e.g., "1.2.0")
- **`description`** (string): Brief description of the plugin

#### Optional Fields

**Metadata:**
- **`author`** (object): Author information
  - `name` (string, required): Author's name
  - `email` (string, optional): Author's email
  - `url` (string, optional): Author's website or GitHub profile
- **`homepage`** (string): Plugin documentation URL
- **`repository`** (string): Git repository URL
- **`license`** (string): License identifier (e.g., "MIT", "Apache-2.0")
- **`keywords`** (array of strings): Searchable keywords

**Component Paths:**
- **`commands`** (string | array): Command files or directories. Example: `"./commands/"` or `["./cmd1.md", "./cmd2.md"]`
- **`agents`** (string | array): Agent files or directories. Example: `"./agents/"` or `["./agents/reviewer.md"]`
- **`skills`** (string | array): Skill directories. Example: `"./skills/"` or `["./skills/my-skill/"]`
- **`hooks`** (string | object): Hook config path or inline configuration
- **`mcpServers`** (string | object): MCP server definitions path or inline configuration
- **`outputStyles`** (string | array): Output style files or directories
- **`lspServers`** (string | object): Language Server Protocol configuration

> **Note:** Default directories (`commands/`, `agents/`, `skills/`) are auto-discovered at the plugin root. Explicit paths in plugin.json **supplement** (not replace) these defaults. Only specify paths if using non-default locations.

#### Example `plugin.json`

**Minimal:**

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "A simple plugin for Claude Code"
}
```

**Complete (with explicit paths):**

```json
{
  "name": "enterprise-plugin",
  "version": "1.2.0",
  "description": "Enterprise-grade development plugin",
  "author": {
    "name": "Jane Developer",
    "email": "jane@example.com",
    "url": "https://github.com/janedev"
  },
  "homepage": "https://docs.example.com/plugin",
  "repository": "https://github.com/janedev/enterprise-plugin",
  "license": "MIT",
  "keywords": ["enterprise", "security", "compliance"],
  "commands": ["./custom/commands/special.md", "./custom/commands/another.md"],
  "agents": ["./custom/agents/reviewer.md", "./custom/agents/tester.md"],
  "skills": "./custom/skills/",
  "hooks": "./config/hooks.json",
  "mcpServers": "./.mcp.json"
}
```

**Minimal (using auto-discovery):**

If your plugin uses default directory locations (`agents/`, `skills/`, `commands/`), you don't need to specify them:

```json
{
  "name": "simple-plugin",
  "version": "1.0.0",
  "description": "Plugin using default directory structure"
}
```

The plugin loader will automatically discover:
- `agents/*.md` - Agent definitions
- `skills/*/SKILL.md` - Skill definitions
- `commands/*.md` - Command definitions

## Optional Directories

### `commands/`

Contains command definition files (`.md` format). Each file defines a custom slash command.

### `agents/`

Contains agent definition files (`.md` format). Each file defines a specialized AI agent.

### `skills/`

Contains skill directories. Each skill directory should have:

- `SKILL.md` - Skill definition
- Optional subdirectories for scripts and resources

### `hooks/`

Contains hook configuration files (`.json` format). Hooks define event-driven automation.

### `scripts/`

Contains scripts used by hooks or for plugin utilities. Can be any executable format (.sh, .py, .js, etc.).

## Optional Files

### `.mcp.json`

Defines Model Context Protocol server configurations. Must be valid JSON.

### `README.md`

Plugin documentation and usage instructions.

## Validation

### Common Validation Errors

1. **Missing `plugin.json`**

   ```
   Error: Missing required file: .claude-plugin/plugin.json
   ```

   Solution: Create the `.claude-plugin/` directory and add a `plugin.json` file.

2. **Invalid `plugin.json` structure**

   ```
   Error: plugin.json missing required fields: name, version
   ```

   Solution: Ensure all required fields are present.

3. **Invalid author field**

   ```
   Error: plugin.json 'author' must be an object
   ```

   Solution: Use the proper author object format with at least a `name` field.

4. **Invalid JSON syntax**

   ```
   Error: Invalid JSON in plugin.json: Expecting ',' delimiter
   ```

   Solution: Fix JSON syntax errors (trailing commas, missing quotes, etc.).

5. **Invalid agents/skills field**

   ```
   Error: Validation errors: agents: Invalid input
   ```

   Solution: The `agents` and `skills` fields accept either a string path or an array of paths. If using default directories (`agents/`, `skills/`), omit these fields entirely and let auto-discovery handle them. Complex YAML frontmatter in agent files (multiline descriptions with special characters) can also cause validation failures.

6. **Agent file validation failure**

   ```
   Error: Failed to parse agent file
   ```

   Solution: Keep agent YAML frontmatter simple. Use single-line descriptions. Valid frontmatter fields are: `name`, `description`, `tools`, `disallowedTools`, `model` (sonnet|opus|haiku|inherit), `permissionMode`, `skills`, `hooks`.

## Best Practices

1. **Use semantic versioning** for the `version` field
2. **Include descriptive keywords** for better discoverability
3. **Provide author information** for attribution and support
4. **Link to repository** for open-source contributions
5. **Document your plugin** with a README.md
6. **Track changes** with CHANGELOG.md
7. **Use clear names** that describe the plugin's purpose
8. **Keep descriptions concise** but informative

## Migration Guide

If you have an existing plugin without `plugin.json`, create it:

```bash
cd plugins/your-plugin
mkdir -p .claude-plugin
cat > .claude-plugin/plugin.json << 'EOF'
{
  "name": "your-plugin",
  "version": "1.0.0",
  "description": "Your plugin description",
  "author": {
    "name": "Your Name"
  }
}
EOF
```

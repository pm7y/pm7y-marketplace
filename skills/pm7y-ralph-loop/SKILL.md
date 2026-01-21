---
name: pm7y-ralph-loop
description: Set up a cross-platform Ralph Wiggum loop for continuous autonomous Claude execution. Use when the user wants to run Claude in a loop, implement the Ralph technique, or needs continuous autonomous operation. This skill only sets up the PS1 scripts - use pm7y-ralph-planner to create TASKS.md files.
allowed-tools: Write, Read, Bash
---

# Ralph Wiggum Loop Setup

The Ralph Wiggum technique involves running Claude in a continuous loop, spawning fresh sessions to maintain context quality over long-running autonomous operations. This skill sets up cross-platform PowerShell Core scripts for this purpose.

**Note:** This skill only creates the execution scripts. Use the `pm7y-ralph-planner` agent to create or update TASKS.md files with validation requirements and learnings tracking.

## Script Locations

The PowerShell scripts are located at:

```
${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-loop.ps1  # Continuous loop
${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-once.ps1  # Single execution
```

## What You Do

When this skill is invoked:

1. **Check for PowerShell Core** - Verify `pwsh` is available
2. **Copy the scripts** - Copy `ralph-loop.ps1` and `ralph-once.ps1` from the plugin to the current directory
3. **Explain usage** - Show the user how to run and customize
4. **Suggest task planning** - Recommend using `pm7y-ralph-planner` if no TASKS.md exists

## Setup Steps

### Step 1: Check Prerequisites

```bash
# Check if pwsh is available
pwsh --version
```

If not installed, advise:
- **macOS:** `brew install powershell`
- **Windows:** Already included or install from Microsoft Store
- **Linux:** `sudo snap install powershell --classic`

### Step 2: Copy the Scripts

Copy both scripts from the plugin to the user's current directory:

- `${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-loop.ps1` → `./ralph-loop.ps1`
- `${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-once.ps1` → `./ralph-once.ps1`

### Step 3: Check for TASKS.md

Check if TASKS.md exists in the current directory:

- **If exists:** Inform the user they're ready to run the loop
- **If missing:** Suggest running `/pm7y-ralph-planner` to create a properly structured TASKS.md with validation requirements and learnings tracking

## Usage Instructions

After setup, tell the user:

### ralph-once.ps1 (Single Execution)

Run Claude once - useful for testing prompts or one-off tasks:

```bash
# Default (uses TASKS.md, opus model)
pwsh ./ralph-once.ps1

# Custom configuration
pwsh ./ralph-once.ps1 -PromptFile "task.md" -Model "sonnet"

# Quiet mode (only show Claude output)
pwsh ./ralph-once.ps1 -Quiet

# Without visualization
pwsh ./ralph-once.ps1 -NoVisualize
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-NoVisualize` | false | Skip repomirror visualization |
| `-Quiet` | false | Suppress status messages |

### ralph-loop.ps1 (Continuous Loop)

Run Claude in a continuous loop:

```bash
# Default (uses TASKS.md, opus model, 25 iterations max, 10s sleep)
pwsh ./ralph-loop.ps1

# Custom configuration
pwsh ./ralph-loop.ps1 -PromptFile "tasks.md" -Model "sonnet" -SleepSeconds 30

# Limited iterations (for testing)
pwsh ./ralph-loop.ps1 -MaxIterations 5

# Without repomirror visualization
pwsh ./ralph-loop.ps1 -NoVisualize
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-SleepSeconds` | 10 | Seconds between iterations |
| `-MaxIterations` | 25 | Max iterations (0 = unlimited) |
| `-NoVisualize` | false | Skip repomirror visualization |

**Prerequisites:**

- PowerShell Core (`pwsh`) - Install via `brew install powershell` (macOS) or from Microsoft
- Claude CLI (`claude`) - Install from <https://claude.ai/code>
- Node.js/npm (for repomirror visualization, optional)

**Stop the loop:** Press `Ctrl+C`

## Checklist

- [ ] Check if `pwsh` is available, suggest installation if not
- [ ] Copy `ralph-loop.ps1` from plugin to current directory
- [ ] Copy `ralph-once.ps1` from plugin to current directory
- [ ] Check if `TASKS.md` exists
- [ ] If no TASKS.md, suggest using `pm7y-ralph-planner` to create one
- [ ] Explain usage to user

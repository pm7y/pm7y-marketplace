---
description: Set up cross-platform Ralph Wiggum scripts for autonomous Claude execution (loop and single-run)
allowed-tools: Write, Read, Bash
---

Set up Ralph Wiggum scripts for autonomous Claude execution in this project.

## What to do

1. **Check prerequisites:**
   - Verify `pwsh` (PowerShell Core) is available
   - If not, explain how to install it:
     - macOS: `brew install powershell`
     - Windows: Already included or install from Microsoft Store
     - Linux: `sudo snap install powershell --classic`

2. **Copy scripts** from the plugin to the current directory:
   - `${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-loop.ps1` → `./ralph-loop.ps1`
   - `${CLAUDE_PLUGIN_ROOT}/skills/pm7y-ralph-loop/ralph-once.ps1` → `./ralph-once.ps1`

3. **Check for TASKS.md** - If it doesn't exist, ask the user if they want a template created

4. **Show usage:**

### ralph-once.ps1 (Single Execution)

```bash
pwsh ./ralph-once.ps1                              # Run once with defaults
pwsh ./ralph-once.ps1 -Model sonnet                # Use sonnet model
pwsh ./ralph-once.ps1 -Quiet                       # Only show Claude output
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-NoVisualize` | false | Skip repomirror visualization |
| `-Quiet` | false | Suppress status messages |

### ralph-loop.ps1 (Continuous Loop)

```bash
pwsh ./ralph-loop.ps1                              # Run with defaults
pwsh ./ralph-loop.ps1 -Model sonnet -SleepSeconds 30
pwsh ./ralph-loop.ps1 -MaxIterations 5             # Test with limited runs
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-SleepSeconds` | 10 | Seconds between iterations |
| `-MaxIterations` | 25 | Max iterations (0 = unlimited) |
| `-NoVisualize` | false | Skip repomirror visualization |

# Ralph Wiggum Loop

A cross-platform PowerShell Core solution for running Claude Code in continuous autonomous mode.

## What is the Ralph Wiggum Technique?

The "Ralph Wiggum technique" involves running Claude in a continuous loop, spawning fresh sessions for each iteration. This maintains context quality over long-running autonomous operations by avoiding context degradation that occurs in very long conversations.

Each iteration:
1. Reads instructions from a task file (e.g., `TASKS.md`)
2. Spawns a fresh Claude session
3. Claude executes autonomously with full tool access
4. Session ends, changes are optionally committed
5. Loop sleeps briefly, then repeats

## Control Flow

### Single Execution (`ralph-once.ps1`)

```
┌─────────────────────────────────────────────────────────────┐
│                      ralph-once.ps1                         │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │  Check Prerequisites  │
                │  - Prompt file exists │
                │  - claude CLI exists  │
                │  - npx exists (opt)   │
                └───────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │  Read Prompt File     │
                │  (default: TASKS.md)  │
                └───────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │  Execute Claude       │
                │  --dangerously-skip-  │
                │    permissions        │
                │  --output-format=     │
                │    stream-json        │
                └───────────────────────┘
                            │
                            ▼
              ┌─────────────┴─────────────┐
              │     Visualization?        │
              └─────────────┬─────────────┘
                   yes │          │ no
                       ▼          ▼
         ┌─────────────────┐  ┌─────────────────┐
         │ Pipe to         │  │ Direct output   │
         │ repomirror      │  │                 │
         │ visualize       │  │                 │
         └─────────────────┘  └─────────────────┘
                       │          │
                       └────┬─────┘
                            ▼
              ┌─────────────────────────┐
              │    AutoCommit?          │
              └─────────────────────────┘
                   yes │          │ no
                       ▼          ▼
         ┌─────────────────┐      │
         │ Check for       │      │
         │ git changes     │      │
         │ Generate commit │      │
         │ message (haiku) │      │
         │ git add -A      │      │
         │ git commit      │      │
         └─────────────────┘      │
                       │          │
                       └────┬─────┘
                            ▼
                ┌───────────────────────┐
                │       Done            │
                └───────────────────────┘
```

### Continuous Loop (`ralph-loop.ps1`)

```
┌─────────────────────────────────────────────────────────────┐
│                      ralph-loop.ps1                         │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │  Check Prerequisites  │
                │  Initialize counter   │
                └───────────────────────┘
                            │
                            ▼
            ┌───────────────────────────────┐
            │     iteration++ < max?        │◄──────────────┐
            └───────────────────────────────┘               │
                   yes │          │ no                      │
                       ▼          ▼                         │
         ┌─────────────────┐  ┌─────────────────┐          │
         │ Log timestamp   │  │ Exit loop       │          │
         │ & iteration #   │  │ "Reached max"   │          │
         └─────────────────┘  └─────────────────┘          │
                   │                                        │
                   ▼                                        │
         ┌─────────────────┐                               │
         │ Read prompt     │                               │
         │ Execute Claude  │                               │
         │ (same as once)  │                               │
         └─────────────────┘                               │
                   │                                        │
                   ▼                                        │
         ┌─────────────────┐                               │
         │ AutoCommit?     │                               │
         │ (if enabled)    │                               │
         └─────────────────┘                               │
                   │                                        │
                   ▼                                        │
         ┌─────────────────┐                               │
         │ Sleep for       │                               │
         │ SleepSeconds    │───────────────────────────────┘
         └─────────────────┘

         Press Ctrl+C to interrupt at any time
```

## Scripts

### `ralph-once.ps1` - Single Execution

Run Claude once against a task file. Useful for testing prompts or one-off tasks.

```powershell
# Default (uses TASKS.md, opus model)
pwsh ./ralph-once.ps1

# Custom configuration
pwsh ./ralph-once.ps1 -PromptFile "task.md" -Model "sonnet"

# Quiet mode (only show Claude output)
pwsh ./ralph-once.ps1 -Quiet

# Without visualization
pwsh ./ralph-once.ps1 -NoVisualize

# Auto-commit changes after execution
pwsh ./ralph-once.ps1 -AutoCommit
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-NoVisualize` | false | Skip repomirror visualization |
| `-Quiet` | false | Suppress status messages |
| `-AutoCommit` | false | Commit changes with AI-generated message |

### `ralph-loop.ps1` - Continuous Loop

Run Claude in a continuous loop with configurable iteration limits.

```powershell
# Default (TASKS.md, opus, 25 iterations max, 10s sleep)
pwsh ./ralph-loop.ps1

# Custom configuration
pwsh ./ralph-loop.ps1 -PromptFile "tasks.md" -Model "sonnet" -SleepSeconds 30

# Limited iterations (for testing)
pwsh ./ralph-loop.ps1 -MaxIterations 5

# Unlimited iterations (run until Ctrl+C)
pwsh ./ralph-loop.ps1 -MaxIterations 0

# Auto-commit after each iteration
pwsh ./ralph-loop.ps1 -AutoCommit
```

| Parameter | Default | Description |
|-----------|---------|-------------|
| `-PromptFile` | TASKS.md | Path to the prompt/task file |
| `-Model` | opus | Claude model (sonnet, opus, haiku) |
| `-SleepSeconds` | 10 | Seconds between iterations |
| `-MaxIterations` | 25 | Max iterations (0 = unlimited) |
| `-NoVisualize` | false | Skip repomirror visualization |
| `-AutoCommit` | false | Commit changes after each iteration |

## Prerequisites

- **PowerShell Core** (`pwsh`)
  - macOS: `brew install powershell`
  - Windows: Included or install from Microsoft Store
  - Linux: `sudo snap install powershell --classic`

- **Claude CLI** (`claude`)
  - Install from https://claude.ai/code

- **Node.js/npm** (optional, for visualization)
  - Required for `repomirror visualize` output formatting

## The TASKS.md File

The task file tells Claude what to do in each iteration. A well-structured TASKS.md includes:

```markdown
# Task Instructions

You are running in autonomous loop mode.

## Current Focus
- [ ] Task 1
- [ ] Task 2

## Validation Requirements (MANDATORY)

### Build Validation
```bash
dotnet build  # or npm run build, cargo build, etc.
```

### Test Validation
```bash
dotnet test   # or npm test, pytest, etc.
```

## Validation Rules

1. NEVER skip validation
2. Build must pass before proceeding
3. Tests must pass
4. Roll back on persistent failure
5. Document learnings before ending failed iterations

## Learnings Log

Document insights from failed attempts so the next iteration benefits.

### [DATE] - [Brief description]
- **Error:** [Exact error message]
- **Tried:** [Approaches attempted]
- **Found:** [What was discovered]
- **Next step:** [Recommended action]
```

## Back Pressure: Validation-Driven Iterations

Each iteration must validate its changes before completing. This prevents:
- Accumulating broken code across iterations
- Introducing regressions that compound
- Drifting from goals without feedback

### Validation Flow

```
┌─────────────────────────────────────────────────────────────┐
│                   Each Iteration                            │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │ 1. Read Learnings Log │
                │    (learn from past)  │
                └───────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │ 2. Pick ONE task      │
                └───────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │ 3. Make changes       │
                └───────────────────────┘
                            │
                            ▼
                ┌───────────────────────┐
                │ 4. Run build          │
                └───────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │       Build passes?       │
              └─────────────┬─────────────┘
                  yes │          │ no
                      │          ▼
                      │    ┌─────────────────┐
                      │    │ Fix and retry   │
                      │    │ (2-3 attempts)  │
                      │    └─────────────────┘
                      │          │
                      │          ▼ still failing
                      │    ┌─────────────────┐
                      │    │ Rollback:       │
                      │    │ git checkout .  │
                      │    │ Document in     │
                      │    │ Learnings Log   │
                      │    └─────────────────┘
                      ▼
                ┌───────────────────────┐
                │ 5. Run tests          │
                └───────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │       Tests pass?         │
              └─────────────┬─────────────┘
                  yes │          │ no
                      │          ▼
                      │    (same fix/rollback flow)
                      ▼
                ┌───────────────────────┐
                │ 6. Commit changes     │
                │    Mark task complete │
                └───────────────────────┘
```

### The Learnings Log

Since each iteration is a fresh Claude session with no memory, the Learnings Log preserves knowledge across iterations:

**What to document:**
- Exact error messages encountered
- Approaches tried and why they failed
- Hypotheses about root cause
- Files/areas investigated
- Partial solutions that showed promise
- Dead ends to avoid

**Example:**
```markdown
### 2024-01-15 - Auth token validation failing
- **Error:** `JWT signature verification failed` in AuthMiddleware.cs:47
- **Tried:** Regenerating keys, checking clock skew, verifying issuer
- **Found:** Token is being double-encoded somewhere in the pipeline
- **Suspect:** Look at `TokenService.CreateToken()` - may be base64 encoding twice
- **Next step:** Add logging before/after each encoding step
```

## Stopping the Loop

Press `Ctrl+C` to gracefully stop the loop. The current iteration will complete and the script will exit with a summary.

## Example Session

```
$ pwsh ./ralph-loop.ps1 -MaxIterations 3 -SleepSeconds 5

Starting Ralph Loop
  Prompt file: TASKS.md
  Model: opus
  Sleep: 5s between iterations
  Max iterations: 3
  Visualization: True
  Auto-commit: False

Press Ctrl+C to stop

========================LOOP=========================

[14:32:05] Iteration 1
... Claude output ...

========================LOOP=========================

Sleeping for 5 seconds...
[14:33:12] Iteration 2
... Claude output ...

========================LOOP=========================

Sleeping for 5 seconds...
[14:34:20] Iteration 3
... Claude output ...

========================LOOP=========================

Reached maximum iterations (3). Stopping.

Ralph Loop completed after 3 iteration(s)
```

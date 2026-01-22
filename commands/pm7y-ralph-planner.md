---
description: Create a TASKS.md file for autonomous ralph loop execution through interactive planning
allowed-tools: Task
---

Create a detailed TASKS.md file suitable for autonomous execution via the ralph loop technique.

## What to do

Invoke the `pm7y-ralph-planner` agent to run the interactive planning process.

**If the user provided an initial prompt/description**, pass it to the agent so it can use that context to streamline the questioning process.

```
Task tool:
  subagent_type: pm7y-claude-code:pm7y-ralph-planner
  description: "Plan tasks for ralph loop"
  prompt: |
    [Include any initial prompt/description the user provided]
    [If no initial prompt, just say "The user wants to create a TASKS.md file."]
```

The agent will:
1. Explore the codebase to understand the project
2. Ask clarifying questions (adaptive based on complexity)
3. Generate a TASKS.md draft
4. Present for review and iterate until approved
5. Write the final TASKS.md file

## Examples

```bash
# No initial prompt - agent will ask what you want to build
/pm7y-ralph-planner

# With initial prompt - seeds the planning process
/pm7y-ralph-planner Add user authentication with JWT tokens

# With detailed prompt - may skip some questions
/pm7y-ralph-planner Refactor the API layer to use repository pattern, need to update UserService, OrderService, and ProductService
```

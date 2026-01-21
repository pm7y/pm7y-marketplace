---
description: Analyze an API codebase for Azure subscription migration. Produces API_Summary.md with dependencies, endpoints, messaging, and risks assessment.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task
skill: pm7y-api-migration-analyzer
---

**Prerequisites:**
- Working directory must be the API repository root
- Must be a .NET codebase (contains .csproj/.sln files)

**Execution:**
1. Discovery phase identifies codebase structure
2. Parallel agents analyze: Core Identity, Integration Surface, API Contract, Messaging & Jobs, Infrastructure & Security
3. Consolidation validates findings and generates API_Summary.md

**Output:** `API_Summary.md` with migration blockers, dependencies, risks, and C4 context diagram

Analyze this API for Azure subscription migration: $ARGUMENTS

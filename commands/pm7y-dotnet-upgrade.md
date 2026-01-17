---
description: Upgrade a .NET project from .NET 8 to .NET 10 using a phased approach
---

# .NET 10 Upgrade Command

Use the **dotnet-10-upgrader** agent to upgrade this codebase from .NET 8 to .NET 10.

The agent follows a phased approach:
- **Phase 1**: Minimal changes to get code compiling on .NET 10
- **Phase 2**: NuGet package updates and code modernization
- **Phase 3**: Centralized build configuration setup

$ARGUMENTS

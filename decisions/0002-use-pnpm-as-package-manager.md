# 2. Use pnpm as package manager

Date: 2026-11-06

## Context

From the inception of this project, we required a package management system that guarantees efficiency, predictability, and long-term maintainability. Traditional package managers like `npm` use a flattened `node_modules` directory layout. While this was designed to avoid deep nesting, it introduces critical systemic risks and performance bottlenecks that we wanted to bypass from day one:

- **Phantom Dependencies:** `npm` hoists sub-dependencies to the root level. This layout allows application code to implicitly import packages that are not explicitly declared in `package.json`. If an upstream dependency removes or updates its internal tree, the application breaks silently in CI or production.
- **Disk Space Degradation:** `npm` downloads and writes physical duplicates of identical packages for every project initialized on a machine. This causes significant storage bloat across developer workstations.
- **CI/CD Bottlenecks:** Re-downloading and unpacking large dependency trees during container builds extends deployment cycles and slows down development feedback loops.

To prevent these technical debts from embedding themselves in our codebase, we evaluated alternative package managers before writing our first line of code.

## Decision

We chose **`pnpm`** as the exclusive package manager from the start of this repository.

- `pnpm` constructs a nested, non-flat `node_modules` structure using symlinks to ensure that application code can only access dependencies explicitly declared in `package.json`.
- We leverage `pnpm-workspace.yaml` and the `workspace:` protocol to manage local inter-package links natively.

## Consequences

- (+) Build steps, lockfile resolutions, and local setups have been optimized from day one because `pnpm` avoids copying files and parallelizes asset fetching.
- (+) All external files are saved exactly once on the developer's disk, keeping our local environments clean.
- (+) The `workspace:*` dependency declaration ensures local links are managed cleanly without polluting external registries.
- (-) `npm` is the default package manager. `pnpm`, although popular, isn't expected to be available. Developers will need to install `pnpm`.

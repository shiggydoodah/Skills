---
name: create-context
description: Creates a compressed CONTEXT.md for one target directory and upserts that directory into the root CONTEXT-MAP.md. Use when the user wants targeted codebase context for an app, package, subsystem, or directory, mentions "create-context", asks to refresh a single CONTEXT.md, or wants agents to navigate a large monorepo without reading unnecessary files.
---

# create-context

Creates one agent-only `CONTEXT.md` for a specific directory, then updates the
root `CONTEXT-MAP.md` so future agents can find it quickly.

Use `map-context` for a repo-wide context map. Use `create-context` when the
user points at one app, package, subsystem, or directory and wants a compressed
brief for that area.

## Invocation

```text
/create-context              # create context for the current directory
/create-context apps/web     # create context for one target directory
```

## Process

1. **Resolve the target** - use the provided path, or the current directory if
   none is provided. Resolve the repo root with `git rev-parse --show-toplevel`;
   if that fails, use the current working directory.
2. **Inspect deeply** - read enough source, tests, manifests, docs, and local
   configuration to understand the target's purpose, architecture, flows,
   integration points, and operational gotchas.
3. **Write one brief** - create or replace `CONTEXT.md` at the target root.
   Do not create child contexts unless the user explicitly asks for them.
4. **Upsert the map** - create or update `CONTEXT-MAP.md` at the repo root,
   preserving unrelated entries and replacing only the target entry.
5. **Report the result** - summarize the target path, context file, map update,
   and any important limitations in what was inspected.

See [REFERENCE.md](REFERENCE.md) for the required `CONTEXT.md` format, map
entry rules, and compression guidelines.

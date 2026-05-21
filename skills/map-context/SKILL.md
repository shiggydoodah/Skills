---
name: map-context
description: Generates CONTEXT.md files for each meaningful directory in a codebase, plus a CONTEXT-MAP.md index at the repo root. Updates CLAUDE.md (and AGENTS.md if present) so agents consult the map before exploring code. Use when the user wants to document a codebase for agents, mentions "context map" or "map-context", wants to reduce token burn from codebase exploration, or wants agents to navigate large repos efficiently.
---

# map-context

Traverses a codebase, writes a CONTEXT.md for each meaningful directory, builds a CONTEXT-MAP.md index at the repo root, and registers it in CLAUDE.md so agents use it automatically.

For a targeted refresh of one app, package, subsystem, or directory, use
`create-context` instead.

## Invocation

```
/map-context           # map from current directory
/map-context src/      # map a specific subtree
```

## Process

1. **Traverse** the target directory recursively, collecting candidate directories
2. **Qualify** each directory — see [REFERENCE.md](REFERENCE.md) for skip heuristics
3. **Read** the code in each qualifying directory (source files, not configs/assets)
4. **Write** CONTEXT.md for each qualifying directory (always overwrite existing)
5. **Write** CONTEXT-MAP.md at the repo root
6. **Update** CLAUDE.md — create if missing; add or replace the Codebase Context block
7. **Update** AGENTS.md — only if it already exists, never create it
8. **Print** a summary: files written, directories skipped with reasons, confirmations

See [REFERENCE.md](REFERENCE.md) for content format, skip heuristics, depth rules, and file templates.

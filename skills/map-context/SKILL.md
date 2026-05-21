---
name: map-context
description: Generates a super-compressed CONTEXT-MAP.md birds-eye index of a codebase at the repo root. Use when the user wants a high-level context map, mentions "context map" or "map-context", wants agents to orient in a large repo without burning context, or needs a routing table to existing or future CONTEXT.md files.
---

# map-context

Builds a tiny `CONTEXT-MAP.md` at the repo root: a birds-eye routing table of
the codebase, not detailed documentation.

Do not write nested `CONTEXT.md` files from this skill. For a targeted brief of
one app, package, subsystem, or directory, use `create-context`.

## Invocation

```text
/map-context           # map the current repo
/map-context apps/     # map a specific subtree into the root map
```

## Process

1. **Find the root** - use `git rev-parse --show-toplevel`; if it fails, use
   the current working directory.
2. **Scan cheaply** - inspect directory names, manifests, existing docs, and
   obvious entrypoints. Read source only when needed to identify what an area is.
3. **Group areas** - list meaningful apps, packages, services, libraries, and
   major subsystems. Skip generated, vendored, cache, build, fixture, and asset
   directories.
4. **Write the map** - create or replace `CONTEXT-MAP.md` at the repo root with
   one compressed entry per meaningful area.
5. **Register the map** - update `CLAUDE.md`, creating it if missing. Update
   `AGENTS.md` only if it already exists.
6. **Report briefly** - summarize the map path, number of areas listed, and
   any major skipped areas.

See [REFERENCE.md](REFERENCE.md) for entry format, compression rules, skip
heuristics, and agent-instruction blocks.

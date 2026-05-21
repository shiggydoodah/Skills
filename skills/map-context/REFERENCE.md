# map-context Reference

## Goal

`CONTEXT-MAP.md` is a repo-level routing table. It should give an agent a fast
birds-eye view of the codebase so the agent can choose where to look next
without reading source files or detailed docs first.

It is not onboarding documentation, a directory tree, or a replacement for
targeted `CONTEXT.md` files.

## Scan Rules

- Prefer cheap signals first: directory names, package manifests, workspace
  config, README headings, existing `CONTEXT.md` files, and obvious entrypoints.
- Read source only when cheap signals do not reveal what an area owns.
- Group noisy leaf directories into their owning app, package, service, or
  subsystem.
- Do not generate or overwrite nested `CONTEXT.md` files.

## Include / Skip

Include meaningful areas such as:

- Apps, services, packages, libraries, CLIs, workers, or major product modules
- Shared infrastructure or tooling directories with real ownership
- Existing `CONTEXT.md` locations, even if they are nested

Skip or collapse:

- Generated, vendored, cache, dependency, and build output directories
- Pure assets, fixtures, snapshots, lockfiles, and static public files
- Tiny implementation leaves that only make sense inside a parent area
- Directories whose names are common noise: `.git/`, `node_modules/`, `dist/`,
  `build/`, `coverage/`, `.next/`, `.turbo/`, `target/`, `vendor/`

## CONTEXT-MAP.md Format

Keep the whole file short. Prefer one line per area. Mention contents only as a
compressed shape, not a file list.

Use this format:

```markdown
# Context Map

> Read this first. It is a compressed routing map, not documentation.

## Areas

- `apps/web` - customer web app; routes, UI, server actions. Context: `apps/web/CONTEXT.md`
- `packages/auth` - shared auth library used by apps and APIs. Context: missing; run `/create-context packages/auth`.
- `infra` - deployment and environment config; Terraform, scripts, CI glue.
```

Entry rules:

- Start with the path in backticks.
- Use one short sentence or sentence fragment.
- Include only stable, useful contents categories such as routes, UI, jobs,
  schemas, adapters, migrations, tests, or deployment config.
- If a `CONTEXT.md` exists for the area, link to it.
- If an area clearly needs a brief but has no `CONTEXT.md`, say:
  `Context: missing; run /create-context path`.
- Do not include file-by-file inventories, implementation detail, or long
  nested bullets.

## CLAUDE.md / AGENTS.md Block

Add or replace the `## Codebase Context` section with:

```markdown
## Codebase Context

Before exploring the codebase, read `CONTEXT-MAP.md` at the repo root.
It is a compressed routing map of the major apps, packages, services, and
subsystems. Use it to choose the smallest relevant area before reading source.
When an area has a linked `CONTEXT.md`, read that next.
```

**CLAUDE.md**: Create if missing. If a `## Codebase Context` section already exists, replace it. Otherwise append.

**AGENTS.md**: Same block, same logic. Never create — only update if the file already exists.

# create-context Reference

## Goal

`CONTEXT.md` is an agent-only operational brief. It should let a future agent
decide whether this directory matters, understand how it works, and start in
the right place without first reading a directory tree or broad source files.

Optimize for context-window savings over exhaustive documentation.

## Target Rules

- Generate exactly one `CONTEXT.md` at the requested target directory by
  default.
- Inspect nested files and subdirectories as needed, but summarize them into
  the target brief.
- Create child `CONTEXT.md` files only when the user explicitly requests child
  contexts.
- Overwrite the target `CONTEXT.md` if it already exists.
- Do not treat generated output, vendored code, build artifacts, assets, lock
  files, or test fixtures as primary sources unless they explain runtime
  behavior.

## CONTEXT.md Format

Use tight Markdown. Prefer dense bullets over explanatory prose. Keep the brief
short enough that an agent can read it by default.

```markdown
# Context: path/to/target

## Purpose
- What this directory owns in the product/system.
- When an agent should read or edit this area.

## Architecture
- Main runtime pieces and how they fit together.
- Important entrypoints, naming only stable files or symbols an agent needs.

## Key Flows
- Inputs, outputs, state changes, side effects, and user/system paths.

## Integrations
- Upstream callers, downstream dependencies, APIs, storage, queues, services,
  environment variables, or generated contracts.

## Gotchas
- Non-obvious constraints, invariants, failure modes, coupling, or test setup.

## Agent Notes
- Where to start for common edits.
- What to avoid reading or changing unless the task specifically requires it.
```

## Compression Rules

- No full file listings. Mention files only when they are durable entrypoints,
  ownership boundaries, or common edit locations.
- No onboarding prose, tutorials, or marketing explanations.
- Prefer "what matters for changing this safely" over historical background.
- Capture cross-directory relationships when they prevent unnecessary source
  exploration.
- If something is uncertain, say so briefly instead of inventing certainty.

## CONTEXT-MAP.md Upsert

`CONTEXT-MAP.md` lives at the repo root. Preserve existing entries and replace
only the entry for the normalized target path. If the file does not exist,
create this minimal form:

```markdown
# Context Map

> Read this first. It is a compressed routing map, not documentation.

## Areas

- `path/to/target` - one sentence description of what this area owns. Context:
  `path/to/target/CONTEXT.md`
```

Keep the map as a routing table, not documentation. Each entry should include:

- Path in backticks, normalized from the repo root
- One short sentence or sentence fragment describing what the area owns
- `Context: path/to/target/CONTEXT.md`

If the target is the repo root, use `.` as the path and `CONTEXT.md` as the
context link.

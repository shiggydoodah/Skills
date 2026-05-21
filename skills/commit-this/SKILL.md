---
name: commit-this
description: Create a git commit for the current work by default, or write/review a commit message when requested with --print. Supports short and detailed modes. Use when the user invokes /commit-this, asks to commit current changes, asks for a commit message, commit title, commit body, conventional commit, or help splitting broad changes into reviewable commits.
---

# Commit This

## Goal

Create commits that make history useful: clear scope, specific intent,
reviewable changes, and truthful verification or risk notes when relevant.

By default, this skill stages the relevant work and creates a git commit. It
only prints the commit message without committing when the user passes
`--print`.

## Workflow

1. Inspect the repository state with `git status --short`, then inspect the
   relevant staged and unstaged diffs.
2. If there is no staged or unstaged git diff, exit immediately with a short
   silly error message. Do not write a commit message and do not run
   `git commit`.
3. Decide whether the change is one logical commit.
4. If unrelated work is mixed together, recommend splitting it into separate
   commits instead of committing.
5. Choose the requested mode. If the user passes `--short`, use short mode. If
   no mode is requested, use detailed mode.
6. Resolve staging:
   - If there are both staged and unstaged changes, ask the user whether to
     include the unstaged changes or commit only the staged changes.
   - If the user chooses to include unstaged changes, run `git add .`.
   - If the user chooses staged changes only, do not stage anything else.
   - If the work is all staged or all unstaged, run `git add .` before
     committing.
7. Create the commit with `git commit` using the generated message, unless the
   user passed `--print`.
8. If the user passed `--print`, print the commit message to the current window
   instead of staging or committing, then ask: "Add this commit to the git tree?
   Yes or No". If the user says yes, follow the normal staging and commit
   workflow. If the user says no, stop.

## Invocation

```text
/commit-this
```

Create a detailed commit for the current diff.

```text
/commit-this --short
```

Create a short one-line commit for the current diff.

```text
/commit-this --print
```

Print a detailed commit message for the current diff, then ask whether to add
it to the git tree.

```text
/commit-this --short --print
```

Print a short one-line commit message for the current diff, then ask whether to
add it to the git tree.

## No-Diff Error

If there is no staged or unstaged git diff, return a short funny or silly error
message immediately.

Example:

```text
Commit machine says: no diff, no cookie.
```

## Commit Behavior

- Default behavior is to create a git commit, not merely print a message.
- `--print` is the only flag that changes the default into print-only preview.
- `--short` only changes the message format; it does not change whether a
  commit is created.
- When committing, prefer `git commit -m "<subject>"` for short mode and a
  commit message file or multiple `-m` flags for detailed mode.
- After committing, report the commit hash and subject briefly.

## Commit Rules

- Use one logical change per commit.
- Make the subject specific, imperative, and action-based.
- Avoid vague subjects like `update`, `fix stuff`, `changes`, `wip`, or `final`.
- Do not invent implementation details.
- Do not claim tests or checks were run unless the user provided them or you ran them.
- Prefer these types when they fit: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `build`, `ci`, `perf`, `style`, `revert`.

## Short Mode

Use only when the user passes `--short` or explicitly asks for a concise
one-line commit.

Format:

```text
<type>: <clear action-based summary>
```

Examples:

```text
fix: correct refresh token cookie expiry
docs: document local logging setup
chore: remove unused profile imports
```

## Detailed Mode

Use by default. Especially use for feature work, bug fixes, backend/API
changes, auth or security changes, database/schema changes, infrastructure
changes, architecture refactors, or anything that needs useful historical
context.

Format:

```text
<type>: <clear action-based summary>

<why this change exists>

<what changed and how it changed>

<verification, risks, migrations, compatibility notes, or follow-up work if relevant>
```

Keep the body useful but not padded. Use bullets when there are multiple
meaningful changes.

Example:

```text
feat: add request ID middleware

Adds request ID support so API logs can be correlated across middleware,
controllers, exception filters, and frontend error reports.

- Reads x-request-id when provided
- Generates a request ID when the header is missing
- Echoes the request ID in the response header
- Attaches requestId to the request context

This is foundational for structured logging and future tracing work.
```

## Splitting Broad Changes

If the diff contains unrelated work, do not force it into one message. Recommend
separate commits:

```text
The current diff appears to contain multiple logical changes. Split it into:

1. feat: add request ID middleware
2. docs: document structured logging fields
3. test: add request logging middleware coverage
```

## Quality Check

Before returning, verify the message is focused, specific, reviewable,
reversible, and free from filler.

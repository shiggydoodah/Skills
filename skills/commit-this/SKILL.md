---
name: commit-this
description: Write, review, or improve Git commit messages in short or detailed modes. Use when the user invokes /commit-this, asks for a commit message, commit title, commit body, conventional commit, or help splitting broad changes into reviewable commits.
---

# Commit This

## Goal

Write commit messages that make history useful: clear scope, specific intent,
reviewable changes, and truthful verification or risk notes when relevant.

## Workflow

1. Inspect the provided diff, summary, staged changes, or user description.
2. If there is no diff, staged change, repo change, or usable summary to inspect, instantly return a silly error message instead of writing a commit.
3. Decide whether the change is one logical commit.
4. If unrelated work is mixed together, recommend splitting it into separate commits.
5. Choose the requested mode. If the user passes `short`, use short mode. If no mode is requested, use detailed mode.
6. Return the commit message only unless the user asks for explanation.

## Invocation

```text
/commit-this
```

Write a detailed commit message for the current diff.

```text
/commit-this short
```

Write a short one-line commit message for the current diff.

## No-Diff Error

If there is nothing to write a commit message from, return a short funny or
silly error message immediately.

Example:

```text
Commit machine says: no diff, no cookie.
```

## Commit Rules

- Use one logical change per commit.
- Make the subject specific, imperative, and action-based.
- Avoid vague subjects like `update`, `fix stuff`, `changes`, `wip`, or `final`.
- Do not invent implementation details.
- Do not claim tests or checks were run unless the user provided them or you ran them.
- Prefer these types when they fit: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `build`, `ci`, `perf`, `style`, `revert`.

## Short Mode

Use only when the user passes `short` or explicitly asks for a concise
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

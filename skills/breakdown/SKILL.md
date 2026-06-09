---
name: breakdown
description: Breaks an existing plan, PRD, or tech spec into small, paste-ready task tickets — one self-contained prompt per task that drops cleanly into the current context window or a fresh one. Use when the user says "breakdown", "break this down", wants a plan/PRD/spec split into actionable tasks, steps, or tickets, or wants implementation work they can copy-paste one task at a time. Does not write the plan/PRD/spec — those must already exist.
---

# breakdown

Takes an existing plan and breaks it into small, paste-ready task tickets. Each
ticket is one self-contained prompt: copy it into the current context window or a
brand-new one and an agent has everything it needs to do that one task — no extra
explanation required.

This skill only creates the tickets. It never writes the plan, PRD, or tech spec —
those must already exist.

## Input

The source plan must already exist. Read it in priority order:

1. A `@file` passed to the skill (e.g. `/breakdown @docs/features/billing/tech_spec.md`).
2. `tech_spec.md`, `prd.md`, or `plan.md` in the current feature folder or project
   root (any case — `TECH_SPEC.md`, `PRD.md`, `PLAN.md` all count).
3. A plan the user points to in the conversation.

If no plan exists, stop and tell the user to create one first (e.g. `/create-prd`
or `/create-tech-spec`). Never invent the plan.

## Output

Write the tickets into the feature's own folder — the same folder the plan already
lives in (create it only if one doesn't exist yet). One folder per feature. The
plan/PRD/tech-spec already exist there and are never written by this skill.

Name the files by whether the feature spans both disciplines:

- Feature with **both** backend and frontend work — two independently-numbered
  lanes, each starting at `01`, with a `be-` / `fe-` infix:

  ```text
  docs/features/<feature>/
    tech_spec.md          # input — already exists
    01-be-<feature>.md    # backend task 1
    02-be-<feature>.md    # backend task 2
    01-fe-<feature>.md    # frontend task 1
    02-fe-<feature>.md    # frontend task 2
  ```

- **Backend-only or frontend-only** feature — no `be-`/`fe-` infix, just number
  the tasks:

  ```text
  docs/features/<feature>/
    tech_spec.md          # input — already exists
    01-<feature>.md       # task 1
    02-<feature>.md       # task 2
  ```

Rules:

- One task per file, commit-sized, numbered in execution order (dependencies
  first). Backend and frontend never share a file.
- Use the `be-`/`fe-` infix only when a single feature contains both disciplines;
  a single-discipline feature omits it.
- Each file is one task — tell them apart by number and the Goal inside.
- Tickets are commit-worthy artifacts unless the user says otherwise.

## Process

1. **Read the plan** — read the source and any nearby docs that clarify scope.
   Never guess at requirements.
2. **Explore the codebase** — inspect the real files, directories, entrypoints,
   patterns, and tests the work touches, so tickets reference true paths.
3. **Break it down** — split into the smallest tasks that each leave the repo in a
   working state. Split by reviewable progress, not by layer — except backend vs
   frontend, which are always separate tickets.
4. **Write the tickets** — create the numbered files, then report their paths and
   the order to run them in.

## Ticket format

Every ticket must stand on its own in a fresh context window, after compaction, or
in the same session. Give enough grounding to act immediately without pasting large
source excerpts. Keep each ticket well under a typical context window; if it needs
more, split it.

```md
# <NN> — <Backend|Frontend>: <task title>

Feature: <feature name>   ·   Plan: <relative path to the plan>
Prerequisites: <none, or the tickets that must be done first>

## Goal
<one commit-sized outcome, in plain terms>

## Context
<the slice of the plan that matters for this task>

## Files
- Inspect: <real paths to read first>
- Likely edit: <real paths>

## Notes
<patterns, APIs, commands, constraints to follow; out-of-scope callouts>

## Done when
- <acceptance criteria, in behaviour terms>
- Tests pass — run the project's tests, build, and lint. The task is NOT complete
  until these are green and the behaviour above is verified.
- You have summarised exactly what changed.
- You have asked the user to review before committing — do not commit yourself.
```

## Required gate — every ticket, no exceptions

Each ticket's "Done when" must always include these two, worded generically so the
ticket works in any agent or repo:

1. **Verify** — nothing is complete until tests/build/lint pass and the described
   behaviour is confirmed.
2. **Summarise + review** — end by summarising what changed and asking the user to
   review before committing.

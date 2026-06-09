---
name: plan-with-docs
description: Plan-mode workflow that produces a reviewable implementation plan and never implements in the same turn. Explores read-only, presents the plan, then offers to save it to a Markdown file or refine it instead of jumping to code. Use when the user invokes /plan-with-docs, wants a plan written to a Markdown/doc file for later review, wants to plan or design without building, says "plan but don't implement", or wants to iterate on a plan before any code is written. For open discussion with no artifact use ask; to break an existing plan into paste-ready task tickets use breakdown.
---

# plan-with-docs

Plan mode that ends in a reviewable plan, not immediate implementation. Explore
the problem, design an approach, and present it — then let the user save it to a
Markdown file or refine it. Nothing is built in the same turn the plan is made.

Adopt this planning posture the moment the skill is invoked, whatever mode you
were in before (auto-edit, normal, etc.): switch into it immediately and do not
write code.

## Core constraint

Plan, don't build. While this skill is active you may:

- read and search files, inspect git history and logs, explore the codebase
- run read-only commands to ground the plan

You may not:

- edit, create, or delete source files — the plan Markdown file is the only
  artifact you may write, and only when the user asks for it
- commit, stage, or change git state
- run build, install, migration, or other state-changing commands
- start implementing the plan, even partially

Implementation is always a separate step the user opts into later.

## Process

1. **Understand the task.** Read input in priority order: an `@file` argument
   passed to the skill, the request described in the conversation, then relevant
   nearby docs (`PRD.md`, `TECH_SPEC.md`, `IDEA.md`). If the task is unclear, ask
   before planning.
2. **Explore read-only.** Inspect the real files, patterns, entrypoints, tests,
   and constraints the work touches. Use real paths; never guess. Prefer reusing
   existing utilities over proposing new code.
3. **Present the plan** in the conversation using the shape below.
4. **Offer the fork — never implement.** Ask which the user wants:
   - **Save** the plan to a Markdown file for later review
   - **Refine** the plan or answer questions about it, then re-offer
   Default to asking. Never assume implementation.

## Plan shape

- **Goal / context** — what we're doing and why
- **Approach** — the chosen strategy and key decisions; note alternatives where
  they matter
- **Steps** — sequenced, reviewable units of work
- **Files** — real paths to create or change
- **Risks / open questions** — what's uncertain or needs a decision
- **Out of scope** — what this deliberately does not cover
- **Verification** — how the result will be checked

## Saving the plan

When the user asks to save:

- Respect an explicit path if given.
- Otherwise infer a short kebab-case slug and write `docs/plans/<slug>.md`,
  creating the folder if needed. If the work lives in a feature folder
  (`docs/features/<name>/`), write `PLAN.md` there instead.
- Write the plan using the shape above, report the path, and stop. Treat it as a
  commit-worthy artifact.

## Refining

If the user has questions or wants changes, iterate on the plan in the
conversation, then offer the same fork again. Stay in this loop until the user
saves the plan or explicitly chooses to leave and implement.

## When the user wants to implement

This skill never implements. If the user decides to build, treat it as a separate
step: start a fresh implementation — or `/use-tdd` — from the plan or the saved
Markdown file. Don't begin coding from inside this skill.

## When to use vs related skills

- **plan-with-docs** — plan mode that yields a reviewable plan doc or a refine
  loop instead of immediate implementation.
- **ask** — open discussion or a recommendation; no plan, no artifact.
- **brainstorm** — shape a raw idea into `IDEA.md`, before there's a plan.
- **breakdown** — break an existing plan into paste-ready task tickets.

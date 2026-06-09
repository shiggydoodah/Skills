---
name: brainstorm
description: Shapes a raw, in-your-head idea into a scoped, non-technical concept brief (IDEA.md) through a collaborative one-question-at-a-time interview — the pre-planning step that comes before there is any plan to refine. Use when the user has an unformed idea to flesh out or scope, says "brainstorm", "help me scope/shape this idea", or "I have an idea", or wants a feature/product concept captured before planning. For stress-testing a plan or document that already exists, use grill-me instead.
---

# brainstorm

Turn a rough idea into an `IDEA.md` concept brief that downstream skills can refine. This is the front of the feature-docs pipeline:

`brainstorm` → `grill-me` → `create-prd` → `create-tech-spec`

Stay at the user / feature / product level. Do not design architecture or choose technologies — that comes later.

## When to use vs grill-me

- **brainstorm** — there is no plan yet, just an idea. Help discover and shape it.
- **grill-me** — a plan, design, or document already exists. Stress-test it.

## Interview Approach

Interview the user one question at a time. For each question, provide your recommended answer.

1. **Diverge first.** Start from whatever the user has — a sentence or a vague notion. Explore the problem, who it is for, and the value before discussing any solution. Surface angles they may not have considered.
2. **Then converge.** Pin down the core concept, then draw a first-version scope boundary — separate must-haves from nice-to-haves.
3. Keep it non-technical. Ask the user about product intent; if a question can be answered by exploring the codebase (e.g. what already exists), explore the codebase instead.

## Output: IDEA.md

Infer a kebab-case feature name from the idea, propose `docs/features/[feature-name]/`, then build `IDEA.md` incrementally as decisions settle:

```md
## Concept
[One paragraph: what the idea is.]

## Problem / Motivation
[The itch it scratches; why it matters now.]

## Target Users
[Who it is for.]

## Value / Success
[What good looks like, in plain terms.]

## Core Capabilities
[The handful of things a first version must do — feature level, not technical.]

## Scope
[In for a first version vs. explicitly out.]

## Open Questions
[What is still fuzzy, to resolve later in grill-me / create-prd.]
```

These sections map onto `PRD.md`, so `create-prd` can refine `IDEA.md` directly.

## Patch Behaviour

If `IDEA.md` already exists:

- Read it first
- Apply targeted updates only
- If no changes are needed, exit with a message — do not overwrite

## Reference

See [../create-tech-spec/REFERENCE.md](../create-tech-spec/REFERENCE.md) for directory structure and document conventions.

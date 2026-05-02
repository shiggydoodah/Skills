---
name: create-prd
description: Creates or updates PRD.md from CONTEXT.md and/or IDEA.md. Auto-brainstorms if no context exists. Use when user wants to write a product requirements document, convert brainstorm notes into a PRD, or mentions "create-prd".
---

# create-prd

Produces a structured `PRD.md` from existing feature context.

## Input

Read in order of preference:

1. `CONTEXT.md` (agent session memory — preferred)
2. `IDEA.md` (user-provided seed)

If neither exists, run a domain-specific brainstorm first — focused on extracting product requirements, not general grilling — then write `PRD.md` from the outcome.

## PRD Sections

```md
## Problem Statement

## Target Users

## Goals & Success Criteria

## Non-Goals

## Requirements

## Open Questions
```

## Patch Behaviour

If `PRD.md` already exists:

- Read it first
- Apply targeted updates only
- If no changes are needed, exit with a message — do not overwrite

## Reference

See [../spec-to-code/REFERENCE.md](../spec-to-code/REFERENCE.md) for directory structure and section templates.

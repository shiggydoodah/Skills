---
name: brainstorm
description: Interviews the user about a feature idea to surface requirements and constraints, then writes CONTEXT.md as incremental session memory. Reads IDEA.md as seed if present. Use when user wants to brainstorm a feature, stress-test an idea, or create initial feature context before writing a PRD.
---

# brainstorm

A focused interview that extracts product requirements and constraints from a rough idea.

## Behaviour

Interview the user relentlessly. Walk down each branch of the decision tree, resolving dependencies one-by-one. For each question, provide your recommended answer.

Ask questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.

## Seed Input

If `IDEA.md` exists in the feature directory, read it first and use its content as the starting context. Do not re-ask questions already answered by it.

## CONTEXT.md

Write `CONTEXT.md` incrementally — after each resolved decision branch, not after every message.

Structure:

```md
## Resolved Decisions

- [decision]: [outcome]

## Open Questions

- [question]

## Key Constraints

- [constraint]

## Raw Notes

[unstructured content from IDEA.md or session seed]
```

If no feature directory exists yet, create it when writing the first `CONTEXT.md` update.

Can be used standalone or before `/create-prd` and `/create-tech-spec`.

## Reference

See [../create-tech-spec/REFERENCE.md](../create-tech-spec/REFERENCE.md) for directory structure and document formats.

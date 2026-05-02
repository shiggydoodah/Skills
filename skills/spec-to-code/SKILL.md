---
name: spec-to-code
description: Orchestrates the full spec-to-code pipeline — brainstorm → create-prd → create-spec — to take a user from a rough idea to a complete TECH_SPEC.md ready to implement. Use when user wants to go from an idea to a spec, mentions "spec-to-code", or wants to create feature documentation.
---

# spec-to-code

Guides a user from a rough idea to a complete `TECH_SPEC.md` ready to implement.

## Entry

Requires at least one of:

- Inline rough idea (even a single line)
- Path to an existing feature directory

If neither is provided, ask the user for a starting point before continuing.

## Feature Name

Infer a kebab-case slug from the idea. Propose it before creating any files:

> "I'll save to `docs/features/oauth-login/` — ok?"

If pointing to an existing directory, take the name from the path silently.

## State Detection

Read the target directory and skip completed steps:

| State                            | Action                                              |
| -------------------------------- | --------------------------------------------------- |
| `TECH_SPEC.md` exists            | Nothing to do — print implementation handoff        |
| `PRD.md` exists                  | Run `/create-spec` only                             |
| `CONTEXT.md` or `IDEA.md` exists | Run `/create-prd`, then run `/create-spec`          |
| Nothing exists                   | Run `/brainstorm`, then `/create-prd`, then `/create-spec` |

## Implementation Handoff

At the end, print the path to the completed spec and a suggested next step:

```
TECH_SPEC.md is ready at docs/features/[name]/TECH_SPEC.md
Next step: implement using the spec as the implementation plan.
```

## Reference

See [REFERENCE.md](REFERENCE.md) for directory structure, document formats, and section templates.

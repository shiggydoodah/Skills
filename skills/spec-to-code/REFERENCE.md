# spec-to-code Reference

Shared conventions for the `spec-to-code` skill set.

## Directory Structure

Feature docs live at:

```
docs/features/[feature-name]/
├── IDEA.md       # user-provided raw seed (optional, never modified by agent)
├── CONTEXT.md    # agent session memory, updated incrementally
├── PRD.md        # output of /create-prd
└── TECH_SPEC.md  # output of /create-spec
```

Feature names are kebab-case slugs inferred from the idea (e.g. `oauth-login`, `csv-export`).

## Document Ownership

| File           | Owner | Notes                                                |
| -------------- | ----- | ---------------------------------------------------- |
| `IDEA.md`      | User  | Raw seed. Agent reads but never modifies.            |
| `CONTEXT.md`   | Agent | Updated incrementally during brainstorm.             |
| `PRD.md`       | Agent | Written by `/create-prd`. Patched, not overwritten.  |
| `TECH_SPEC.md` | Agent | Written by `/create-spec`. Patched, not overwritten. |

## CONTEXT.md Structure

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

## PRD.md Sections

```md
## Problem Statement

## Target Users

## Goals & Success Criteria

## Non-Goals

## Requirements

## Open Questions
```

## TECH_SPEC.md Core Sections

Always present:

```md
## Overview

## Implementation Plan

## Testing Strategy

## Out of Scope

## Open Questions
```

Backend additions: `## Architecture`, `## Data Models`, `## API / Interface Design`

Frontend additions: `## Component Architecture`, `## State Management`, `## Routing`, `## Design Tokens / Styling`

Fullstack: all backend + frontend sections.

## Patch Policy

Never overwrite an existing document without reading it first. Apply targeted updates only. If no changes are needed, exit with a message.

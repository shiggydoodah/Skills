---
name: create-spec
description: Creates or updates TECH_SPEC.md from an existing PRD.md. Detects project type from the codebase and adapts sections accordingly. Runs a blocking requirements coverage check before finalising. Use when user wants to write a technical specification, turn a PRD into a tech spec, or mentions "create-spec".
---

# create-spec

Produces a structured `TECH_SPEC.md` from an existing `PRD.md`.

## Hard Requirement

`PRD.md` must exist. If not found, exit with:

> "No PRD.md found. Run `/create-prd` first."

Do not auto-chain to `/create-prd`.

## Project Type Detection

Read the codebase (package.json, framework files, folder structure) to detect project type:

| Type      | Additional sections                                                        |
| --------- | -------------------------------------------------------------------------- |
| Backend   | Architecture, Data Models, API / Interface Design                          |
| Frontend  | Component Architecture, State Management, Routing, Design Tokens / Styling |
| Fullstack | All backend + frontend sections                                            |

## Core Sections (always)

- Overview
- Implementation Plan (ordered steps)
- Testing Strategy
- Out of Scope
- Open Questions

## Coverage Check

Before writing `TECH_SPEC.md`, map every PRD requirement to a TECH_SPEC section:

| Requirement      | Covered in   |
| ---------------- | ------------ |
| ✅ [requirement] | ## [section] |

Do not finalise `TECH_SPEC.md` until all requirements are covered.

## Patch Behaviour

If `TECH_SPEC.md` already exists:

- Read it first
- Apply targeted updates only
- If no changes are needed, exit with a message — do not overwrite

## Reference

See [../spec-to-code/REFERENCE.md](../spec-to-code/REFERENCE.md) for directory structure and section templates.

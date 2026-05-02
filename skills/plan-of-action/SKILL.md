---
name: plan-of-action
description: Breaks a big task into a sequenced, actionable plan with rich copy-paste agent prompts. Explores the codebase first to ground every step in real file paths. Use when the user wants to plan a feature before building it, mentions "plan of action", wants fine-grained control over what an agent codes, or wants to break a spec into steps.
---

# plan-of-action

Takes a task or spec and produces a sequenced plan of steps — each with a rich, self-contained agent prompt ready to copy-paste.

## Entry

Accepts input in priority order:

1. `@file` argument passed to the skill (e.g. `/plan-of-action @/TECH_SPEC.md`)
2. `TECH_SPEC.md` in the project root — read automatically if present
3. Free-form description in the conversation

If none of these exist, ask the user to describe the task before continuing.

## Process

### 1. Read the input
Read the spec file or extract the task description from the conversation.

### 2. Explore the codebase
Before generating anything, explore the repo to find:
- Files and directories directly relevant to the task
- Existing patterns, conventions, and abstractions to follow
- Entry points, config files, and integration boundaries

Use real file paths in every agent prompt — never guess.

### 3. Print a summary
Write 2–3 sentences confirming what will be built and the overall approach. This lets the user abort before reading the full plan if the understanding is wrong.

### 4. Generate the sequenced plan
Produce numbered steps in recommended execution order. See output format below.

## Granularity rule

Each step = one deployable chunk / one commit's worth of work. The codebase must be in a working state after each step. Never split by layer (no "add types only" steps).

- Small task → 3–5 steps
- Medium feature → 5–8 steps
- Large feature → 8–15 steps

## Output format

```
## Plan of Action: [task name]

[2–3 sentence summary]

---

### Step N: [title]
**Description:** [what needs to be done]
**Blocked by:** [step numbers] or None

**Agent prompt:**
[rich prompt — includes: specific goal, relevant file paths, what "done" looks like, constraints]

---
```

## Agent prompt requirements

Every agent prompt must be self-contained and include:
- The specific goal for this step
- File paths to read or modify (from codebase exploration)
- Clear acceptance criteria — what "done" looks like
- Any constraints (do not touch X, follow pattern Y, stay consistent with Z)

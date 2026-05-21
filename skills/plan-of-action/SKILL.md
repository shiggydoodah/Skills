---
name: plan-of-action
description: Turns a PRD, tech spec, feature document, feature idea, or implementation request into copy-paste-ready agent prompt Markdown files. Use when the user mentions "plan of action", wants agent prompts, wants to break a spec into implementation prompts, or wants a large task split across commits or PRs.
---

# plan-of-action

Turns a PRD, tech spec, feature document, or feature idea into `AGENT_PROMPT.md`
files that can be copied into coding agents one prompt at a time.

## Input

Read input in priority order:

1. `@file` argument passed to the skill (e.g. `/plan-of-action @/TECH_SPEC.md`)
2. `TECH_SPEC.md`, `PRD.md`, or another obvious feature document in the current
   feature folder or project root
3. Free-form feature idea or implementation request in the conversation

If none of these exist, ask the user to describe the task before continuing.

## Output Location

Respect an explicit output path from the user. Otherwise:

- If the input file is in a feature folder, write prompt files there.
- For root docs or free-form ideas, infer a short slug and write to
  `docs/features/<slug>/`.
- Create the feature folder if needed.
- Treat generated prompt files as commit-worthy artifacts unless the user says
  otherwise.

## Process

1. **Read the source** - read the supplied file or extract the feature request
   from the conversation. Also read nearby docs that clarify scope.
2. **Explore the codebase** - inspect relevant files, directories, manifests,
   patterns, entrypoints, tests, and integration boundaries. Use real file paths
   in prompts; never guess.
3. **Break down the work** - decide whether the task is one commit, one PR with
   multiple commits, or multiple PRs. Split by user-visible, reviewable progress,
   not by technical layer.
4. **Write prompt files** - create the Markdown files and report their paths plus
   the intended PR/commit breakdown.

## Split Rules

Use judgment. Optimize for small, reviewable PRs and for each prompt to leave the
repo in a working state.

- Small task: write `AGENT_PROMPT.md` with one prompt.
- Large but single-PR task: write `AGENT_PROMPT.md` with multiple numbered
  prompts.
- Very large, risky, or naturally staged task: write `01_AGENT_PROMPT.md`,
  `02_AGENT_PROMPT.md`, etc. Each file is one PR.

Each prompt inside a file is one commit-sized unit of work. Avoid splitting into
"types only", "backend only", or "tests only" unless that commit is independently
useful and leaves the repo passing.

## Prompt Requirements

Every prompt must work in the same context, after context compaction, or in a
fresh agent window. Include enough grounding for a new agent to move quickly
without dumping large source excerpts.

Every prompt includes:

- Goal and commit scope
- Relevant source/spec context
- File paths to inspect and likely paths to edit
- Existing patterns, APIs, commands, and constraints to follow
- Acceptance criteria and verification commands
- Explicit out-of-scope notes where helpful

Keep prompts context-efficient. A single prompt should not need more than about
35% of a typical agent context window; split it if it needs too much background
or touches too many unrelated areas.

## File Formats

Use these names:

```text
AGENT_PROMPT.md       # one PR, one or more prompts
01_AGENT_PROMPT.md    # PR 1 of a multi-PR breakdown
02_AGENT_PROMPT.md    # PR 2 of a multi-PR breakdown
```

Use this structure:

```md
# Agent Prompts: <feature or task>

This file represents PR <number or "1">.
Prerequisites: <none, or previous prompt files that must be complete>
PR outcome: <what this PR should contain when finished>

## Prompt 1: <commit-sized title>

<copy-paste-ready prompt>
```

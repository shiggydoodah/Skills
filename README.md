# LL Skills

Practical skills for coding agents.

This is my working collection of agent skills for Claude Code and Codex-style
workflows. Some are small habits I want agents to follow every time. Others are
larger workflows for planning, reviewing, debugging, writing specs, and getting
changes ready for review.

The goal is not to be exhaustive. The goal is to make the useful paths easy to
reach and hard to forget.

## Skills

**Feature docs**

- [`brainstorm`](./skills/brainstorm/SKILL.md) - Stress-test an idea and write `CONTEXT.md` as decisions settle.
- [`create-prd`](./skills/create-prd/SKILL.md) - Turn context or an `IDEA.md` seed into a structured `PRD.md`.
- [`create-tech-spec`](./skills/create-tech-spec/SKILL.md) - Turn `PRD.md` into `TECH_SPEC.md` with adaptive sections and a coverage check.

**Development workflow**

- [`use-tdd`](./skills/use-tdd/SKILL.md) - Test-driven development with a red-green-refactor loop.
- [`run-diagnose`](./skills/run-diagnose/SKILL.md) - Reproduce, isolate, instrument, and fix bugs without guessing.

**Git and GitHub**

- [`commit-this`](./skills/commit-this/SKILL.md) - Stage and create clear short or detailed Git commits, with an optional print-only preview.
- [`create-pr`](./skills/create-pr/SKILL.md) - Create a high-signal draft GitHub PR from the current branch.
- [`review-pr-feedback`](./skills/review-pr-feedback/SKILL.md) - Triage GitHub PR review feedback before changing code.
- [`code-review`](./skills/code-review/SKILL.md) - Review the current feature branch diff by default, or review a GitHub PR with optional inline comments.

**Planning**

- [`plan-of-action`](./skills/plan-of-action/SKILL.md) - Break a task or spec into sequenced, copy-paste-ready agent prompts.

**Codebase context**

- [`create-context`](./skills/create-context/SKILL.md) - Create one compressed `CONTEXT.md` for a target app, package, or directory.
- [`map-context`](./skills/map-context/SKILL.md) - Generate `CONTEXT.md` files and a root `CONTEXT-MAP.md` for faster codebase navigation.

**Utilities**

- [`me-caveman`](./skills/me-caveman/SKILL.md) - Ultra-compressed communication mode.
- [`create-a-skill`](./skills/create-a-skill/SKILL.md) - Create new agent skills with the right structure.

## Install

Install the plugin from a terminal:

```bash
claude plugin marketplace add shiggydoodah/Skills
claude plugin install ll-skills@ll-skills
```

Or run the same flow inside Claude Code:

```text
/plugin marketplace add shiggydoodah/Skills
/plugin install ll-skills@ll-skills
/reload-plugins
```

## Use

```text
/use-tdd
/run-diagnose
/me-caveman
/create-a-skill
/commit-this
/commit-this --short
/commit-this --print
/commit-this --short --print
/create-pr
/create-pr --open
/create-pr .github/templates/documentation-template.md
/review-pr-feedback
/code-review
/code-review --pr https://github.com/org/repo/pull/42
/code-review --inline --pr https://github.com/org/repo/pull/42
/brainstorm
/create-prd
/create-tech-spec
/create-context apps/web
/map-context
```

A few notes:

- `/commit-this` stages the current work and creates a detailed commit by
  default. Use `--short` for a one-line commit, and `--print` to preview the
  message before deciding whether to add it to the git tree.
- `/create-pr` creates a draft GitHub PR by default. Use `/create-pr --open` to
  create it and open it in the browser, or pass a Markdown template path to use
  an alternate PR template.
- `/review-pr-feedback` triages PR review feedback first and waits for approval
  before changing code or replying to comments.
- `/code-review` performs a review-only local PR-style review of the current
  feature branch diff. Use `--pr <URL>` or a bare GitHub PR URL to review a PR,
  and `--inline` to post findings as GitHub review comments. If present,
  `.github/instructions/pr-review.instructions.md` is used as project-specific
  review guidance; otherwise PR reviews use the PR description for context.

## Feature Docs

These skills turn a loose idea into implementation-ready feature docs.

By default, feature documents live under `docs/features/[feature-name]/`:

```
docs/features/my-feature/
├── IDEA.md        # optional user-provided seed (never modified by the agent)
├── CONTEXT.md     # agent session memory, updated incrementally during brainstorm
├── PRD.md         # product requirements document
└── TECH_SPEC.md   # technical specification ready for implementation
```

Run the steps in sequence from a rough idea:

```text
/brainstorm add user authentication with GitHub OAuth
/create-prd
/create-tech-spec
```

The agent infers a feature name during brainstorm, proposes the directory, then
uses the saved context to write `PRD.md` and `TECH_SPEC.md`.

Resume an in-progress feature by running the next step from its directory:

```text
/create-prd
/create-tech-spec
```

Each step also works on its own:

```text
/brainstorm              # focused interview, writes CONTEXT.md
/create-prd              # PRD.md from CONTEXT.md or IDEA.md
/create-tech-spec        # TECH_SPEC.md from PRD.md (errors if no PRD.md)
```

`/create-tech-spec` detects the project type from the codebase (backend,
frontend, or fullstack), adapts the `TECH_SPEC.md` sections, and checks that
every PRD requirement is covered before finalising the document.

## Local Development

Test the plugin directly from this checkout:

```bash
claude --plugin-dir .
```

Validate the Claude manifests:

```bash
claude plugin validate .claude-plugin/plugin.json
claude plugin validate .claude-plugin/marketplace.json
```

Check skill discovery:

```bash
npx skills@latest add . --list
```

## Codex

This repo includes `.codex-plugin/plugin.json` so the same skills are ready to
be packaged for Codex plugin workflows. Claude Code is the primary install path
for now.

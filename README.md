# LL Skills

Practical coding-agent skills for Claude Code and Codex.

This is my public collection of agent skills: skills I write myself, skills I
adapt, and useful skills I find along the way. The goal is to keep the
collection practical, readable, and easy to install across Claude Code and
Codex-style workflows.

## Skills

**Spec-to-code pipeline**

- [`spec-to-code`](./skills/spec-to-code/SKILL.md) - Orchestrate the full pipeline from a rough idea to a complete TECH_SPEC.md.
- [`brainstorm`](./skills/brainstorm/SKILL.md) - Grill-me session that extracts requirements and writes CONTEXT.md incrementally.
- [`create-prd`](./skills/create-prd/SKILL.md) - Produce a structured PRD.md from brainstorm context or an IDEA.md seed.
- [`create-spec`](./skills/create-spec/SKILL.md) - Produce a TECH_SPEC.md from a PRD.md with adaptive sections and a coverage gate.

**Development workflow**

- [`tdd`](./skills/tdd/SKILL.md) - Test-driven development with a red-green-refactor loop.
- [`diagnose`](./skills/diagnose/SKILL.md) - Disciplined debugging for bugs and performance regressions.

**Git and GitHub**

- [`add-commit`](./skills/add-commit/SKILL.md) - Write clear short or detailed Git commit messages.
- [`create-pr-github`](./skills/create-pr-github/SKILL.md) - Create a high-signal draft GitHub PR from the current branch.
- [`review-pr-feedback`](./skills/review-pr-feedback/SKILL.md) - Triage and address GitHub PR review feedback safely.
- [`do-review`](./skills/do-review/SKILL.md) - Review the current feature branch diff before opening or updating a PR.

**Utilities**

- [`grill-me`](./skills/grill-me/SKILL.md) - Interview and stress-test a plan until the decisions are clear.
- [`caveman`](./skills/caveman/SKILL.md) - Ultra-compressed communication mode.
- [`write-a-skill`](./skills/write-a-skill/SKILL.md) - Create new agent skills with the right structure.

## Install In Claude Code

From your terminal:

```bash
claude plugin marketplace add shiggydoodah/Skills
claude plugin install ll-skills@ll-skills
```

Or inside Claude Code:

```text
/plugin marketplace add shiggydoodah/Skills
/plugin install ll-skills@ll-skills
/reload-plugins
```

Invoke the skills as:

```text
/ll-skills:tdd
/ll-skills:diagnose
/ll-skills:grill-me
/ll-skills:caveman
/ll-skills:write-a-skill
/add-commit
/add-commit short
/create-pr
/create-pr --open
/review-pr-feedback
/do-review
/spec-to-code
/spec-to-code docs/features/my-feature/
/brainstorm
/create-prd
/create-spec
```

`/create-pr` creates a draft GitHub PR by default. Use `/create-pr --open` to
create the draft PR and open it in the browser, or ask for title/body only when
you do not want a PR created.

`/review-pr-feedback` triages PR review feedback first and waits for approval
before making code changes or replying to comments.

`/do-review` performs a review-only local PR-style review of the current feature
branch diff and stops on `main` or `master`.

### spec-to-code pipeline

Takes a rough idea all the way to a `TECH_SPEC.md` ready to implement. Each
skill can be used independently or chained together via `/spec-to-code`.

Feature documents live at `docs/features/[feature-name]/`:

```
docs/features/my-feature/
├── IDEA.md        # optional user-provided seed (never modified by the agent)
├── CONTEXT.md     # agent session memory, updated incrementally during brainstorm
├── PRD.md         # product requirements document
└── TECH_SPEC.md   # technical specification ready for implementation
```

**Full pipeline** — point at a rough idea and let the orchestrator drive:

```text
/spec-to-code add user authentication with GitHub OAuth
```

The agent infers a feature name, proposes the directory path, then runs
`/brainstorm` → `/create-prd` → `/create-spec` in sequence.

**Resume an in-progress feature** — point at an existing directory and the
orchestrator detects how far along it is and picks up from there:

```text
/spec-to-code docs/features/github-oauth/
```

**Run individual steps** — each sub-skill works standalone:

```text
/brainstorm              # grill-me session, writes CONTEXT.md
/create-prd              # PRD.md from CONTEXT.md or IDEA.md
/create-spec             # TECH_SPEC.md from PRD.md (errors if no PRD.md)
```

`/create-spec` detects the project type from the codebase (backend, frontend,
or fullstack) and adapts the TECH_SPEC sections accordingly. It runs a
blocking requirements coverage check before writing the final document to
ensure every PRD requirement is accounted for.

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

Check skill discovery with `skills.sh`:

```bash
npx skills@latest add . --list
```

## Codex

This repo includes `.codex-plugin/plugin.json` so the same skills are ready to
be packaged for Codex plugin workflows. Claude Code distribution is the primary
install path for now.

## Attribution

The initial skills are copied from Matt Pocock's
[`mattpocock/skills`](https://github.com/mattpocock/skills) repo under the MIT
License.

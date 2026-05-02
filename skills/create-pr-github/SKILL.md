---
name: create-pr-github
description: Prepare and create a high-signal GitHub pull request using the repository's PR template and the current branch diff. Use when the user invokes /create-pr, asks to create a PR, open a draft PR, draft a PR description, write a pull request title/body, or prepare a pull request for review.
---

# Create PR

## Goal

Create a draft GitHub PR that helps reviewers, future maintainers, and the author. Keep it high signal, scoped to the actual change, and structured by the repo's PR template.

Default behavior: open a draft PR on GitHub unless the user explicitly asks for title/body only, says not to create a PR, or asks for a non-draft PR.

Flag behavior:

- `/create-pr --open`: create the draft PR and open the resulting GitHub PR page in the browser.
- `/create-pr --no-create`, `body only`, `title/body only`, or equivalent: return the title/body without creating a PR.
- If the user explicitly asks for a ready-for-review PR, create it without `--draft`.

## Workflow

1. **Check the current branch.** Run `git branch --show-current`. If the current branch is `main` or `master`, stop immediately and tell the user:

   > Refusing to create a PR from `main`/`master`. Check out or create a feature branch, then re-run `/create-pr`.

   Do not inspect the diff. Do not draft a PR. Do not create a PR. Exit.

2. **Check for the PR template.** Look for `.github/PULL_REQUEST_TEMPLATE.md` at the repo root.
3. **If the template is missing**, stop immediately and tell the user:

   > No `.github/PULL_REQUEST_TEMPLATE.md` found. Create one in your repo with the sections you want every PR to follow, then re-run `/create-pr`. The template is the source of structure — without it, this skill will not guess.

   Do not draft a PR. Do not invent a template. Exit.

4. **Read the template** to learn the required sections, checkboxes, and ordering.
5. **Inspect the change.** Run in parallel:
   - `git status` (no `-uall`)
   - `git diff` for unstaged
   - `git diff --staged` for staged
   - `git log <base>..HEAD` and `git diff <base>...HEAD` for the full branch range (default base: `main`)
6. **Detect scope.** Decide which areas are _materially_ affected (backend, frontend, packages, docs, tooling, infra). Incidental touches do not count.
7. **Draft the title.** See [Title rules](#title-rules).
8. **Fill the template** section by section, following the [PR quality principles](#pr-quality-principles). Remove sections that do not apply rather than filling them with `N/A`.
9. **Verification block.** Only claim a command passed if it was actually run. If the user has not run lint/typecheck/test, leave them blank or mark `not run` — never fabricate `pass`.
10. **Create the GitHub PR by default.** Push the current branch if needed, then run `gh pr create --draft --title "..." --body-file <temp-file>` unless the user explicitly asked not to create a PR. Use `--base <base>` when the base branch was detected or requested. Use `--web` only when the user wants to finish creation manually in the browser.
11. **Apply labels** after creation when suitable labels already exist in the repo.
12. **Open the PR page** with `gh pr view --web` when the user passed `--open`.
13. **Return the PR URL, title, and body.** If creation was disabled, return only the title and body.

## Title rules

Format: `[FEAT-###]: ...`, `[BUG]: ...`, `[CHORE]: ...` (or whatever prefix style the template/repo history uses — check recent PR titles via `gh pr list` if unsure).

- Specific, searchable, action-based.
- Under 70 characters when possible.
- No trailing period.

Good: `[FEAT-034]: Add request ID middleware to backend logs`
Weak: `[FEAT-034]: Updates`, `[BUG]: Fix stuff`

## PR quality principles

Apply these when filling the template:

- **High signal over completeness.** A small docs PR can be short. A backend architecture PR should be detailed. Match detail to risk.
- **Explain the problem, not just the diff.** The summary must answer: what problem, what changed, why this approach, what impact, what is out of scope.
- **Guide the reviewer.** Name the files to start with, the highest-risk areas, and decisions worth challenging.
- **Be explicit about scope.** Tick only materially-affected boxes. If `Backend` is checked, the backend impact checklist and verification matrix are required.
- **Make risk visible.** Name API/contract, auth, data, concurrency, observability, performance, rollout, and post-deploy concerns. Do not pretend risk does not exist.
- **Truthful verification.** Use `pass`, `fail -> fixed -> pass`, `blocked`, or `skipped (docs-only; low risk)`. Never claim a command passed unless it actually ran.
- **Concrete manual checks.** "Clicked around and it seemed fine" is not a check. Give a flow, expected outcome, and result.
- **Remove empty sections.** Delete a section instead of filling it with `N/A`.

## Labels

After drafting, suggest labels based on material scope. Common set: `bug`, `frontend`, `backend`, `tooling`, `config`, `docs`, `infra`, `refactor`. A PR may have several. Skip labels for areas only incidentally touched (a typo fix in a backend README is `docs`, not `backend`).

When creating the PR, apply labels with `gh pr edit <num> --add-label ...` after creation, but only labels that already exist on the repo (`gh label list`).

## Output shape

If PR creation was disabled, return two clearly separated blocks:

```text
TITLE:
<the title>

BODY:
<the filled template>
```

If creating the PR, write the body to a temporary file and run:

```bash
gh pr create --draft --title "$title" --body-file "$body_file"
```

Use `--draft` unless the user explicitly requested a ready-for-review PR. After creation, return:

```text
PR:
<GitHub URL>

TITLE:
<the title>

BODY:
<the filled template>
```

If `/create-pr --open` was used, run `gh pr view --web` after creation.

## What good looks like

The PR should make these answerable at a glance: what problem, what changed, why this approach, where to start review, what the risk is, how it was verified, what was left out, what future maintainers should know.

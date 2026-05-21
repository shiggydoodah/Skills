---
name: create-pr
description: Create or draft a high-signal GitHub pull request from the current branch. Use when the user invokes /create-pr, asks to create/open a PR, draft a PR description, write PR title/body, or prepare GitHub changes for review.
---

# Create PR

Prepare a GitHub PR that is scoped to the actual branch diff, follows the repo's PR template, and is useful to reviewers.

Defaults:

- Target GitHub and use `gh` for PR creation.
- Create a draft PR unless the user asks for title/body only, says not to create a PR, or asks for ready-for-review.
- Use `.github/PULL_REQUEST_TEMPLATE.md` at the repo root unless the user passes a Markdown template path, e.g. `/create-pr .github/templates/documentation-template.md`.

Flags:

- `/create-pr --open`: create the PR and open it in the browser.
- `/create-pr --no-create`, `body only`, `title/body only`: return the title/body without creating a PR.
- Ready-for-review wording: create without `--draft`.

## Workflow

1. Run `git branch --show-current`. If on `main` or `master`, stop:

   > Refusing to create a PR from `main`/`master`. Check out or create a feature branch, then re-run `/create-pr`.

2. Choose the PR template:
   - Default: `.github/PULL_REQUEST_TEMPLATE.md` from the repo root.
   - Explicit path: resolve the supplied Markdown path from the repo root first, then current working directory. Use it directly and do not fall back.
   - If missing, stop. For default: say no `.github/PULL_REQUEST_TEMPLATE.md` was found. For explicit: say no PR template was found at the supplied path.

3. Inspect the change in parallel:
   - `git status` (no `-uall`)
   - `git diff`
   - `git diff --staged`
   - `git log <base>..HEAD` and `git diff <base>...HEAD` (default base: `main`, unless detected/requested otherwise)

4. Fill the template:
   - Keep the template's section order, required checkboxes, and wording.
   - Explain what problem changed, why this approach, reviewer entry points, material risks, verification, and anything intentionally out of scope.
   - Remove irrelevant optional sections instead of writing `N/A`.
   - Tick only materially affected areas; incidental files do not count.

5. Draft the title using the repo's style from the template or recent PRs. Prefer a specific, searchable, action-based title under 70 characters with no trailing period.

6. Verification must be truthful. Only mark commands as passing if they were actually run; otherwise use `not run`, `blocked`, or `skipped (docs-only; low risk)`.

7. Create the PR unless disabled:

   ```bash
   gh pr create --draft --title "$title" --body-file "$body_file"
   ```

   Omit `--draft` only for ready-for-review PRs. Add `--base <base>` when requested or detected. Use `--web` only when the user wants manual browser creation.

8. After creation, add suitable existing labels only (`gh label list`, then `gh pr edit <num> --add-label ...`). Common labels: `bug`, `frontend`, `backend`, `tooling`, `config`, `docs`, `infra`, `refactor`.

9. If `/create-pr --open` was used, run `gh pr view --web`.

## Output

If PR creation was disabled:

```text
TITLE:
<title>

BODY:
<filled template>
```

If a PR was created:

```text
TITLE:
<title>

PR:
<GitHub URL>
```

---
name: code-review
description: Perform a focused PR-style code review. Use local branch review by default when the user says "code-review" or asks to review local changes. Use PR review mode when the user passes --pr, provides a GitHub PR URL, or asks for inline PR comments with --inline. Supports project review instructions, PR description context, optional base branch, path, focus area, and GitHub inline comment posting for open or draft PRs.
---

# Code Review

## Goal

Review code changes before merge. By default, review local code changes on a
feature branch before a pull request is opened or updated. When the user passes
`--pr`, provides a GitHub PR URL, or uses `--inline`, review the pull request
diff instead.

Keep the review focused on the diff and changed code. This skill is review-only
by default: do not edit files, apply fixes, commit changes, or rewrite code
unless the user explicitly asks for implementation after the review.

The review should answer:

- Is this branch safe to merge?
- What must be fixed first?
- What should be improved if practical?
- What risks or test gaps remain?
- Which changed code deserves the most attention?

## Trigger

Run this workflow when the user says:

```text
code-review
```

The user may specify a target branch, base branch, file or directory, whether to
include uncommitted changes, a focus area, a PR URL, or inline GitHub posting.
Examples:

```text
code-review
code-review against main
code-review this branch against develop
code-review only apps/backend
code-review focus on security and auth
code-review include unstaged changes
code-review --pr
code-review --pr https://github.com/org/repo/pull/42
code-review https://github.com/org/repo/pull/42
code-review --inline --pr https://github.com/org/repo/pull/42
code-review --inline https://github.com/org/repo/pull/42
```

## Scope

Do not scan the whole codebase except as needed to understand changed files.

Choose review mode from the invocation:

- **Local mode**: default when there is no `--pr`, no PR URL, and no `--inline`.
- **PR mode**: use when `--pr` is present, when the user provides a URL, or when
  `--inline` is present.
- **Inline mode**: PR mode plus GitHub comment posting, only when `--inline` is
  present.

Local mode scope:

- current branch diff compared with the detected base branch
- staged and unstaged changes when present, unless the user asked for committed
  changes only
- surrounding code only when needed to understand the changed code

PR mode scope:

- the GitHub PR diff
- PR metadata such as base branch, head branch, author, URL, state, and draft
  status when available
- surrounding code only when available and needed to understand changed code

Do not produce broad architecture feedback unrelated to the reviewed diff. Do
not review unrelated legacy code unless the diff touches it directly.

`code-review --pr` reviews code changes. It is not the same as
`review-pr-feedback`, which triages existing reviewer comments, requested
changes, and review threads.

## Local Branch Safety

Apply this section in local mode. For current-branch PR discovery, use
`PR Resolution` instead.

1. Verify the current directory is a git repository. If not, exit:

   ```text
   I did not run the review because this directory is not a git repository.
   ```

2. Detect the current branch with `git branch --show-current`.
3. If the current branch is `main` or `master`, stop before reviewing:

   ```text
   You are currently on `main`, so I will not run a feature-branch review by default. Please switch to a feature branch or specify a branch/diff to review.
   ```

4. If the requested files or paths do not exist, exit with a short reason and
   the next best action.

Do not apply the `main` or `master` local branch stop when the user provided a
valid PR URL. URL-based PR review does not depend on the current local branch.

## Local Base Branch

Use this section in local mode. Use the base branch specified by the user when
provided.

If no base branch is specified, detect the most likely base branch in this
order:

1. `origin/main`
2. `main`
3. `origin/master`
4. `master`
5. repository default branch if available

If no base branch can be detected, ask the user to specify it.

Use the merge base between the current branch and base branch:

```bash
git merge-base HEAD <base>
git diff <merge-base>...HEAD
```

If uncommitted changes exist, mention them and include them in the review unless
the user explicitly asked for committed changes only. Inspect them with
`git diff`, `git diff --staged`, and relevant untracked files.

Exit if there are no local branch changes compared with the base branch and no
included uncommitted changes:

```text
I did not run the review because there are no local changes compared with `<base>`.
```

## PR Resolution

Use this section in PR mode.

Resolve the PR in this order:

1. If `--pr <URL>` is provided, use that URL.
2. If a URL is provided without `--pr`, treat it as the PR URL.
3. If `--pr` is provided without a URL, discover the open or draft GitHub PR for
   the current branch.
4. If `--inline` is provided without a URL, discover the open or draft GitHub PR
   for the current branch.

For URL-based PR review:

- Support GitHub pull request URLs only, matching
  `https://github.com/<owner>/<repo>/pull/<number>`.
- If the URL is not a GitHub PR URL, exit:

  ```text
  I did not run the PR review because the provided URL is not a GitHub pull request URL.
  ```

For current-branch discovery:

- Verify the current directory is a git repository.
- Detect the current branch with `git branch --show-current`.
- If the current branch is `main` or `master`, exit:

  ```text
  I did not run the PR review because no PR URL was provided and the current branch is `main`.
  ```

- Find the open or draft GitHub PR associated with the current branch.
- If no PR exists, exit:

  ```text
  I did not run the PR review because I could not find an open or draft GitHub PR for the current branch.
  ```

After resolving the PR:

- Verify the PR state is open. Draft PRs are valid because they are still open.
- If the PR is closed or merged, exit:

  ```text
  I did not run the PR review because the pull request is closed or merged.
  ```

- Gather the PR diff and changed files using available GitHub tooling. Prefer
  structured GitHub connector data when available; use `gh pr view`,
  `gh pr diff`, or `gh api` when needed for current-branch discovery, diff
  content, or inline comment anchors.
- If GitHub auth, repository access, or PR lookup fails, exit with the concrete
  blocker and the next best action.

## Review Instructions and PR Context

Before reviewing findings, load project-specific review guidance when available.

1. Look for `.github/instructions/pr-review.instructions.md` at the root of the
   repository being reviewed.
2. If the file exists, read it and apply it as review guidance for what to
   prioritize, how to judge risk, and any project-specific review conventions.
3. If the file does not exist and PR mode is active, read the PR title and
   description and use them as intent context for the review.
4. If both are available, prefer the repository instructions for review policy
   and use the PR description only for change intent and scope.
5. If neither source is available, continue with the standard checks in this
   skill.

Do not let project instructions override the safety rules in this skill:
`code-review` remains review-only, and GitHub posting still requires the
posting rules below.

## Large Diff Handling

This skill is not for large whole-codebase reviews. If the diff is too large for
a high-quality single-pass review, do not perform a shallow review.

Treat the diff as too large when it is clearly broad enough to reduce review
quality, such as dozens of unrelated files, multiple domains plus generated
code, or thousands of changed lines.

When exiting for size:

1. Summarize the size and shape of the diff.
2. Warn that it is too large for a reliable single-pass review.
3. Recommend narrowing the scope.
4. Offer review slices based on changed files.

Example:

```text
This branch changes 84 files across backend, frontend, and generated API code. That is too large for a reliable single-pass local review. I recommend reviewing it in slices:
1. Backend API and service changes
2. Frontend UI changes
3. Tests
4. Generated files/config
```

Do not produce low-confidence findings just to fill the review.

## Review Process

1. Gather context:
   - mode: local, PR, or inline
   - project review instructions from
     `.github/instructions/pr-review.instructions.md`, when present
   - changed files and stats
   - reviewed diff
   - requested path or focus filters
   - for local mode: `git status --short`, current branch, detected or specified
     base branch, merge base, branch diff, and staged or unstaged diff when
     included
   - for PR mode: PR URL, state, draft status, author, base branch, head branch,
     title, description when no local review instructions exist, changed files,
     PR diff, and available inline comment anchors
2. Identify domains touched: backend, frontend, tests, tooling, config, infra,
   docs, or generated code.
3. Inspect nearby code only where needed to understand the changed behavior.
4. Review changed code for the baseline checks and domain-specific checks below.
5. De-duplicate overlapping findings. Keep one primary finding per root cause.
6. Produce findings first, ordered by severity.

## Severity

Use these severities exactly:

- `Critical`: exploitable security issue, auth bypass, data corruption or loss
  risk, or outage-level reliability risk. Must be fixed before merge.
- `Major`: high-impact correctness, performance, reliability, data integrity,
  or security issue likely to cause production problems. Should be fixed before
  merge unless explicitly deferred with owner and rationale.
- `Minor`: meaningful lower-impact issue such as maintainability risk,
  moderate performance concern, incomplete edge handling, unclear behavior, or
  incomplete test coverage. Fix in this PR when practical, otherwise track a
  follow-up.
- `Nit`: low-impact style, readability, naming, formatting, or consistency
  suggestion with no material behavior or risk impact. Optional and non-blocking.

## Baseline Checks

Assess changed code for:

- correctness and behavioral regression risk
- security impact and access-control boundary changes
- performance impact on hot paths
- reliability and failure-mode handling
- timeout, retry, idempotency, and fallback behavior
- validation and input handling
- error handling
- data integrity
- concurrency risks
- test coverage quality
- whether tests meaningfully cover changed behavior
- whether the change is scoped cleanly
- whether the implementation matches the apparent intent

## Frontend Checks

When frontend files are changed, also review:

- React rendering correctness
- state management issues
- incorrect hook dependencies
- unnecessary re-renders
- hydration risks
- accessibility issues
- form validation behavior
- loading, error, and empty states
- API client usage
- user-facing copy or UX regressions
- routing and navigation behavior
- type safety
- test coverage for changed behavior

## Backend Checks

When backend files are changed, also review:

- API contract compatibility
- DTO validation
- auth and authorization boundaries
- resource ownership checks
- database query correctness
- transaction and consistency risks
- race conditions
- idempotency
- logging and observability impact
- sensitive data leakage
- error response safety
- pagination and bounded query behavior
- dependency injection or module wiring issues
- test coverage for success and failure paths

## Test Checks

When test files are changed, review:

- whether tests assert meaningful behavior
- false positives
- overly brittle assertions
- excessive mocking
- missing negative, auth, error, or edge cases
- whether tests match production behavior
- whether important behavior is only snapshot-tested
- whether the test would fail if the implementation regressed

## Evidence Rules

Every finding must include concrete evidence from the diff or nearby code.
Evidence may include file path, function name, route name, component name,
changed behavior, code pattern, test gap, data flow, or failing scenario.

Do not invent issues without evidence. Avoid vague feedback such as "this might
be bad." Prefer specific feedback:

```text
`createPost` validates that the profile exists, but it does not verify that the authenticated user owns that profile before attaching the post. This could allow one user to create posts under another profile ID.
```

Prefer fewer, higher-quality findings over noisy comments.

## GitHub Posting

Posting applies only in PR mode for open or draft GitHub PRs.

Without `--inline`, print the full review to the current window first. If there
are findings, ask:

```text
Would you like me to post these review comments to GitHub?
```

Post only after the user explicitly agrees. If there are no material findings,
do not ask to post comments unless the user explicitly requested a GitHub
summary comment.

With `--inline`, post review findings to GitHub after completing the review. If
there are no material findings, do not post a comment unless the user explicitly
requested a GitHub summary comment.

When posting:

- Prefer inline diff comments when a finding maps to a changed file and changed
  line in the PR diff.
- Use a top-level PR review or PR comment for findings that cannot be anchored
  inline.
- Never approve the PR, request changes, merge the PR, resolve threads, or edit
  code as part of posting.
- Before posting, de-duplicate findings and skip low-value noise.
- Keep each comment concise and include the issue, evidence, risk, and suggested
  remediation.
- If auth, permissions, API limits, or diff-line mapping fail, print the
  findings locally and explain what prevented posting.

## Output Format

Use this structure for local mode:

```md
## Local Branch Review

- Current branch:
- Base branch:
- Diff reviewed:
- Uncommitted changes included:
- Review scope:
- Review guidance:

## Findings

### Critical

#### 1. <short finding title>

- File:
- Severity: `Critical`
- Issue:
- Evidence:
- Risk:
- Impact:
- Suggested remediation:

### Major

#### 1. <short finding title>

- File:
- Severity: `Major`
- Issue:
- Evidence:
- Risk:
- Impact:
- Suggested remediation:

### Minor

#### 1. <short finding title>

- File:
- Severity: `Minor`
- Issue:
- Evidence:
- Risk:
- Impact:
- Suggested remediation:

### Nit

#### 1. <short finding title>

- File:
- Severity: `Nit`
- Issue:
- Evidence:
- Risk:
- Impact:
- Suggested remediation:

## Residual Risks and Testing Gaps

- ...

## Review Summary

- Blocking issues:
- Should-fix issues:
- Optional improvements:
- Overall recommendation:
```

Use this structure for PR mode:

```md
## Pull Request Review

- PR:
- Author:
- State:
- Draft:
- Base branch:
- Head branch:
- Diff reviewed:
- Review scope:
- Review guidance:
- Posting mode: `local only` / `ask before posting` / `inline`

## Findings

### Critical

#### 1. <short finding title>

- File:
- Line:
- Severity: `Critical`
- Issue:
- Evidence:
- Risk:
- Impact:
- Suggested remediation:
- GitHub posting: `inline` / `top-level` / `not posted`

Repeat the same severity section pattern for `Major`, `Minor`, and `Nit`
findings when present.

## Residual Risks and Testing Gaps

- ...

## Review Summary

- Blocking issues:
- Should-fix issues:
- Optional improvements:
- Overall recommendation:
```

Omit severity sections with no findings.

If there are no material findings, use:

```md
## Findings

No material issues found in the reviewed diff.

## Residual Risks and Testing Gaps

- ...
```

## Recommendation

End with exactly one overall recommendation:

- `Block merge`: use when any Critical issue exists.
- `Fix major issues before merge`: use when one or more Major issues exist.
- `Safe to proceed after minor follow-ups`: use when only Minor or Nit findings
  exist.
- `No material issues found`: use when no material issues are found.

## Exit Conditions

Exit without review when:

- local mode or current-branch PR discovery needs a git repository and the
  current directory is not a git repository
- local mode is running on `main` or `master`
- PR discovery is requested from `main` or `master`
- no base branch can be detected in local mode and the user did not specify one
- there are no local branch changes to review in local mode
- a provided URL is not a GitHub PR URL
- a current-branch PR cannot be found when `--pr` or `--inline` is used without
  a URL
- the resolved PR is closed or merged
- GitHub auth, permissions, or repository access prevents PR review
- requested files or paths do not exist
- the diff is too large for a focused review

When exiting, explain the reason clearly and provide the next best action.

## Final Rule

Review only. Do not make code changes, apply patches, commit, approve, request
changes, merge, resolve threads, or rewrite code during `code-review`. Inline mode
may post review comments to GitHub, but it must not perform any other PR action.
If the user later asks to fix findings, treat that as a separate implementation
task and follow the repository's normal workflow.

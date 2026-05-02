---
name: do-review
description: Perform a focused local PR-style review of the current feature branch diff. Use when the user says "do-review" or asks to review local branch changes before opening or updating a PR, optionally against a specified base branch, path, or focus area such as security, backend, frontend, tests, performance, or architecture.
---

# Do Review

## Goal

Review local code changes on a feature branch before a pull request is opened or
updated. Keep the review focused on the branch diff and changed code. This skill
is review-only by default: do not edit files, apply fixes, commit changes, or
rewrite code unless the user explicitly asks for implementation after the review.

The review should answer:

- Is this branch safe to merge?
- What must be fixed first?
- What should be improved if practical?
- What risks or test gaps remain?
- Which changed code deserves the most attention?

## Trigger

Run this workflow when the user says:

```text
do-review
```

The user may specify a target branch, base branch, file or directory, whether to
include uncommitted changes, or a focus area. Examples:

```text
do-review
do-review against main
do-review this branch against develop
do-review only apps/backend
do-review focus on security and auth
do-review include unstaged changes
```

## Scope

Review local branch changes only. Do not scan the whole codebase except as
needed to understand changed files.

Default scope:

- current branch diff compared with the detected base branch
- staged and unstaged changes when present, unless the user asked for committed
  changes only
- surrounding code only when needed to understand the changed code

Do not produce broad architecture feedback unrelated to the branch diff. Do not
review unrelated legacy code unless the branch touches it directly.

## Branch Safety

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

## Base Branch

Use the base branch specified by the user when provided.

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

## Large Diff Handling

This skill is not for large whole-codebase reviews. If the diff is too large for
a high-quality single-pass review, do not perform a shallow review.

Treat the diff as too large when it is clearly broad enough to reduce review
quality, such as dozens of unrelated files, multiple domains plus generated
code, or thousands of changed lines.

When exiting for size:

1. Summarize the size and shape of the diff.
2. Warn that it is too large for a reliable single-pass local review.
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
   - `git status --short`
   - current branch
   - detected or specified base branch
   - merge base
   - changed files and stats
   - branch diff
   - staged and unstaged diff when included
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

## Output Format

Use this structure:

```md
## Local Branch Review

- Current branch:
- Base branch:
- Diff reviewed:
- Uncommitted changes included:
- Review scope:

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

- the current directory is not a git repository
- the current branch is `main` or `master`
- no base branch can be detected and the user did not specify one
- there are no branch changes to review
- requested files or paths do not exist
- the diff is too large for a focused review

When exiting, explain the reason clearly and provide the next best action.

## Final Rule

Review only. Do not make code changes, apply patches, commit, or rewrite code
during `do-review`. If the user later asks to fix findings, treat that as a
separate implementation task and follow the repository's normal workflow.

---
name: review-pr-feedback
description: Review, triage, plan, and optionally address pull request review feedback. Use when the user says "Review PR feedback", asks to handle PR review comments, requested changes, unresolved GitHub PR conversations, inline review comments, or reviewer questions for a PR, branch, repository, or subset of comments.
---

# Review PR Feedback

## Goal

Review pull request feedback safely before changing code. GitHub is the default
provider. The first pass is always triage only: gather feedback, group related
comments, classify severity, propose an action plan, and wait for explicit user
approval before implementation.

Never resolve PR comments, review threads, or conversations. Resolution belongs
to the human reviewer or PR author.

## Trigger

Run this workflow when the user says:

```text
Review PR feedback
```

The user may specify a PR number, PR URL, branch, repository, or subset of
comments. Examples:

```text
Review PR feedback
Review PR feedback for PR #42
Review PR feedback on this branch
Review PR feedback for https://github.com/org/repo/pull/42
Review PR feedback for the auth comments only
```

## PR Discovery

1. Use GitHub by default.
2. If the user provides a PR URL, PR number, repository, branch, or comment
   subset, use that scope.
3. If no PR is specified:
   - Detect the current repository.
   - Detect the current branch with `git branch --show-current`.
   - If the branch is `main` or `master`, exit immediately:

     ```text
     No PR was specified and the current branch is `main`, so there is no safe branch-specific PR to review.
     ```

   - Find the open GitHub PR associated with the current branch.
   - If no PR is found, exit:

     ```text
     No PR was specified and I could not find an open GitHub PR for the current branch.
     ```

4. If the repository cannot be detected, GitHub access is unavailable, or the
   specified PR cannot be found, exit with a short reason.

## Feedback Collection

Fetch review feedback before triage:

- inline code review comments
- general PR review comments
- unresolved review threads or conversations where available
- requested changes and review summaries

Use thread-aware GitHub data when possible. If a tool only returns flat
comments, state that limitation in the triage notes.

Exit if the PR has no review comments, unresolved feedback, requested changes,
or actionable reviewer questions:

```text
I found the PR, but there are no review comments or unresolved feedback to handle.
```

## First Pass: Triage Only

The first pass must not make code changes or post PR replies.

During triage:

- Merge duplicates, near-duplicates, and overlapping comments into one item.
- Include all relevant links for grouped comments.
- Ignore praise-only or positive-only comments that require no action.
- Include question-only comments, even when no code change is required.
- Assign one severity to each unique item.
- Propose an action and expected verification.
- Ask the user to approve the action plan before implementation.

## Severity

Use exactly one severity per triaged item:

- `critical`: exploitable security issue, auth bypass, data corruption or loss
  risk, or outage-level reliability risk. Blocks merge until fixed.
- `major`: serious functional defect, data integrity risk, or scoped security
  issue. Normally fixed before merge.
- `minor`: non-blocking bug, UX issue, or performance degradation. May be fixed
  before merge or deferred with reasoning.
- `nit`: style, naming, formatting, or small cleanup with low technical risk.
  May be fixed or declined with a short explanation.
- `question`: asks for clarification or a response and does not clearly require
  code. Answer it or convert it into an action item if it reveals a real issue.

## Triage Output

For each unique item, use this structure:

```md
## PR Feedback Triage

### Item <number>

- comment:
  - <short summary of the issue or question>

- link/links:
  - <direct PR comment link>

- severity:
  - `critical` / `major` / `minor` / `nit` / `question`

- action:
  - <concise proposed plan, or `No action`>

- implementation notes:
  - <notes for the fix or response>

- expected verification:
  - <checks, tests, commands, or manual verification expected>
```

For grouped duplicate comments, include every relevant link under
`link/links`.

## Proposed Action Plan

After triage, build a plan ordered by severity:

1. `critical`
2. `major`
3. `minor`
4. `nit`
5. `question` and no-action items

Each plan entry must include:

- the item being addressed
- proposed action
- severity
- whether it requires a code change or only a reply
- implementation notes
- expected verification

Use this shape:

```md
## Proposed Action Plan

1. <action summary>
   - Severity: `critical` / `major` / `minor` / `nit` / `question`
   - Requires code change: Yes/No
   - Implementation notes:
     - <note>
   - Expected verification:
     - <check>

## Awaiting Approval

No code changes have been made yet.

Approve the plan before implementation begins.
```

## Approval Gate

Do not implement code changes until the user explicitly approves the triage
action plan.

Supported approval phrases include:

- `Approve plan`
- `Approved`
- `Go ahead`
- `Looks good`
- `Continue`

If the user asks to revise the plan, update the plan and wait for approval
again.

## Implementation After Approval

After approval, implement only the approved changes.

Follow the repository's normal development workflow, coding standards,
verification requirements, and commit conventions. Work in clear, reviewable
steps, report progress after each meaningful step, and preserve the mapping
between:

- triaged item
- code change
- verification result
- commit, if one is made
- optional PR comment reply

If feedback conflicts, is ambiguous, or would cause a behavioral regression,
pause and ask the user before changing code.

## Verification

Run appropriate verification based on what changed. This may include lint,
typecheck, unit tests, integration tests, E2E tests, build, startup smoke, or
manual regression checks.

Report verification with this structure:

```md
## Verification

- Lint:
- Typecheck:
- Tests:
- Build:
- Additional checks:
```

Use only these statuses:

- `pass`
- `fail -> fixed -> pass`
- `blocked`
- `skipped`

Never claim a command passed unless it actually ran successfully.

## Optional PR Comment Replies

After implementation and verification are complete, ask:

```text
Do you want me to reply to the addressed PR comments with a concise summary of what changed?
```

Do not reply unless the user approves.

If approved, replies must:

- map clearly to the addressed triaged item or grouped comments
- be concise and factual
- mention the code change at a high level when code changed
- mention relevant verification briefly when verification ran
- give concise reasoning for no-action items only when appropriate
- never imply resolution unless the change was made and verified
- never resolve a PR comment, review thread, or conversation

Example implementation reply:

```text
Addressed this by adding the missing auth guard to the route and covering the unauthorized case in the backend tests. Verification passed with `pnpm --filter @app/api run test`.
```

Example no-action reply:

```text
Thanks. I left this unchanged because the current DTO name matches the existing API naming convention. Happy to revisit if the convention should change more broadly.
```

## Exit Conditions

Exit without implementation when:

- no PR is specified and the current branch is `main` or `master`
- no PR can be found for the current branch
- the specified PR cannot be found
- the PR has no review comments, unresolved comments, requested changes, or
  actionable feedback
- GitHub access is unavailable
- the repository cannot be detected

When exiting, provide a short reason.

## Final Output After Implementation

Use this structure:

```md
## PR Feedback Implementation Summary

- Items addressed:
- Items not addressed:
- Commits made:
- Verification:
- Remaining risks / follow-ups:

## Optional Comment Replies

Do you want me to reply to the addressed PR comments with a concise summary of what changed?
```

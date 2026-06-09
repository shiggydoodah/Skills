---
name: audit
description: Reviews a specific area of the codebase for security, performance & scalability, code quality, or test coverage. Invoke with a path, e.g. /audit apps/backend/auth.
argument-hint: "[path-to-area]"
disable-model-invocation: true
allowed-tools: Read Grep Glob Bash(git diff *) Bash(git log *) Bash(ls *) AskUserQuestion
---

# audit

Interactive, **read-only** review of one area of this monorepo (NestJS backend,
React frontend, Node throughout). It analyses and reports findings; it never edits
code on its own. Only at the very end may it _offer_ to fix things — and only if
you say yes.

The target area is `$ARGUMENTS` (a path, e.g. `apps/backend/auth`).

## Core constraint

Read-only. Do not edit, create, fix, or commit anything while reviewing. Use only
`Read`, `Grep`, `Glob`, and the read-only `Bash` commands. Writing the report or
fixing code happens only after step 7, and only on explicit confirmation.

## Flow

### 1. Resolve the target

Confirm `$ARGUMENTS` is a real path in the repo. If it is empty, ask which area to
review before doing anything else. If it is set but does not exist, say so and ask
for a valid path. Do not proceed without a valid target.

### 2. Pick the review type(s)

Use the `AskUserQuestion` tool to present a **multi-select** menu so I can choose
one or several:

- Review security
- Review performance & scalability
- Review code quality
- Review test coverage

### 3. Pre-flight (always, before any checklist)

Learn this repo's conventions so you grade against _our_ standards, not generic
ones. Keep it brief — orientation, not a deliverable:

- Read the nearest `tsconfig`, ESLint/Prettier config, and any Nest config near
  the target.
- Glob a couple of sibling modules in the same app to learn the established
  structure, naming, and patterns.
- Note the test setup: Jest or Vitest, where tests live, any coverage config.

### 4. Run the selected checklist(s)

For each chosen review type, read the matching file in `references/` and work
through **every** item against the target area. Read the actual source — and its
tests, for coverage. Cite every finding with `file:line`.

- Security → [references/security.md](references/security.md)
- Performance & scalability → [references/performance.md](references/performance.md)
- Code quality → [references/quality.md](references/quality.md)
- Test coverage → [references/coverage.md](references/coverage.md)

### 5. Triage every finding

Assign each finding exactly one severity from the rubric below.

### 6. Report

Produce the report in the output format below. If there are **zero** findings,
skip the report entirely and instead reply with one short, genuinely funny
one-liner congratulating me on clean code.

### 7. Offer next step

Only if there were findings, end by offering to:

- (a) write the report to a markdown file, and/or
- (b) fix the criticals.

Do nothing further unless I confirm. (Edits are not pre-approved, so each write
will ask for permission — that is intended.)

## Severity rubric

- **Critical** — exploitable security hole, auth bypass, data loss/corruption, or
  a crash under normal use. Block merge.
- **Major** — real bug risk, a notable performance/scalability problem, or a
  missing test on a critical path. Fix soon.
- **Minor** — smaller correctness issues or moderate tech debt. Fix when
  convenient.
- **Nit** — style, preference, or tiny polish. Optional.

## Output format

Start with a one-line summary of counts:

```text
2 critical · 1 major · 3 minor · 4 nit
```

Then list findings grouped by severity, highest first. Each finding:

- **What** — the issue, with `file:line`
- **Why it matters** — the concrete risk or cost
- **Fix** — a specific, actionable suggestion

Keep it scannable. Don't pad.

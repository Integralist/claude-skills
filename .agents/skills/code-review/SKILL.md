---
name: code-review
description: >-
  Code review using specialized subagents. Analyzes consistency,
  idiomatic Go, data correctness, and security. Works on PRs
  or local code.
---

# Code Review Skill

Review code using up to four specialized subagents working in
parallel. Each subagent focuses on a different review dimension.
Works against GitHub PRs or local code changes.

**Note:** Some platforms limit concurrent subagents (e.g.,
Swival caps at 4). The four dimensions below are chosen to fit
that constraint. If your platform supports more, consider
splitting "Consistency" into separate naming and architecture
reviews.

## Input

The argument follows the skill invocation. Detect the mode:

| Argument                      | Mode                                  |
| ----------------------------- | ------------------------------------- |
| PR URL or `owner/repo#number` | **PR mode**                           |
| `--diff` or no argument       | **Local: branch diff** vs main/master |
| `--uncommitted`               | **Local: uncommitted changes**        |
| File path or glob pattern     | **Local: explicit paths**             |

## PR Mode: Fetch Context

Use the GitHub CLI (`gh`) or equivalent to fetch PR metadata and
the full diff:

1. `gh pr view <number> --repo <owner>/<repo> --json title,body,baseRefName,headRefName,additions,deletions`
2. `gh pr diff <number> --repo <owner>/<repo> --name-only`
3. `gh pr diff <number> --repo <owner>/<repo>`

## Local Mode: Gather Context

### Detect the default branch

Run these in order until one succeeds:

1. `git rev-parse --verify main` — use `main`
2. `git rev-parse --verify master` — use `master`
3. `git symbolic-ref refs/remotes/origin/HEAD` — parse the
   branch name from the output

Store the result as `DEFAULT_BRANCH`.

### Branch diff (default / `--diff`)

```bash
BASE=$(git merge-base HEAD "$DEFAULT_BRANCH")
git diff "$BASE"...HEAD
git diff --name-only "$BASE"...HEAD
```

### Uncommitted (`--uncommitted`)

```bash
git diff HEAD
git diff --name-only HEAD
git status --porcelain | sed -n 's/^?? //p'
```

For any untracked files listed above, read each one and include
its contents as additional context alongside the diff.

### Explicit paths

For each provided path or glob pattern: expand globs, read file
contents, and if tracked, get the diff:
`git diff HEAD -- <paths>`

### No changes

If the diff is empty and no files are found, report
"No changes to review" and stop.

### Large diffs

If the diff exceeds ~3000 lines, pass only the file list to
subagents and instruct them to read files individually rather
than embedding the entire diff in the prompt.

## Spawn Subagents

Spawn one subagent per review dimension. The roles below are
descriptions, not agent names — use your platform's actual
agent/subagent primitives.

Each subagent prompt must include:

- The review dimension and focus area
- The list of changed files
- The diff (or instruction to read files if diff is too large)
- **Do NOT add comments to any PR. Report findings back when
  complete.**

### Review Dimensions

1. **Consistency Review** (general-purpose role) — naming
   patterns, code style consistency, error handling patterns,
   metric/label/logging consistency, structural consistency
   with existing codebase

2. **Data Correctness Review** (general-purpose role) —
   correctness of computations and state, race conditions in
   concurrent access, correct context/value propagation,
   resource lifecycle (leaks, double-close), error path
   completeness

3. **Security Review** (general-purpose role) —
   injection/cardinality attacks on labels or inputs,
   information leakage, unbounded reads or allocations,
   resource exhaustion, dependency security, timing side
   channels, authentication/authorization gaps

4. **Idiomatic Go Review** (general-purpose role) — idiomatic
   Go as detailed in https://go.dev/doc/effective_go. This
   subagent MUST read https://go.dev/doc/effective_go before
   beginning the review.

Collect all results before compiling the summary.

## Compile Summary

### PR mode output

Present a consolidated review in the conversation:

```markdown
## PR #<number> Review Summary: "<title>"

**Overall assessment:** [1-2 sentence summary of PR quality]

### Actionable Items

Items worth discussing or changing, ordered by severity
(High/Medium/Low). Each item should include:
- The file and approximate line reference
- A code snippet if relevant
- Why it matters
- Suggestion for improvement

### Informational / No Action Needed

Brief bullet points for things that are fine or only worth
noting for awareness.
```

### Local mode output

Use the same format as PR mode output above, with these
additional metadata fields at the top:

```markdown
## Code Review: <branch-name or "uncommitted" or path>

**Date:** YYYY-MM-DD HH:MM
**Mode:** branch-diff | uncommitted | paths
**Branch:** <branch-name>
**Base:** <merge-base-ref> (if applicable)
**Files reviewed:** <count>
```

After presenting the review, ask the user what they want to
do next:

```txt
What would you like to do with this review?

1. Save to docs/plans/code-review-<date>.md
2. Address the actionable items now
3. Nothing — just wanted the review
4. Something else?
```

Deduplicate findings across subagents — if multiple subagents
flag the same issue, combine them into a single item citing all
relevant perspectives.

## Notes

- The review is language-aware but optimized for Go codebases.
- For non-Go PRs, the idiomatic Go subagent should be replaced
  with language-appropriate idiom checking, or omitted.
- All subagents should read full file context (not just the
  diff) when needed to understand surrounding code patterns.
- The skill does not post any comments to the PR — all output
  stays in the conversation or in the local review file.

## References

- https://go.dev/doc/effective_go — Full Effective Go document
  used by the Idiomatic Go Review subagent

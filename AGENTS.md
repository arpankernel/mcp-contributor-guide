# AGENTS.md: instructions for an agent contributing to MCP

This file tells a coding agent how to contribute to the `modelcontextprotocol`
org effectively. A human can read it too. If your harness uses `CLAUDE.md`, copy
or symlink this file to that name.

## Prerequisites

- `gh` authenticated (`gh auth status`), `jq`, and the toolchain for the target
  repo (Go, Python with `uv`, or Node with `corepack`).
- A fork of the target repo, cloned with `origin` = your fork and `upstream` =
  `modelcontextprotocol/<repo>`.

## The loop

### 1. Pick a target repo you can build and test

Only take on a repo whose tests you can run locally. You cannot defend a change
you have not run. See the [per-repo playbook](./guide/02-repo-playbook.md) for
build/test commands and which repos accept PRs.

### 2. Find work that is genuinely open

Do **not** trust a bulk scan that detects claims by matching `#N` in PR
titles/bodies. It undercounts badly. For each candidate issue, verify freshness
with the per-issue timeline:

```bash
gh api repos/modelcontextprotocol/<repo>/issues/<N>/timeline --paginate \
  | jq -r '.[] | select(.event=="cross-referenced") | .source.issue.pull_request.html_url // empty'
```

An issue is only fresh if that returns nothing, it has no assignee, and no
recent comment calls dibs (watch for "I'll take", "working on this", "would like
to work/help/investigate", "I prepared a branch"). Note: `ready for work` issues
are usually the most contested. Details in
[Finding work](./guide/01-finding-work.md).

### 3. Verify the premise before you build

Read the issue, then confirm the bug against the **actual code** and the
**spec**. Ask: "what real client/flow actually hits this?" If the answer is
"none," the maintainer will likely close it. Do not take a spec-cited report at
face value. See [Finding and verifying bugs](./guide/04-finding-and-verifying-bugs.md).

### 4. Reproduce it

Write a runnable test (or small program) that demonstrates the wrong behavior.
Red-without-the-fix, green-with-it. If you cannot reproduce it, do not claim it.

### 5. Make the smallest correct change, with a test

Match the surrounding code's style exactly. Add a regression test next to the
fix. Run the full package test suite plus the race detector (Go) or equivalent.

### 6. Open the PR (or issue, for inspector)

Follow the repo's branch and PR conventions. Mind the fork-behind-upstream
`workflow`-scope push rejection. See [Opening a PR](./guide/03-opening-a-pr.md).

## Authorship and voice (strict)

- **No AI/tool attribution anywhere.** Not in commits, commit trailers, PR
  titles/descriptions, issue comments, or review replies. No `Co-Authored-By`
  trailers, no "generated with" footers. Remove any template default that adds
  one.
- **Write like a terse human.** Conventional, one-line commit subjects
  (`fix: ...`, `mcp: ...` where the repo uses a prefix). PR descriptions of one
  to three lines plus `Closes #<issue>`. Brief, casual issue comments. Match the
  repo's existing tone by reading a few recently merged PRs first.
- **Only submit what you can defend in review.** Every change is one you have
  read, understood, reproduced, and tested.
- **Be low-noise.** Do not file a stream of tiny nits or duplicate issues. A
  maintainer's trust is the asset that gets your next PR merged; protect it.

## Hard-won rules

- Verify claims via the timeline API, not text search.
- inspector accepts **issues, not PRs** from external contributors.
- Confirm a bug's premise against code and spec before writing a fix.
- Reproduce before claiming; test before submitting.
- Small, focused, defensible changes only.

---
name: mcp-contribute
description: Find, verify, reproduce, and land a contribution to a modelcontextprotocol org repo (go-sdk, python-sdk, typescript-sdk, servers, inspector, registry, and the rest). Use when asked to find an MCP issue to work on, evaluate whether an MCP issue is worth taking, reproduce an MCP SDK bug, or open a PR/issue against a modelcontextprotocol repo. Encodes the reliable freshness check, premise-scrutiny, and per-repo contribution rules so you avoid racing claimed issues or shipping fixes that get closed as "working as intended".
---

# Contributing to a modelcontextprotocol repo

This skill runs the full contribution loop for the `modelcontextprotocol` GitHub
org. It assumes `gh` (authenticated) and `jq` are available. Read the guide
chapters in this repo for depth: `guide/01`..`guide/04`. Follow the steps in
order; do not skip verification.

## Step 0 — Know the contribution model before writing code

Different repos accept different things (see `guide/02-repo-playbook.md`):

- **inspector**: does NOT accept external PRs. File a detailed issue instead;
  maintainers implement it. Stop here if the task was "open a PR to inspector".
- **modelcontextprotocol** (the spec repo): docs/schema fixes are normal PRs;
  protocol changes go through the SEP process, not a drive-by PR.
- **SDKs / servers / registry / mcpb / conformance**: normal fork-branch-PR.
- **ext-\* / experimental-ext-\* / working groups / infra**: usually
  proposal/discussion or maintainer-only; check the repo first.

Only take on a repo whose tests you can actually run locally. Build/test
commands per repo are in `guide/02-repo-playbook.md`.

## Step 1 — Find work that is genuinely open

Do NOT trust a bulk scan that greps PR text for `#N`; it undercounts. For each
candidate issue N, run the reliable per-issue timeline check:

```bash
gh api repos/modelcontextprotocol/<repo>/issues/<N>/timeline --paginate \
  | jq -r '.[] | select(.event=="cross-referenced")
           | .source.issue.pull_request.html_url // empty' \
  | grep "modelcontextprotocol/<repo>" | sort -u
```

An issue is fresh only if: that returns nothing, it has no assignee, no recent
comment calls dibs (scan for "working on this", "I'll take", "taking this",
"would like to work/help/investigate", "PR incoming", "I prepared a branch"),
and it is not labeled in-progress. A comment claim counts even without a PR.

Caution: a `ready for work` / `good first issue` / `help wanted` label is a
magnet, not an opening. Those are usually the MOST contested. Check harder, do
not relax.

If nothing is open, either (a) strike-first monitor for newly-filed bugs, or
(b) find a bug yourself by reading and running the code (uncontested by
definition). A self-discovery loop:

```bash
# one monitor tick: report issues newer than a saved baseline that pass the
# freshness check and are small, concrete bugs; then update the baseline.
baseline=$(cat .mcp-baseline 2>/dev/null || echo 0)
gh issue list --repo modelcontextprotocol/<repo> --state open --limit 40 \
  --json number,title,createdAt,labels \
  | jq --argjson b "$baseline" -r '[.[]|select(.number>$b)][] | "#\(.number) \(.title)"'
gh api "repos/modelcontextprotocol/<repo>/issues?state=all&per_page=1&sort=created&direction=desc" \
  --jq '.[0].number' > .mcp-baseline
```

## Step 2 — Verify the premise (do this before any fix)

Read the issue, then confirm the bug against the ACTUAL code and the spec. Ask:
"what real client, server, or flow actually hits this path?" If the honest
answer is "none", the maintainer will likely close it. Watch for:

- version-keyed behavior where a "fix" changes nothing for any real client;
- legacy-only or broken-environment-only bugs that the current line does not hit.

Do not take a spec-cited report at face value just because it quotes a MUST.

## Step 3 — Reproduce it

Write a runnable test (or small program) that fails on current code and passes
with the fix. For hangs/deadlines use a black-hole peer and measure elapsed
time; for concurrency use the race detector; for end-to-end use the SDK's
in-memory transports. If you cannot make it fail without the fix, you have not
proven the bug. See `guide/04-finding-and-verifying-bugs.md`.

## Step 4 — Make the smallest correct change, with a test

Match the surrounding code exactly. Add a regression test next to the fix. Run
the full package suite plus the race detector / linters the repo uses.

## Step 5 — Open the PR (or issue, for inspector)

Conventions (see `guide/03-opening-a-pr.md`):

- Terse, conventional commit subject (match the repo's prefix, e.g. `mcp: ...`).
- PR body: 1-3 lines + `Closes #<N>`. No scaffolding.
- NO AI/tool attribution anywhere. No `Co-Authored-By` trailers, no
  "generated with" footers. Strip any tool default.

If a push to your fork is rejected with `refusing to allow an OAuth App to
create or update workflow ... without 'workflow' scope`, your fork is behind
upstream and your branch carries upstream workflow-file commits. Fix without
re-authing by cherry-picking onto the fork's own main so the push delta is only
your files:

```bash
git fetch origin
git checkout -b pr-clean origin/main
git cherry-pick <your-commit-sha>
git diff --name-only origin/main...HEAD   # confirm: only your files
git push origin pr-clean:<branch-name>
```

## Step 6 — After opening

Keep it green and current, then wait. One polite check-in after genuine silence
is fine; do not re-nudge repeatedly. A maintainer's trust is what gets your next
PR merged fast, so stay low-noise and never ship a change you have not
reproduced and tested.

# 1. Finding work that is actually open

The single biggest time-sink for a new MCP contributor is investing in an issue
that is already being worked. This org is one of the most actively contributed
on GitHub. Assume every attractive issue is contested until you prove otherwise.

## Why the obvious "is it claimed?" check is wrong

The tempting approach is to pull all open PRs, scan their titles and bodies for
`#N`, and treat any issue not mentioned as free. **This badly undercounts.**
Contributors link a PR to its issue through GitHub's "Development" sidebar or a
`Closes #N` in a non-default-branch PR, neither of which reliably shows up as
`#N` text you can grep. Issues that look fresh by text search routinely turn out
to have several linked PRs.

Observed repeatedly: issues that a bulk text scan reported as "fresh" had one to
seven linked PRs once checked properly. Do not trust the fast scan.

## The reliable freshness check (per issue)

Use the issue timeline API, which surfaces real PR cross-references:

```bash
gh api repos/modelcontextprotocol/<repo>/issues/<N>/timeline --paginate \
  | jq -r '.[] | select(.event=="cross-referenced")
           | .source.issue.pull_request.html_url // empty' \
  | grep "modelcontextprotocol/<repo>" | sort -u
```

An issue is genuinely open only if ALL of these hold:

1. The command above returns nothing (no linked PR).
2. It has no assignee.
3. No recent comment calls dibs. Scan comment bodies for phrases like
   "working on this", "I'll take", "taking this", "would like to work",
   "would like to help", "would like to investigate", "PR incoming",
   "I prepared a branch". People claim work in comments without opening a PR,
   and without using the exact words you expect.
4. It is not labeled in-progress (`in progress`, `wip`, etc.).

A claim in a comment counts even without a PR. Someone who posted "I prepared a
signed branch for this issue" a day ago has effectively taken it, even though no
PR exists and no standard claim phrase appears.

## The `ready for work` trap

It is natural to filter for a `ready for work` (or `good first issue`,
`help wanted`) label and start there. In this org that label is a **magnet, not
an opening**. Because it advertises "maintainers welcome a PR here," those
issues attract the most competition. In practice, nearly every `ready for work`
issue already has multiple linked PRs. Treat a prominent label as a reason to
check harder, not as a green light.

## Even brand-new issues get swarmed fast

Filing-to-fix latency is often hours, not days. A bug filed in the morning can
have three people mapping the fix in the comments by afternoon, and a complete
fix branch posted the same day. A high-severity bug rarely stays unclaimed long
enough for a leisurely approach.

## Two approaches that actually work

### A. Strike first

Monitor a target repo for newly-filed issues and be ready to open a small,
tested PR within the hour, before the swarm forms. A simple cron/loop that lists
issues newer than a saved baseline, timeline-checks each, filters to small
concrete bugs in your area, and alerts you, is enough. Speed is the whole game
here; if you are not going to act within a few hours, someone else will.

### B. Find the bug yourself

A bug you discover by reading and running the code is uncontested by definition.
This is the most reliable path to unclaimed, mergeable work. Read a spec section,
write a test that exercises it, and look for a deviation. See
[Finding and verifying bugs](./04-finding-and-verifying-bugs.md) for a taxonomy
and method. Even self-found bugs are sometimes already reported (check first
with the timeline method above), but far less often than labeled issues.

## Before you invest, always

- Confirm the bug's premise against the actual code and the spec
  (see [chapter 4](./04-finding-and-verifying-bugs.md)). A surprising number of
  plausible, spec-cited reports do not survive this step and get closed as
  "working as intended."
- Confirm you can build and test the repo
  (see [the per-repo playbook](./02-repo-playbook.md)).
- Confirm the repo even accepts PRs. One of them does not (inspector).

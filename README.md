# Contributing to the Model Context Protocol (a practical field guide)

A no-nonsense guide to actually landing a contribution in the
[`modelcontextprotocol`](https://github.com/modelcontextprotocol) org: the SDKs
(`go-sdk`, `python-sdk`, `typescript-sdk`), the reference `servers`, the
`inspector`, and friends.

It exists because the gap between "I want to help" and "my PR merged" in this
org is wider than it looks. The repos are among the most actively contributed on
GitHub, each has its own contribution rules (one of them does not accept PRs at
all), and a lot of time gets wasted on issues that are already being worked, or
on fixes whose premise does not survive contact with the maintainer.

This guide is written for **both humans and coding agents**. If you are an LLM
or an agent, start with [`llms.txt`](./llms.txt) and [`AGENTS.md`](./AGENTS.md).

## Read this in order

1. [Finding work that is actually open](./guide/01-finding-work.md): the
   org is saturated; here is how to find genuinely-fresh, unclaimed work instead
   of racing five other PRs.
2. [Per-repo playbook](./guide/02-repo-playbook.md): covers **every
   non-archived repo in the org**, grouped into five contribution models (takes
   PRs / issues-only / the spec's SEP process / extension repos / working-groups
   and infra), with build and test commands for the ones that take code.
3. [Opening a PR without tripping](./guide/03-opening-a-pr.md): commit/PR
   conventions, the fork-behind-upstream `workflow`-scope gotcha, and the
   cherry-pick workaround.
4. [Finding and verifying bugs](./guide/04-finding-and-verifying-bugs.md):
   a bug-class taxonomy, how to reproduce by class, and the premise-scrutiny
   step that saves you from shipping a fix that gets closed as "working as
   intended."

## The one-paragraph version

Do not fight over existing issues. Most clean, small, well-labeled bugs already
have one to seven open PRs racing them, and the `ready for work` label is a
magnet, not an opening. The reliable paths are: (a) **strike first** on a
freshly-filed bug before the swarm arrives, or (b) **find a bug yourself** by
reading and running the code. Before you invest, **verify the premise against
the actual code and the spec** and **verify the issue is genuinely unclaimed**
using the per-issue timeline API (not a text search). Keep every change small,
reproduced, and tested. Be a low-noise, high-signal contributor: a maintainer's
impression of you is what gets your next PR merged quickly.

## Status and accuracy

Observations in this guide were gathered in **September 2026**. Contribution
policies, labels, and file paths change. Treat specifics (line numbers, label
names, exact policies) as starting points and re-verify against the live repo
before relying on them. The *methods* (how to find fresh work, how to verify,
how to reproduce) age far better than the specifics.

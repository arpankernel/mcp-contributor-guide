# MCP Contributor Field Guide

A practical field guide and Claude Code skill for contributing to the
[`modelcontextprotocol`](https://github.com/modelcontextprotocol) org: the SDKs
(`go-sdk`, `python-sdk`, `typescript-sdk`, and the rest), the reference
`servers`, the `inspector`, the spec repo, and the extension repos.

It exists because the gap between "I want to help" and "my PR merged" in this
org is wider than it looks. These are among the most actively contributed repos
on GitHub, each has its own contribution rules (one of them does not accept PRs
at all, another requires a formal proposal process), and a lot of time gets
wasted on issues that are already being worked, or on fixes whose premise does
not survive contact with the maintainer. This repo turns that hard-won,
learned-the-hard-way knowledge into a reusable playbook.

**Works for both humans and AI coding agents** (Claude Code, Cursor, Codex,
OpenCode, and Kilo Code all have an entry point here; see [`USAGE.md`](./USAGE.md)).

## Start here

- **New? Read [`USAGE.md`](./USAGE.md)** for how to use this repo (as a human,
  or with an AI agent via the `/mcp-contribute` skill).
- **Using an AI agent?** Point it at [`llms.txt`](./llms.txt) and
  [`AGENTS.md`](./AGENTS.md), or invoke the [`/mcp-contribute` skill](./.claude/skills/mcp-contribute/SKILL.md).

## Supported AI coding tools

Each tool has a native entry point here. `AGENTS.md` is the canonical source;
the tool-specific files point to it, so there is one loop to keep fresh.

| Tool | Entry point |
|------|-------------|
| Claude Code | `CLAUDE.md` + `AGENTS.md` + the `/mcp-contribute` skill |
| Cursor | `.cursor/rules/mcp-contribute.mdc` |
| Codex (OpenAI CLI) | `AGENTS.md` |
| OpenCode | `AGENTS.md` |
| Kilo Code | `.kilocode/rules/mcp-contribute.md` |

See [`USAGE.md`](./USAGE.md) for how to invoke each and how to reuse the entry
points in another project.

## What's in here

```
README.md         you are here: overview
USAGE.md          how to use this repo (human and agent)
llms.txt          machine-facing index for LLMs/agents
AGENTS.md         step-by-step contribution loop for agents
CLAUDE.md         pointer so Claude Code conventions work too
guide/
  01-finding-work.md               find work that is actually open
  02-repo-playbook.md              every org repo: PRs vs issues vs SEP, + build/test
  03-opening-a-pr.md               conventions + the workflow-scope push fix
  04-finding-and-verifying-bugs.md premise-scrutiny, reproduction, bug taxonomy
.claude/skills/mcp-contribute/     the /mcp-contribute Claude Code skill
.cursor/rules/                     Cursor rule entry point
.kilocode/rules/                   Kilo Code rule entry point
```

Agent entry points: Claude Code (`CLAUDE.md` + skill), Codex and OpenCode
(`AGENTS.md`), Cursor (`.cursor/rules/`), Kilo Code (`.kilocode/rules/`).
`AGENTS.md` is the canonical source; the tool-specific files point to it.

## The guide, in reading order

1. [Finding work that is actually open](./guide/01-finding-work.md): the org is
   saturated; here is how to find genuinely-fresh, unclaimed work instead of
   racing five other PRs (the timeline freshness check, the `ready for work`
   magnet trap, strike-first).
2. [Per-repo playbook](./guide/02-repo-playbook.md): covers **every
   non-archived repo in the org**, grouped into five contribution models (takes
   PRs / issues-only / the spec's SEP process / extension repos / working-groups
   and infra), with build and test commands for the ones that take code.
3. [Opening a PR without tripping](./guide/03-opening-a-pr.md): commit/PR
   conventions, and the fork-behind-upstream `workflow`-scope push rejection
   with its cherry-pick workaround.
4. [Finding and verifying bugs](./guide/04-finding-and-verifying-bugs.md): a
   bug-class taxonomy, how to reproduce by class, and the premise-scrutiny step
   that saves you from shipping a fix that gets closed as "working as intended."

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

## Two facts worth knowing up front

- **`inspector` does not accept external pull requests.** Open a detailed issue;
  maintainers implement it. Do not prepare an inspector PR.
- **The spec repo (`modelcontextprotocol`) uses the SEP process** for protocol
  changes. Docs and small schema fixes are normal PRs; protocol changes are not.

## Status and accuracy

Observations here were gathered in **September 2026**. Contribution policies,
labels, and file paths change. Treat specifics (line numbers, label names, exact
policies) as starting points and re-verify against the live repo before relying
on them. The methods (how to find fresh work, verify, reproduce) age far better
than the specifics.

## Contributing to this guide

Found something out of date, or a repo rule that changed? Open an issue or PR
here. Keep entries grounded in what you verified, and flag anything that is
point-in-time.

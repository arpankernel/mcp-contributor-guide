# How to use this repo

This repo helps you contribute to the [`modelcontextprotocol`](https://github.com/modelcontextprotocol)
org without the usual wasted effort. There are two ways to use it: as a human
reading a guide, or as (or with) an AI coding agent that runs the playbook for
you.

## Quick start

```bash
git clone https://github.com/arpankernel/mcp-contributor-guide
cd mcp-contributor-guide
```

You need `gh` (authenticated: `gh auth status`) and `jq` for the commands in the
guide, plus the toolchain of whatever repo you want to contribute to (Go, Python
with `uv`, Node, etc.). See [`guide/02-repo-playbook.md`](./guide/02-repo-playbook.md)
for per-repo tooling.

## Option A: use it as a human

Read the four chapters in order:

1. [`guide/01-finding-work.md`](./guide/01-finding-work.md) — find work that is
   actually open (the freshness check that catches already-claimed issues).
2. [`guide/02-repo-playbook.md`](./guide/02-repo-playbook.md) — pick a repo you
   can build; learn its contribution model and build/test commands.
3. [`guide/03-opening-a-pr.md`](./guide/03-opening-a-pr.md) — conventions plus
   the `workflow`-scope push fix.
4. [`guide/04-finding-and-verifying-bugs.md`](./guide/04-finding-and-verifying-bugs.md)
   — verify a bug is real and reproduce it before you invest.

The commands in the chapters are copy-paste ready; swap `<repo>` and `<N>` for
your target.

## Option B: use it with an AI coding agent

The repo ships entry points for the major AI coding tools. `AGENTS.md` is the
single canonical source of the loop; each tool-specific file is a thin pointer
to it plus the non-negotiable rules, so there is one source of truth to keep
fresh.

| Tool | What it reads | Notes |
|------|---------------|-------|
| **Claude Code** | `CLAUDE.md`, `AGENTS.md`, and the `/mcp-contribute` skill | Invoke `/mcp-contribute` for the full loop with commands embedded |
| **Cursor** | `.cursor/rules/mcp-contribute.mdc` | Agent-requested rule; fires when the task matches its description |
| **Codex** (OpenAI Codex CLI) | `AGENTS.md` | Reads `AGENTS.md` at the repo root by convention |
| **OpenCode** | `AGENTS.md` | Follows the AGENTS.md convention |
| **Kilo Code** | `.kilocode/rules/mcp-contribute.md` | Loaded as a workspace rule |

So from any of these, just ask the agent to find an MCP issue to work on, or to
evaluate/reproduce/fix a specific one, and it will follow the verified loop
(detect the repo's contribution model, find genuinely-fresh work, verify the
premise, reproduce, make a small tested change, open the PR or a detailed issue
for inspector).

### Reusing it in another project

Copy the entry point(s) for your tool into the project you are working in:

- Claude Code: `.claude/skills/mcp-contribute/` (plus `CLAUDE.md`/`AGENTS.md`)
- Cursor: `.cursor/rules/mcp-contribute.mdc`
- Kilo Code: `.kilocode/rules/mcp-contribute.md`
- Codex / OpenCode: `AGENTS.md`

Bring the `guide/` directory along too if you want the full depth available to
the agent. Note: tool conventions evolve; if an agent is not picking up the
rules, check that tool's current docs for where it loads project rules.

## Common scenarios

| You want to... | Do this |
|----------------|---------|
| Find an issue worth taking | Agent: `/mcp-contribute`. Human: `guide/01` (timeline freshness check) |
| Check if a specific issue is already claimed | Run the timeline check in `guide/01` / skill Step 1 on that issue number |
| Know if a repo takes PRs and how to build it | `guide/02` (inspector is issues-only; the spec repo uses SEP) |
| Decide whether a reported bug is worth fixing | `guide/04` premise-scrutiny: "what real client hits this?" |
| Reproduce an SDK bug | `guide/04` reproduction methods (black-hole peers, `-race`, in-memory transports) |
| Fix a rejected push ("...workflow scope...") | `guide/03` cherry-pick-onto-fork-main workaround |
| Get the commit/PR conventions right | `guide/03` (and read a few recently merged PRs in the target repo) |

## Keep it honest

Specifics in this repo (labels, file paths, exact policies) were gathered at a
point in time and drift. Re-verify against the live repo before relying on a
specific. The methods (how to find fresh work, verify, reproduce) age well; the
specifics do not.

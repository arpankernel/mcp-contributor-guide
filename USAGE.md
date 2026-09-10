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

The repo is built to be consumed by agents (Claude Code, Cursor, and similar).

- **Claude Code:** copy the skill into your working project, or work from inside
  this repo, and invoke it:

  ```
  /mcp-contribute
  ```

  The skill ([`.claude/skills/mcp-contribute/SKILL.md`](./.claude/skills/mcp-contribute/SKILL.md))
  runs the whole loop with the commands embedded: detect the repo's contribution
  model, find genuinely-fresh work, verify the premise, reproduce, make a small
  tested change, and open the PR (or a detailed issue, for inspector).

- **Other agents / LLMs:** point the agent at [`llms.txt`](./llms.txt) (the
  machine-facing index) and [`AGENTS.md`](./AGENTS.md) (the step-by-step loop).
  [`CLAUDE.md`](./CLAUDE.md) is a thin pointer so both harness conventions work.

To reuse the skill in another project, copy the `.claude/skills/mcp-contribute/`
directory into that project's `.claude/skills/`.

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

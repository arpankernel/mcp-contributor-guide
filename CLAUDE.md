# CLAUDE.md

Guidance for Claude Code (and other agents) working in this repo or using it to
contribute to the `modelcontextprotocol` org.

## What this repo is

A field guide + skill for contributing to MCP org repos effectively. The prose
lives in [`README.md`](./README.md) and [`guide/`](./guide); the machine-facing
index is [`llms.txt`](./llms.txt); the full agent instructions are in
[`AGENTS.md`](./AGENTS.md) (this file intentionally just points there so both
`CLAUDE.md` and `AGENTS.md` harness conventions work).

## How to use it

- To **contribute to an MCP repo**, invoke the skill: `/mcp-contribute`
  (defined in [`.claude/skills/mcp-contribute/SKILL.md`](./.claude/skills/mcp-contribute/SKILL.md)).
  It runs the full loop: pick repo and model, find genuinely-fresh work, verify
  the premise, reproduce, make a small tested change, open the PR (or an issue
  for inspector).
- For the reasoning behind each step, read [`AGENTS.md`](./AGENTS.md) and the
  [`guide/`](./guide) chapters.

## Non-negotiables when contributing

- Verify an issue is unclaimed with the per-issue timeline API, not a text
  search. `ready for work` is a magnet, not an opening.
- `inspector` takes issues, not PRs. The spec repo uses the SEP process for
  protocol changes.
- Verify a bug's premise against code AND spec before writing a fix. Reproduce
  before claiming; test before submitting.
- No AI/tool attribution anywhere (commits, trailers, PR/issue text). Terse,
  human, conventional style. Keep changes small and defensible; stay low-noise.

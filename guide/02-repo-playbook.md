# 2. Per-repo playbook

Each repo has its own contribution model, base branch, and tooling. The most
important fact up front: **`inspector` does not accept external pull requests.**
Get the model right before you write any code.

## Contribution model at a glance

| Repo | Language | Accepts external PRs? | How to contribute |
|------|----------|-----------------------|-------------------|
| `go-sdk` | Go | Yes | Fork, branch, PR against `main` |
| `python-sdk` | Python | Yes | Fork, branch, PR; note v1.x vs v2 lines |
| `typescript-sdk` | TypeScript | Yes | Fork, branch, PR; note v1.x vs v2 lines |
| `servers` | TS + Python | Yes | Fork, branch, PR; bug fixes/usability only |
| `inspector` | TypeScript | **No** | **File a detailed issue**; maintainers implement |
| `registry` | Go | Yes | PRs; but many issues are ops/takedown requests |
| `mcpb` | TypeScript | Yes | PRs (uses Yarn) |

Re-verify a repo's `CONTRIBUTING.md` and `AGENTS.md` before starting; policies
change.

## inspector: issues, not PRs

inspector's `CONTRIBUTING.md` / `AGENTS.md` state plainly: "We accept issues,
not pull requests. Pull requests against this repository are opened by the repo
maintainers only." This applies even to org members with write access. The
contribution path is:

- File a **detailed issue** through the issue forms (blank issues are disabled).
  The bug form auto-labels `bug`; the feature form auto-labels `enhancement`
  and `v2`.
- Include: the problem, reproduction, expected behavior, and (if you prototyped
  a fix) a description or branch link, not a diff.
- A maintainer implements it through their own workflow.

Do not prepare an inspector PR. Put the effort into a crisp, reproducible issue.
Base branch for its active line is `v2/main`; branches use a `v2/` prefix.

## go-sdk

- **Toolchain:** Go (1.26 as of writing).
- **Build:** `go build ./...`
- **Test:** `go test ./mcp/ ./internal/jsonrpc2/` and, for the concurrency-
  sensitive code, `go test -race ./mcp/ ./internal/jsonrpc2/`.
- **Format/vet:** `gofmt -l <files>` (must be empty) and `go vet ./...`.
- **PR title style:** `mcp: <lowercase summary>` (match recent merged PRs).
- **API changes** (new/changed exported signatures) are governed by a design
  process; see the repo's `design/` docs. A plain bug fix does not need a
  design proposal; a new public API does.
- No CLA/DCO observed for routine fixes. Confirm on the live repo.
- Registration-time validation is strict (for example `AddTool` panics on an
  invalid tool), which is a useful place to look for both bugs and the repo's
  conventions.

## python-sdk

- **Toolchain:** Python >= 3.10, managed with `uv`.
- **Setup:** `uv sync --frozen --all-extras`
- **Test:** `uv run pytest` (target a subset with `-k` while iterating).
- **Two release lines:** v2 is current; v1.x is maintained on `v1.x` (and
  `v1.N.x`) branches with active backports. A bug may live only on one line;
  check which line the reporter hit and where the code actually is. The v2 and
  v1.x code can diverge significantly (a bug present in v1.x may have been
  restructured away in v2).
- **Labels:** `P0`..`P3`, plus `ready for work` (remember: a magnet, not an
  opening).
- This is the most saturated repo observed; favor strike-first or self-found
  bugs here.

## typescript-sdk

- **Toolchain:** Node. Install deps and run the repo's `test`/`lint` scripts.
- Two release lines (v1.x and v2), like python-sdk. Check which line an issue
  targets.

## servers (reference servers)

- **What is welcome:** bug fixes, usability improvements, and enhancements that
  demonstrate underused protocol features (Resources, Prompts, Roots). **New
  server implementations are not accepted** (publish those to the MCP registry
  instead). README third-party listing changes are out of scope.
- **TypeScript servers** (`everything`, `filesystem`, `memory`,
  `sequentialthinking`): `cd src/<server> && npm ci && npm run build && npm test`.
  Tests use **vitest**. Node 22.
- **Python servers** (`fetch`, `git`, `time`): `uv sync --frozen --all-extras --dev`,
  then `uv run pytest`, `uv run pyright`, `uv run ruff check .`.
- Apache-2.0 for new contributions; no CLA observed.

## registry

- **Toolchain:** Go.
- Accepts PRs, but a large share of open issues are operational requests
  (server takedowns, deprecations, "register my server", admin edits) that a
  maintainer handles, not code. Filter for genuine code/bug issues.

## mcpb

- **Toolchain:** Node with **Yarn** (enable via `corepack enable` and use
  `corepack yarn ...` if Yarn is not installed globally).
- Many issues are actually Claude Desktop client bugs filed here; the fixable
  ones are in the `mcpb` CLI/library code itself.

## General

- Fork with `origin` = your fork, `upstream` = `modelcontextprotocol/<repo>`.
- Keep your branch current with `upstream`'s base branch before asking for
  review, but see [chapter 3](./03-opening-a-pr.md) for the push gotcha that
  rebasing onto a fresh upstream can trigger.

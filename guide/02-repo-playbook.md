# 2. Per-repo playbook (whole org)

Each repo has its own contribution model, base branch, and tooling. The most
important fact up front: **`inspector` does not accept external pull requests**,
and the **spec repo uses a formal proposal (SEP) process** for protocol changes.
Get the model right before you write any code.

A repo falls into one of five buckets. Re-verify a repo's `CONTRIBUTING.md` and
`AGENTS.md` before starting; policies and tooling change.

- **[A] Code repos that take PRs** (SDKs, servers, tools): normal fork-branch-PR.
- **[B] Issues-only**: you file a detailed issue; maintainers implement.
- **[C] The spec/docs repo**: docs/schema fixes are PRs; protocol changes go
  through the SEP process.
- **[D] Extension repos**: per-extension spec + SDK; often proposal-oriented.
- **[E] Working/interest groups and infra**: discussion or maintainer-only, not
  general-contributor code targets.

The "Verified?" column below marks what was confirmed hands-on (Sept 2026) vs
what you should confirm yourself from the repo's own docs.

## [A] Code repos that accept PRs

| Repo | Language | Verified? | Build / test |
|------|----------|-----------|--------------|
| `go-sdk` | Go | yes | `go build ./...`; `go test ./mcp/ ./internal/jsonrpc2/`; `go test -race ...`; `gofmt -l`; `go vet ./...` |
| `python-sdk` | Python | yes | `uv sync --frozen --all-extras`; `uv run pytest` |
| `typescript-sdk` | TypeScript | partial | Node; run the repo's `test`/`lint` scripts |
| `servers` | TS + Python | yes | TS: `cd src/<s> && npm ci && npm run build && npm test` (vitest, Node 22). Py: `uv sync --frozen --all-extras --dev && uv run pytest && uv run pyright && uv run ruff check .` |
| `csharp-sdk` | C# | no | .NET; see its `CONTRIBUTING.md` (likely `dotnet build` / `dotnet test`) |
| `java-sdk` | Java | no | JVM; see its docs (likely Maven/Gradle) |
| `kotlin-sdk` | Kotlin | no | JVM; see its docs (likely Gradle) |
| `php-sdk` | PHP | no | see its docs (likely Composer + PHPUnit) |
| `ruby-sdk` | Ruby | no | see its docs (likely Bundler + RSpec/minitest) |
| `rust-sdk` | Rust | no | Cargo (`cargo build` / `cargo test`); needs a Rust toolchain |
| `registry` | Go | partial | Go; accepts PRs, but many open issues are ops/takedown/register requests a maintainer handles, not code. Filter for genuine code bugs. |
| `mcpb` | TypeScript | partial | **Yarn** via `corepack enable` then `corepack yarn ...`. Many issues are Claude Desktop client bugs filed here; fixable ones are in the `mcpb` CLI/library code. |
| `conformance` | TypeScript | no | Node; the MCP conformance test suite |
| `quickstart-resources` | TypeScript | no | example servers/clients; small, docs-like |
| `example-remote-server` | TypeScript | no | hosted "Everything" server demo |
| `example-remote-client` | TypeScript | no | example client |

For any repo marked "no": the general method still applies (find fresh work,
verify the premise, reproduce, small tested PR). Do not guess build commands;
read the repo's `CONTRIBUTING.md`. **Only take on a repo whose tests you can
actually run** so you can defend your change.

### servers: scope rules
Welcome: bug fixes, usability improvements, enhancements that demonstrate
underused protocol features (Resources, Prompts, Roots). **Not accepted:** new
server implementations (publish to the MCP registry instead) and README
third-party listing changes.

## [B] Issues only: `inspector`

inspector's `CONTRIBUTING.md`/`AGENTS.md` state: "We accept issues, not pull
requests. Pull requests against this repository are opened by the repo
maintainers only." This applies even to org members with write access. So:

- File a **detailed issue** through the issue forms (blank issues are disabled;
  the bug form auto-labels `bug`, the feature form auto-labels `enhancement`
  + `v2`).
- Include the problem, reproduction, expected behavior, and, if you prototyped a
  fix, a description or branch link, not a diff. A maintainer implements it.
- Do not prepare a PR. Active base branch is `v2/main`; branches use a `v2/`
  prefix.

## [C] The spec/docs repo: `modelcontextprotocol`

This holds the specification, schemas, and docs.

- **Docs and small schema/wording fixes:** normal PRs are welcome.
- **Protocol/spec changes:** go through the **SEP process** (Specification
  Enhancement Proposal), per `modelcontextprotocol.io/community/sep-guidelines`
  and the design principles. Do not open a PR that changes protocol behavior
  without a SEP.
- **Generated files:** do not hand-resolve conflicts in generated files
  (`schema/*/schema.json`, `docs/specification/*/schema.mdx`,
  `docs/seps/*.mdx`). Edit the source (for example `schema/draft/schema.ts`) and
  regenerate.

## [D] Extension repos (`ext-*`, `experimental-ext-*`)

These hold the spec and/or reference SDK for a specific MCP extension. They are
often proposal- and design-oriented, and several are explicitly experimental.
Check each repo's README/CONTRIBUTING for whether it wants PRs or proposals.

- `ext-apps` (TS): MCP Apps protocol spec + SDK.
- `ext-tasks` (TS): tasks extension reference.
- `ext-server-card` (TS).
- `ext-auth` (MDX): authorization extensions (doc/spec).
- `ext-skills` (MDX): skills discovery/distribution (experimental).
- `experimental-ext-grouping` (JS), `experimental-ext-interceptors` (C#),
  `experimental-ext-tool-annotations` (MDX), `experimental-ext-variants` (Go),
  `experimental-ext-triggers-events`: incubation/experimental. Expect
  discussion and proposals over drive-by PRs.

## [E] Working/interest groups and infra (usually not PR targets)

- **Working/interest groups** (discussion, proposals, minutes; not code):
  `agents-wg`, `transports-wg`, `financial-services-interest-group`,
  `experimental-ext-triggers-events`. Contribute by participating in the
  discussion/proposal process, not PRs.
- **Org infra** (maintainer-operated; not general-contributor targets):
  `.github` (org README/discussions), `access` (IaC for access management),
  `actions` (GitHub Actions for repo management), `dns` (IaC for domains),
  `static` (static file hosting). Avoid unless you are doing sanctioned infra
  work.

## General setup (for any [A] repo)

- Fork with `origin` = your fork, `upstream` = `modelcontextprotocol/<repo>`.
- Keep your branch current with `upstream`'s base branch before review, but see
  [chapter 3](./03-opening-a-pr.md) for the `workflow`-scope push rejection that
  rebasing onto a fresh upstream can trigger, and its fix.
- Match the repo's commit/PR conventions (read a few recently merged PRs).

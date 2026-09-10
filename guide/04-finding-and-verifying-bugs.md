# 4. Finding and verifying bugs

Self-found bugs are the most reliable path to uncontested, mergeable work. But a
bug you cannot reproduce, or whose premise does not survive contact with the
code and the spec, is worse than no bug: it wastes your time and the
maintainer's, and a "working as intended" close spends goodwill. This chapter is
about doing it right.

## Premise-scrutiny: the step most people skip

Before writing a single line of fix, confirm the bug is real against **both the
code and the spec**, and ask the question that deflates most plausible reports:

> "What real client, server, or flow actually hits this path?"

If the honest answer is "none," the maintainer will likely close it. Two real
patterns to watch for:

- **Version-keyed behavior.** A "the server rejects X" report can be correct at
  face value but a no-op in practice, because the rejection is keyed on the
  protocol version the request declares. An older client declares an older
  version and keeps getting the old behavior regardless of a version-gate; a
  modern client never sends X. The "fix" changes nothing for any real client.
- **Legacy-only / environment-only.** A real crash that only occurs on a
  maintenance branch the project is sunsetting, or only in a broken/unusual
  environment, is low-gravity even if the traceback looks alarming. Check
  whether the current/primary line is even affected.

Do not take a spec-cited report at face value just because it quotes a MUST and
names a file. Read the code path yourself. Reproduce it. Only then is it real.

## Reproduce it, every time

- **Red-then-green.** Write a test that fails on current code and passes with
  your fix. If you cannot make it fail without the fix, you have not proven the
  bug.
- **Timeouts and deadlines:** to test hangs or deadline handling, use a
  "black-hole" peer (a `net.Listener` that accepts TCP but never responds, or an
  HTTP handler that blocks) and measure elapsed time against the deadline.
- **Concurrency:** a race must be flagged by the race detector (`go test -race`);
  a deadlock must actually hang under a watchdog timeout; a leak must show
  goroutines/resources not returning to a baseline you captured before.
- **End-to-end:** SDKs provide in-memory client/server transports (look for the
  in-memory transport helpers used in existing tests). Use them to drive a real
  request through the real stack and observe the wrong output.
- **White-box vs public API:** reproduce through the public API when you can
  (it is the strongest evidence); drop to an in-package test only when you need
  access to unexported internals.

## A bug-class taxonomy for an MCP SDK

Use this to hunt systematically. Highest-gravity classes first. For each: where
it tends to live and how to detect it.

1. **Spec-compliance / protocol correctness (silent).** Behavior that deviates
   from the spec without erroring. Transport/protocol/header/session code.
   Detect: read each MUST/SHOULD, write a repro asserting the behavior, diff vs
   the spec text. Often low gravity but genuinely uncontested.
2. **Transport robustness / malformed-input.** Bad frames, oversized bodies,
   truncated reads, invalid UTF-8, early EOF that panic or hang the session.
   Transport and wire-decoding code. Detect: feed adversarial input with
   timeouts; assert graceful handling. High gravity (a public endpoint).
3. **Concurrency: races, deadlocks, leaks.** Connection/session code. Detect:
   `go test -race`, stress loops, Close-vs-in-flight, goroutine-count
   assertions. High gravity, hardest to debug.
4. **Cancellation and lifecycle.** Work not stopping on cancel, deadlines not
   respected, Close hanging, sessions not terminated, a response sent after the
   peer cancelled. Detect: black-hole peers, cancel-then-complete races,
   goroutine dumps mid-hang. High gravity (real prod hangs).
5. **Auth / OAuth correctness and security.** Token refresh, DCR, resource
   metadata, `WWW-Authenticate` error codes (RFC 6750), RFC 8707 resource
   param, scope validation, redirect SSRF, token echoing. Auth packages.
   Detect: reproduce flows against the relevant RFCs/specs. High gravity.
6. **Input validation / injection.** Header/body mismatch, Base64 sentinel
   edge cases, header-name constraints, path handling. Detect: tricky values
   (unicode, CRLF, sentinel-shaped, traversal), round-trip + validation tests.
7. **Schema generation / type handling.** Type-to-JSON-Schema bugs: integer
   precision loss beyond 2^53, `$ref` for reused types, nil output schemas,
   alias/tag mismatches, recursive types. Detect: register tools with tricky
   types; inspect the generated schema and round-trip values through a real
   call. Moderate to high (silently wrong schemas break LLM calls).
8. **Error handling / propagation.** Wrong error codes, swallowed errors,
   opaque failures (a bare "connection closed" hiding a real error), grouped
   exceptions obscuring the cause. Detect: trigger each error path, assert the
   spec-mandated code/message surfaces.
9. **API/usability.** Panics on valid-but-unusual input, nil handling, wrong
   return shapes. Detect: exercise the public API with edge inputs.
10. **Version negotiation / backward-compat.** Modern/legacy mismatches,
    discovery-probe fallback, session handling across versions, header-version
    mismatch handling. Detect: connect across versions, mismatched headers,
    non-conforming peers.
11. **Resource limits / memory.** Unbounded buffering, missing frame/event-size
    caps, event-store growth. Detect: large/streamed inputs, memory-growth
    assertions.
12. **Docs / examples mismatches.** Comments or examples that do not match
    behavior. Detect: run the examples, diff docs vs code. Low gravity but
    reliably mergeable.

## A sober expectation

A thorough, systematic hunt across these classes tends to find that these SDKs
are well-built: transport hardened, concurrency sound, auth careful. The
high-gravity bugs that do exist are frequently **already reported and PR'd**
(the saturation pattern holds even for deep bugs). The genuinely-fresh,
self-found bugs are often lower severity, and some have their root cause in a
dependency (for example a schema-generation library), which complicates a clean
single-repo fix.

That is fine. A small, correct, reproduced, uncontested fix that lands is worth
more than a big one that gets closed. And the map itself ("this area is solid,
that area has a known-but-claimed bug") is valuable: it tells you where not to
spend time.

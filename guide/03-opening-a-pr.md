# 3. Opening a PR without tripping

You have a fresh issue, a verified premise, a reproduction, and a tested fix.
Here is how to get it onto GitHub cleanly.

## Commit and PR style

- **Commit subject:** short, conventional, one line. Match the repo's prefix
  convention (go-sdk uses `mcp: ...`; servers uses `fix(<server>): ...`). A body
  only if it genuinely adds information.
- **PR title:** concise, same convention; mirror recent merged PRs.
- **PR description:** one to three lines. What changed, why, and `Closes #<N>`.
  No essay, no "Summary/Testing/Motivation" scaffolding unless the repo's
  template requires it.
- **No AI or tool attribution anywhere.** No `Co-Authored-By` trailers, no
  "generated with" footers, in commits or the PR body. Strip any default a tool
  adds.
- **Link the issue.** Note that `Closes #N` only auto-links/auto-closes on PRs
  targeting the repo's **default branch**. For lines whose base is a
  non-default branch (for example inspector/SDK `v2/main`), `Closes #N` is only
  a cross-reference; it will not hard-link or auto-close on merge.

## The fork-behind-upstream `workflow`-scope push rejection

This one wastes a lot of time the first time you hit it.

**Symptom:** you rebase your branch onto a fresh `upstream/main` and push to your
fork, and the push is rejected with:

```
refusing to allow an OAuth App to create or update workflow
`.github/workflows/<file>.yml` without `workflow` scope
```

**Cause:** your fork's default branch is behind upstream. The commits you are
pushing therefore include upstream commits that touched files under
`.github/workflows/`, and the `gh`/OAuth token you are using lacks the
`workflow` scope required to push workflow-file changes. It is not your commit
that is the problem; it is the upstream history your branch now carries that the
fork does not yet have.

**Fix A (grant the scope):**

```bash
gh auth refresh -s workflow
```

Then push normally. Simple, but requires re-authing the token.

**Fix B (cherry-pick onto the fork's own main, no scope needed):** put just your
commit on top of what the fork already has, so the push delta is only your
files (no workflow files):

```bash
git fetch origin
git checkout -b pr-clean origin/main          # base on the fork's current main
git cherry-pick <your-commit-sha>             # replay only your change
git diff --name-only origin/main...HEAD       # confirm: only your files
# re-run build + tests on this base to be safe
git push origin pr-clean:<branch-name>        # push under your intended branch name
```

Then open the PR from that branch. GitHub computes the PR diff against the
upstream base automatically, so a branch based on a slightly-older fork main is
fine as long as there are no conflicts.

Note: once the fork's object store has caught up with upstream (for example
after one of your PRs merges, or after the fork syncs), a force-push of a
rebased branch often succeeds without the scope, because the push no longer
introduces new workflow-file objects. If in doubt, try the push; if it is
rejected, fall back to Fix B.

## Merge state and CI

- `mergeStateStatus=BEHIND` means main moved ahead; the PR is still mergeable,
  this is cosmetic. `BLOCKED` usually means up-to-date but waiting on a required
  review/check. Do not rebase reflexively just to clear `BEHIND`; rebasing every
  time main moves is a treadmill, and `BEHIND` is not a merge blocker.
- **First-time contributors:** GitHub Actions workflows often require a
  maintainer to approve the first run. No green CI, no merge. After your first
  PR merges, CI typically runs automatically on your later PRs. Updating a stale
  branch (a rebase + push) can also re-trigger the approval prompt and resurface
  the PR in the maintainer's list.

## After opening

- Keep it green and current, then wait. A substantive PR sitting for several
  days is normal OSS latency.
- Do not re-nudge repeatedly. One polite check-in after genuine silence is fine;
  a second within the same week reads as impatient and spends goodwill. A
  maintainer's trust is what gets your *next* PR merged fast, so protect it.
- When a maintainer leaves a review note, address it in a small follow-up commit
  with a terse, human reply. Do not argue a "working as intended" close; learn
  from it (see [chapter 4](./04-finding-and-verifying-bugs.md)).

---
id: governed-code-review
title: "Run a governed code review"
goal: >-
  Put a pull request through CE's governed review gate: an independent reviewer
  inspects the diff, grounds its findings, and submits a verdict that actually
  gates the merge.
status: active

prerequisites:
  - id: onboarded-repo
    description: "The repository is CE-onboarded and `ce` is authenticated."
  - id: open-pr
    description: "There is an open pull request to review."
  - id: independent-reviewer
    description: >-
      A governed reviewer seat is available that is NOT the author of the PR
      (self-review is forbidden by the spine).

triggers:
  - "I want CE to review this PR before it can merge."
  - "I want every PR in my repo to pass an independent, governed review."

steps:
  - id: scope
    action: >-
      Determine the review reason: a full review, a scoped re-review of named
      findings, or a fast base-only-rebase confirmation. Scope sets the work.
  - id: review
    action: >-
      Dispatch a governed reviewer (not the author) to inspect the diff, tests,
      and nearby code, leading with concrete findings ordered by severity.
  - id: verdict
    action: >-
      Submit a GitHub review verdict — approve, comment, or request changes —
      with evidence naming the inspected range or the verified findings.
  - id: gate
    action: >-
      Treat the verdict as a merge gate: only an approval on the current head
      clears the block. A dismissed request-for-changes is not an approval.

expected_outcome:
  - id: submitted-review
    description: "A grounded GitHub review is submitted with an explicit verdict."
  - id: gated-merge
    description: "The PR cannot merge until it has an approval on the current head."
  - id: review-evidence
    description: "The review records the inspected range or the named findings it verified."

mode: dev
gates:
  - review
work_class: story

related:
  - author/first-governed-pr
---

## Overview

Code review is where CE earns its keep. A governed review isn't a rubber stamp
and isn't a vibe check — it's an **independent reviewer, distinct from the
author, whose verdict actually gates the merge.** That independence is enforced
by the spine: a seat cannot review its own work, and a *dismissed* objection
does not silently become an approval.

This is the standalone review gate. Use it on any PR — including ones authored
outside CE — to get a grounded, evidence-backed verdict that blocks the merge
until the code is right.

## What you need

- A **CE-onboarded repo** with `ce` authenticated.
- An **open pull request**.
- A **governed reviewer seat that is not the PR author.** You don't need to wait
  for a dedicated reviewer to free up — assign the review now; any non-author
  governed seat can do it.

## Steps

### 1. Scope — pick the review reason

Not every review is a full read. Tell CE the reason:

- **`full`** — a normal, complete code review. Use this for new content.
- **`scoped-content-change`** — re-review only previously named findings after
  the author addressed them. Don't re-litigate the whole diff.
- **`base-only-rebase`** — the branch was only rebased or re-pinned with no
  content change. CE fast-confirms with `git range-diff <old>..<new>` that
  nothing but the base moved.

Picking the reason keeps the review proportional and honest.

### 2. Review — independent inspection

CE dispatches a **governed reviewer that is not the author**. The reviewer:

- inspects the diff, the relevant tests, and the nearby code,
- leads with **concrete findings ordered by severity**,
- if there are no findings, says so plainly rather than inventing nits.

The reviewer is grounded: it reproduces what it claims (it doesn't transcribe
hashes or assert results it didn't observe).

> If the reviewer turns out to be the author, **halt.** Self-review is not a
> review.

### 3. Verdict — submit a real decision

The reviewer submits a GitHub review with one of: **approve**, **comment**, or
**request changes** — plus evidence naming the inspected range (for a full
review) or the specific findings it verified (for a scoped re-review). The
verdict is on the record, attached to the PR.

### 4. Gate — only an approval clears the block

The verdict is a **merge gate**, not advice:

- A **request-changes** blocks the merge until addressed.
- Addressing findings means a re-review, scoped to those findings, until the
  result is an approval.
- The approval must be on the **current head** — a stale approval from an
  earlier commit doesn't count.
- **Dismissing** a request-for-changes only clears the block; it is *not* an
  approval. Verify the review decision is actually `APPROVED` before you let it
  merge.

## What's next

- Make this the standing gate on every PR so nothing merges unreviewed.
- Pair it with [`author/first-governed-pr`](../../author/first-governed-pr/) for
  the full plan → author → review → merge loop.

## Customization

- **Reason.** Default to `full` for new content; use `scoped-content-change` and
  `base-only-rebase` to keep re-reviews proportional.
- **Work class.** This is tuned for `story`. Raise `work_class` for larger PRs to
  signal deeper expected review; lower it for trivial diffs.
- **Reviewer identity.** Any non-author governed seat qualifies. Keep at least
  one credentialed reviewer available so verdicts can actually be posted.

## Related

- [`author/first-governed-pr`](../../author/first-governed-pr/) — the end-to-end
  loop that this gate plugs into.
- [`FORMAT.md`](../../FORMAT.md) — the format and its mapping to CE's internal
  workflow contract.

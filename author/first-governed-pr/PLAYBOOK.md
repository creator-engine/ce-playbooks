---
id: first-governed-pr
title: "Ship your first governed PR"
goal: >-
  Take a small change from idea to a merged, governed pull request on a
  CE-onboarded repo — plan, author, review, and merge, all under CE's spine.
status: active

prerequisites:
  - id: onboarded-repo
    description: >-
      Your repository is CE-onboarded: the `ce` CLI is installed and
      authenticated against the repo's source host.
  - id: small-change
    description: >-
      You have one small, well-scoped change in mind (a fix, a tweak, a tiny
      feature) — something that fits a `tiny` work class.
  - id: clean-base
    description: "The default branch is green and you can branch from it."

triggers:
  - "I just installed CE and want to make my first real change with it."
  - "I want to feel the full plan → author → review → merge loop once, on something small."

steps:
  - id: plan
    action: >-
      State the change as a problem, not a solution. Let CE shape it into a
      sized plan and a definition of done before any code is written.
  - id: author
    action: >-
      Dispatch a governed author to implement the plan on a fresh branch and
      open a pull request. The author commits locally and pushes; it does not
      self-merge.
  - id: review
    action: >-
      Route the PR to a governed reviewer that is not the author. Address
      findings until the review is an approval on the current head.
  - id: merge
    action: >-
      With an approval on the current head and green CI, ratify and merge via
      the merge gate. Confirm the change is live on the default branch.

expected_outcome:
  - id: merged-pr
    description: "A reviewed, green, governed PR is merged to the default branch."
  - id: first-value
    description: "Your change is live, and you have run the full CE loop once end to end."
  - id: audit-trail
    description: "Plan, review verdict, and merge are all recorded on the PR."

mode: dev
gates:
  - plan
  - author
  - review
  - merge
work_class: tiny

related:
  - review/governed-code-review
---

## Overview

This is the "hello, governed world" of Creator Engine. You'll take one small
change all the way from an idea in your head to a merged pull request — and
you'll do it through CE's governance spine, so you see how each gate works while
the stakes are low.

The whole point of CE is that the *recipe* gets you the value while the *spine*
keeps it safe. Here you experience both: a real change shipped, and four gates
(plan, author, review, merge) crossed cleanly. Once you've done it once on
something tiny, the same loop scales to features and epics — only the work class
and review depth change.

This playbook runs in **dev mode**: you stay hands-on at each gate. (In ceo
mode, you'd frame the goal and ratify the result while CE drives the middle.)

## What you need

- A repository that is **CE-onboarded** — `ce` is installed and authenticated
  for that repo's source host. If you haven't installed CE yet:

  ```sh
  curl --proto '=https' --tlsv1.2 -fsSL https://creator-engine.dev/install.sh | bash
  ```

- **One small change** in mind — a typo fix, a clearer error message, a missing
  guard. Keep it `tiny`. Resist the urge to bundle.
- A **green default branch** you can branch from.

## Steps

### 1. Plan — frame the change, let CE size it

Open your CE controller on the repo and describe the change **as a problem**,
not as a diff. For example: *"The CLI prints a stack trace instead of a clear
message when the config file is missing."*

CE shapes this into a sized plan with a definition of done. For a `tiny` change
the plan is short, but it still exists — a stated intent and a clear "done"
condition are the **plan gate**. Read it. If it captured the wrong thing, say so
now; correcting at the plan gate is free.

> You crossed the **plan gate** when there is an agreed problem statement and a
> definition of done.

### 2. Author — implement on a branch, open the PR

Tell CE to author the change. A governed author seat:

- branches from the green default,
- implements only what the plan describes,
- commits locally and pushes the branch,
- opens a pull request whose body declares the work class (`tiny`).

The author **does not merge its own work** — that boundary is the spine, not a
preference. When this step is done you have a PR with a real diff and a green
(or in-progress) CI run.

> You crossed the **author gate** when there is an open PR implementing the
> plan, authored by a governed seat, not self-merged.

### 3. Review — a governed reviewer that isn't the author

Route the PR to review. CE dispatches a **governed reviewer distinct from the
author** (self-review is blocked by design). The reviewer reads the diff, leads
with concrete findings ordered by severity, and submits a verdict: approve,
comment, or request changes.

If there are findings, address them on the same branch and ask for a re-review.
Keep going until the review is an **approval on the current head** — not a stale
approval from an earlier commit.

For the dedicated, reusable version of this gate, see
[`review/governed-code-review`](../../review/governed-code-review/).

> You crossed the **review gate** when `reviewDecision == APPROVED` on the
> current head, by someone other than the author.

### 4. Merge — ratify and land it

With an approval on the current head and green CI, take it through the **merge
gate**. You (the human) ratify, and CE merges — typically via the merge queue,
which sequences and re-tests before landing. You do not run the merge mechanics
by hand; CE does, after your ratification.

Confirm the change is live on the default branch and the PR is closed as merged.

> You crossed the **merge gate** when the PR is merged to the default branch
> with a recorded ratification.

## What's next

You've run the full CE loop once. From here:

- Try a **`story`-class change** — same four gates, deeper review, a real plan.
- Make CE the standing reviewer on every PR with
  [`review/governed-code-review`](../../review/governed-code-review/).
- When you want to step back from the keyboard, run the same kind of work in
  **ceo mode**: frame the goal, ratify the result, let CE drive the gates.

## Customization

- **Work class.** This playbook is tuned for `tiny`. For larger changes, raise
  `work_class` (`story` / `feature` / `epic`); CE will expect a fuller plan and a
  deeper review, and the G5 work-sizing gate enforces the diff floor.
- **Mode.** Switch `mode` to `ceo` to delegate the middle gates and only stay in
  the loop at plan-in and merge-ratify.
- **Reviewer.** Any non-author governed seat can review. You don't need to wait
  for a "free reviewer" — assign the PR to review immediately.

## Related

- [`review/governed-code-review`](../../review/governed-code-review/) — the
  standalone governed review gate.
- [`FORMAT.md`](../../FORMAT.md) — how this playbook is structured and how it
  maps onto CE's internal workflow contract.

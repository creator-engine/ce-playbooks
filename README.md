# CE Playbooks

Recipes for getting real work done with **Creator Engine (CE)**.

Each playbook is a short, task-oriented guide a human can read *and* hand
straight to their CE controller to run. The same file is both a doc and a
machine contract — read it yourself, or say "run this playbook" and CE executes
it under its governance spine.

Apache-2.0 licensed (see `LICENSE`). Contributions welcome — see
[`CONTRIBUTING.md`](CONTRIBUTING.md).

## The dual-use idea

A CE playbook has two layers in one file (`PLAYBOOK.md`):

- **Frontmatter** — the machine contract. Goal, prerequisites, ordered steps,
  expected outcome, which governance gates it crosses, and its work class. A CE
  controller reads this to plan and dispatch the work.
- **Prose** — the human doc. What you need, numbered steps, what's next, related
  playbooks.

You never have to choose between "a guide for me" and "a script for the agent."
It is the same artifact. The frontmatter is authoritative for machines, the
prose is authoritative for humans, and the two are kept in sync. Full spec:
[`FORMAT.md`](FORMAT.md).

This format is a human-facing layer on top of CE's internal playbook contract —
it reuses the same field names and maps cleanly onto the internal
`workflow.ce.yml`, so these recipes don't fork CE's source of truth. They are
the front door to it.

## Catalog

Playbooks are organized by CE's governance spine — the path a change travels
from idea to merged value.

### `plan/` — frame and shape the work
_Turn an intent into a governed, sized plan._ (Seeds coming.)

### `author/` — implement under governance
| Playbook | Mode | Work class | What it does |
| --- | --- | --- | --- |
| [first-governed-pr](author/first-governed-pr/) | dev | tiny | Ship your first governed PR end to end: plan → author → review → merge. |

### `review/` — the governed review gate
| Playbook | Mode | Work class | What it does |
| --- | --- | --- | --- |
| [governed-code-review](review/governed-code-review/) | dev | story | Run CE as the governed reviewer gate on a pull request. |

### `merge/` — land it cleanly
_Merge-train, conflict triage, and the merge gate._ (Seeds coming.)

### `operate/` — run the system
_Onboarding, secrets, day-2 operations._ (Seeds coming.)

### `ceo-mode/` — delegate and ratify
_Frame the goal, let CE drive, ratify the result._ (Seeds coming.)

## How to run a playbook with your CE controller

1. **Install CE** (if you haven't):

   ```sh
   curl --proto '=https' --tlsv1.2 -fsSL https://creator-engine.dev/install.sh | bash
   ```

   This installs the `ce` CLI and runs an authenticated onboard inventory. No
   sudo, no GitHub mutation, no first-project changes happen during install.

2. **Open a CE controller** on your onboarded repo.

3. **Hand it the playbook.** Two equivalent ways:

   - Point the controller at the folder, e.g.
     `author/first-governed-pr/PLAYBOOK.md`, and say *"run this playbook."* The
     controller reads the frontmatter as the contract and the prose as context.
   - Or, once `ce playbook` ships, run it directly:

     ```sh
     ce playbook run author/first-governed-pr
     ```

     > `ce playbook` is the planned native entry point. Until it lands, hand the
     > `PLAYBOOK.md` to your controller directly — the format is designed so the
     > controller can act on the frontmatter without any extra tooling.

4. **Stay in your mode.** In **dev mode** you stay hands-on at each gate (plan,
   author, review, merge). In **ceo mode** you frame the goal and ratify the
   result; CE drives the gates in between. Each playbook declares its `mode`.

The governance spine is always on: nothing merges without passing the gates the
playbook declares. That is the point — the recipe gets you the value; CE keeps
it safe.

## Layout

```
README.md            this catalog + philosophy
FORMAT.md            the playbook format spec
CONTRIBUTING.md      how to add a playbook
LICENSE              Apache-2.0
plan/                frame & shape
author/              implement under governance
  first-governed-pr/PLAYBOOK.md
review/              the governed review gate
  governed-code-review/PLAYBOOK.md
merge/               land it cleanly
operate/             run the system
ceo-mode/            delegate & ratify
```

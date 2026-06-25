# ce-playbooks Format

This repository holds **user-facing CE playbooks**: recipes a human reads to get
real work done with Creator Engine, that the human can *also* hand straight to
their CE controller to run. Each playbook is dual-use — the same file is a
readable doc and a machine contract.

This format is a **human-primary layer on top of** the internal CE playbook
contract (`schemas/playbook.schema.yaml` and `docs/contracts/playbook-format.md`
in the `creator-engine` repo). It deliberately reuses those field names. It does
not fork a competing schema. The internal format stays the single source of
truth for what a CE controller actually dispatches; this format is the front
door to it.

## One playbook = one folder

Each playbook lives in a folder named after its `id`, under a spine directory
(`plan/`, `author/`, `review/`, `merge/`, `operate/`, `ceo-mode/`):

```
author/first-governed-pr/
  PLAYBOOK.md
```

A playbook folder contains exactly one required file, `PLAYBOOK.md`. Anything
else (images, a generated `workflow.ce.yml`, sample answers) is optional and
supporting.

## `PLAYBOOK.md` anatomy

`PLAYBOOK.md` has two parts:

1. **YAML frontmatter** — the machine contract. A CE controller can read this
   directly to plan and run the playbook.
2. **Markdown prose** — the human doc. Overview, prerequisites, numbered steps,
   customization, related links.

The dual-use property: **the frontmatter is authoritative for machines, the
prose is authoritative for humans, and the two must agree.** A reader follows
the prose; a controller follows the frontmatter. If they diverge, the playbook
is broken.

### Frontmatter schema

The frontmatter reuses internal `playbook.schema.yaml` field names wherever they
apply, and adds a small set of CE-specific keys for the human/governance layer.

```yaml
# --- reused from the internal playbook contract ---
id: first-governed-pr        # slug; MUST equal the folder name
                             #   (maps to internal playbook.name)
title: "Ship your first governed PR"
goal: >-                     # one-sentence outcome (maps to playbook.summary)
  Take a small change from idea to a merged, governed pull request on a
  CE-onboarded repo.
status: active               # active | draft | deprecated

prerequisites:               # DoR; each is {id, description}
                             #   (maps to internal preconditions[])
  - id: onboarded-repo
    description: "Your repo is CE-onboarded (`ce` is installed and authenticated)."

triggers:                    # optional; when a human/controller reaches for this
  - "I want to make my first change with CE."

steps:                       # the ordered flow; each is {id, action}
                             #   (maps to internal stages[] — see "Mapping" below)
  - id: plan
    action: "State the change as a problem and let CE shape it into a plan."
  - id: author
    action: "Dispatch a governed author to implement the change on a branch."

expected_outcome:            # DoD; each is {id, description}
                             #   (maps to internal outputs[])
  - id: merged-pr
    description: "A reviewed, green, governed PR is merged to the default branch."

# --- CE-specific human/governance keys (extend the internal contract) ---
mode: dev                    # ceo | dev — which CE operating mode this is for.
                             #   dev = hands-on SDLC; ceo = delegate-and-ratify.

gates:                       # the governance spine subset this playbook crosses.
                             #   Any of: plan | author | review | merge.
                             #   These are the human-readable names of the
                             #   internal gates[] (dor/dod/review/ratification).
  - plan
  - author
  - review
  - merge

work_class: tiny             # tiny | story | feature | epic
                             #   Ties to the G5 work-sizing gate. Sets the
                             #   expected diff floor and review depth.

related:                     # optional; other playbooks or docs by id/URL
  - review/governed-code-review
```

### Field reference

| Key | Source | Meaning |
| --- | --- | --- |
| `id` | internal `playbook.name` | Slug; **must equal the folder name**. `^[a-z0-9][a-z0-9-]{1,96}$`. |
| `title` | internal `playbook.title` | Human title. |
| `goal` | internal `playbook.summary` | One-sentence outcome. |
| `status` | internal `playbook.status` | `active` \| `draft` \| `deprecated`. |
| `prerequisites[]` | internal `preconditions[]` | DoR. Each `{id, description}`. |
| `triggers[]` | (human layer) | Plain-language "when you'd use this". |
| `steps[]` | internal `stages[]` | Ordered flow. Each `{id, action}`. |
| `expected_outcome[]` | internal `outputs[]` | DoD. Each `{id, description}`. |
| `mode` | **CE-specific** | `ceo` \| `dev`. CE operating mode. |
| `gates[]` | **CE-specific** | Governance-spine subset: `plan`/`author`/`review`/`merge`. |
| `work_class` | **CE-specific** | `tiny` \| `story` \| `feature` \| `epic`. G5 work-sizing. |
| `related[]` | internal `references[]` | Related playbooks/docs. |

`steps[]` here uses `{id, action}` (human-step shape). The internal contract's
`stages[]` uses `{id, title, brief, dispatch_target, gates}`. The mapping below
shows how a human step expands into an internal stage.

## Mapping to the internal `workflow.ce.yml` (no fork)

A user-facing `PLAYBOOK.md` is a **superset projection** of the internal
descriptor, minus the dispatch plumbing. A CE controller (or a generator) turns
this frontmatter into a valid `workflow.ce.yml` deterministically:

| `PLAYBOOK.md` frontmatter | internal `workflow.ce.yml` |
| --- | --- |
| `id` | `playbook.name` |
| `title` | `playbook.title` |
| `goal` | `playbook.summary` |
| `status` | `playbook.status` |
| `mode: dev` (hands-on) | `playbook.type: workflow` |
| `prerequisites[]` | `preconditions[]` (`{id, description}` — same shape) |
| `expected_outcome[]` | `outputs[]` (`{id, description}` — same shape) |
| `gates[]` (`plan`/`author`/`review`/`merge`) | one `gates[]` entry each, typed `dor`/`review`/`ratification`/`dod` |
| `steps[].id` + `steps[].action` | `stages[]` (`brief` written to `briefs/<id>.md`, `dispatch_target` chosen by step kind) |
| `related[]` | `references[]` |

The internal descriptor adds the fields a controller needs to *dispatch* —
`kind: ce-playbook`, `schema_version`, `dispatch.default_target`,
`dispatch.authority_envelope`, per-stage `dispatch_target`, and the stage briefs.
Those are runtime concerns; they are filled by CE, not by the human author of
the recipe. This is why the human layer is a strict, lossless superset of the
*authoring* surface of the internal contract: regenerating one direction is
deterministic, the other direction (internal → human) drops only plumbing.

The rule that protects the SSOT: **`id` is the join key.** A user-facing
playbook with `id: X` is the front door to the internal playbook `name: X`. They
must not disagree on goal, prerequisites, gates, or outcome.

## Validation

A playbook is valid when:

- The folder name equals `id`.
- `id` matches `^[a-z0-9][a-z0-9-]{1,96}$`.
- Frontmatter parses as YAML and carries: `id`, `title`, `goal`, `status`,
  `prerequisites`, `steps`, `expected_outcome`, `mode`, `gates`, `work_class`.
- `mode` ∈ {`ceo`, `dev`}; `work_class` ∈ {`tiny`, `story`, `feature`, `epic`};
  every `gates[]` entry ∈ {`plan`, `author`, `review`, `merge`}.
- `prerequisites[]`, `steps[]`, `expected_outcome[]` are non-empty, and each item
  carries its required keys (`{id, description}` or `{id, action}`).
- The prose contains the human sections (Overview, Prerequisites, Steps,
  Customization, Related) and does not contradict the frontmatter.

See `CONTRIBUTING.md` for the add-a-playbook checklist.

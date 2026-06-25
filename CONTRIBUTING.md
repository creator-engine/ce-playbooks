# Contributing a playbook

Thanks for adding to the CE playbook catalog. Playbooks are Apache-2.0 licensed
(see `LICENSE`); by contributing you agree your work is offered under that
license.

Read [`FORMAT.md`](FORMAT.md) first — it is the full spec. This is the short
checklist.

## Add a playbook

1. **Pick the spine directory** that matches where the work lives:
   `plan/`, `author/`, `review/`, `merge/`, `operate/`, or `ceo-mode/`.

2. **Create one folder named after the `id`**, containing one `PLAYBOOK.md`:

   ```
   <spine>/<id>/PLAYBOOK.md
   ```

   The `id` must be a slug matching `^[a-z0-9][a-z0-9-]{1,96}$`.

3. **Write the frontmatter** (the machine contract). Required keys:
   `id`, `title`, `goal`, `status`, `prerequisites`, `steps`,
   `expected_outcome`, `mode`, `gates`, `work_class`. Reuse the internal
   field shapes — see the field reference in `FORMAT.md`.

4. **Write the prose** (the human doc) with these sections, in order:
   **Overview → What you need → Steps → What's next → Customization → Related.**
   The prose must not contradict the frontmatter.

5. **List it in `README.md`** under the right spine section.

## The id-matching rule (the join key)

`id` is the join key between this user-facing layer and CE's internal playbook
contract. Two hard rules:

- **The folder name MUST equal `id`.**
- **If an internal CE playbook exists with the same `name`, this playbook's
  `id` must match it, and the two must not disagree** on goal, prerequisites,
  gates, or expected outcome. This layer is the front door to the internal
  playbook — never a fork of it.

## Validation checklist

Before opening a PR, confirm:

- [ ] Folder name equals `id`; `id` matches the slug pattern.
- [ ] Frontmatter parses as YAML and carries all required keys.
- [ ] `mode` ∈ {`ceo`, `dev`}.
- [ ] `work_class` ∈ {`tiny`, `story`, `feature`, `epic`}.
- [ ] Every `gates[]` entry ∈ {`plan`, `author`, `review`, `merge`}.
- [ ] `prerequisites[]`, `steps[]`, `expected_outcome[]` are non-empty and each
      item carries its required keys (`{id, description}` or `{id, action}`).
- [ ] Prose has all required sections and agrees with the frontmatter.
- [ ] The playbook is listed in `README.md`.

## Style

- Real content only — no Lorem, no placeholder steps.
- Task-oriented and concrete. Lead with what the reader does.
- Tight. A playbook is a recipe, not an essay.

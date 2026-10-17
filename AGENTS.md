# Working in euphotic

This repo is the plugin: `skills/<name>/SKILL.md` are the product, the manifests at the root make
it installable in Claude Code, Codex and Cursor. `README.md` states what the skills here believe
in - thin process layer, high agent autonomy, intent-driven.

## Writing or changing a skill

Read `skills/writing-great-skills/SKILL.md` first (the user invokes it as `/writing-great-skills`).
It is the single source of truth for how a skill is written, judged and changed here.

Material shared by several skills lives in `shared/`, referenced as `../../shared/<path>` relative
to the skill directory. Packaging rules and the portable frontmatter subset: `shared/CONVENTIONS.md`.

## Before you finish

- `bin/build` regenerates `skills/*/agents/openai.yaml`; `bin/check` lints. Both must pass and the
  generated files are committed - Codex reads them from the repo.
- No `SKILL.md` outside `skills/<name>/`, no symlinks (Codex drops them), no `version` fields
  (every agent tracks the commit SHA, pushing is releasing).

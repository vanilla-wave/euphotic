# euphotic — working in this repo

This repo IS the plugin: `skills/<name>/SKILL.md` are the deliverables, the
manifests at the root make it installable in Claude Code, Codex and Cursor.

- Read `shared/CONVENTIONS.md` before writing or editing a skill.
- After any change under `skills/`: run `bin/build` (regenerates
  `skills/*/agents/openai.yaml`) and `bin/check` (lint + manifest consistency).
  Commit the generated files — Codex reads them from the repo.
- Never add a `SKILL.md` outside `skills/<name>/SKILL.md`; never add symlinks
  (Codex drops them when caching the plugin).
- Shared procedures live in `shared/`, not in a skill. Reference them from a
  skill as `../../shared/<path>` (relative to the skill directory).

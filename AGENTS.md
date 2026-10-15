# Working in euphotic

This repo is the plugin: `skills/<name>/SKILL.md` are the product, the manifests at the root make
it installable in Claude Code, Codex and Cursor. `README.md` states the three principles - thin
process layer, high agent autonomy, intent-driven - and every skill here is judged against them.

## Writing a skill

- **Say what matters, not what to type.** Ordered priorities, what good looks like, the trade-offs,
  the few hard limits. Leave the method to the agent; it is better at choosing it in context than
  you are at predicting the context.
- **Assume competence.** Do not spell out steps a capable agent can derive, do not add confirmation
  prompts, do not ask it to report back between steps. State a default instead, and name the real
  decision points where a human must be asked.
- **Keep it short.** One job per skill. If `SKILL.md` needs more than a screen to explain the job,
  it is two skills, or the detail belongs in `references/` and is read on demand.
- **Hard limits are few and up front.** Every `never` and `always` costs attention and compaction
  may drop everything past the first ~5000 tokens, so put the non-negotiables in the first lines
  and keep the rest as guidance.
- **Procedures shared by several skills live in `shared/`**, referenced as `../../shared/<path>`
  relative to the skill directory - never as a hidden skill the agent is told to invoke.
  Mechanics and the portable frontmatter subset: `shared/CONVENTIONS.md`.

## Before you finish

- `bin/build` regenerates `skills/*/agents/openai.yaml`; `bin/check` lints. Both must pass and the
  generated files are committed - Codex reads them from the repo.
- No `SKILL.md` outside `skills/<name>/`, no symlinks (Codex drops them), no `version` fields
  (every agent tracks the commit SHA, pushing is releasing).

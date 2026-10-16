# Working in euphotic

This repo is the plugin: `skills/<name>/SKILL.md` are the product, the manifests at the root make
it installable in Claude Code, Codex and Cursor. `README.md` states the three principles - thin
process layer, high agent autonomy, intent-driven - and every skill here is judged against them.

## Writing a skill

- **Exact about two things, intent about everything else.** Exact: facts the agent cannot derive
  (commands, flags, field names, IDs, paths, status strings, the output contract, the vocabulary
  of verdicts and categories) and actions it cannot undo (gates before writes, read-only defaults,
  the few hard limits). Everything else is intent: priorities in order, what good looks like, and
  a completion criterion - "done when ..." - instead of a list of steps. Order only where a
  dependency or an irreversible action forces it.
- **The test for a step.** Could a competent agent, given the facts and the completion criterion,
  derive it? Then delete it. If not, it is a fact, a limit, or a forced order - write it as that,
  and say why the order is forced ("schema before call: the names are not guessable").
- **Leave the decisions to the agent.** No confirmation prompts, no reporting back between steps.
  State a default instead, and name the real decision points where a human must be asked.
- **Keep it short.** One job per skill. If `SKILL.md` needs more than a screen to explain the job,
  it is two skills, or the detail belongs in `references/` and is read on demand.
- **Hard limits are few and up front.** Every `never` and `always` costs attention and compaction
  may drop everything past the first ~5000 tokens, so put the non-negotiables in the first lines
  and keep the rest as guidance.
- **Procedures shared by several skills live in `shared/`**, referenced as `../../shared/<path>`
  relative to the skill directory - never as a hidden skill the agent is told to invoke.
  Mechanics and the portable frontmatter subset: `shared/CONVENTIONS.md`.

## Changing a skill

When a skill misbehaves, the fix is almost never one more sentence.

- **Root cause, not symptom.** "The agent skipped step 3" is a symptom. The cause is usually a
  priority that was never stated, a step that should not exist, or two instructions pulling in
  opposite directions. Find it and fix that; do not bolt on a reminder.
- **Subtract before you add.** Try removing, merging or rewording first. A skill that grows with
  every incident is accumulating patches, not improving.
- **A pile of patches is a design smell** - same as in code. When the third fix lands in the same
  place, the design is wrong one level up: the skill's scope, the split between skills, or the
  shared procedure. Redesign there instead of adding the next patch.

## Before you finish

- `bin/build` regenerates `skills/*/agents/openai.yaml`; `bin/check` lints. Both must pass and the
  generated files are committed - Codex reads them from the repo.
- No `SKILL.md` outside `skills/<name>/`, no symlinks (Codex drops them), no `version` fields
  (every agent tracks the commit SHA, pushing is releasing).

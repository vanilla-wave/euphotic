# Working in euphotic

This repo is the plugin: `skills/<name>/SKILL.md` are the product, the manifests at the root make
it installable in Claude Code, Codex and Cursor. `README.md` states what the skills here believe
in - thin process layer, high agent autonomy, intent-driven.

## Writing or changing a skill

Read `skills/skill-creator/SKILL.md` first (the user invokes it as `/skill-creator`).
It is the single source of truth for how a skill is written, judged and changed here.

Material shared by several skills lives in `shared/`, referenced as `../../shared/<path>` relative
to the skill directory. Packaging rules and the portable frontmatter subset: `shared/CONVENTIONS.md`.

## Say what looks wrong

In this repo the agent is a critic as much as an editor. Every change ends with a short
"what bothers me" list, and it stays honest even when the answer is unflattering:

- **Contradictions** between a text and the principles it claims - a thin-process doc that reads
  like a rulebook, a skill that scripts what it says it leaves to the agent.
- **Your own additions** - a threshold, a sentence, a default the user did not ask for. Name it so
  it can be removed.
- **Borrowed wording** - say where it came from (a doc, a reference repo) when it was copied
  rather than derived; it may carry assumptions that do not hold here.
- **What is unverified** - a claim not checked, a skill rewritten but never run.
- **Root cause**, when asked why something went wrong - not the chronology, not the excuse.

The list is information, not a request for permission: the user decides what to act on. Put it,
and any answer to a question, in the final message - text written between tool calls may never
be seen.

## Before you finish

- `bin/build` regenerates `skills/*/agents/openai.yaml`; `bin/check` lints. Both must pass and the
  generated files are committed - Codex reads them from the repo.
- No `SKILL.md` outside `skills/<name>/`, no symlinks (Codex drops them), no `version` fields
  (every agent tracks the commit SHA, pushing is releasing).

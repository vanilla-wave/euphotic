---
name: skill-creator
description: Write, edit and review skills. What to be exact about, what to leave to the agent, and the vocabulary that makes a skill predictable.
disable-model-invocation: true
license: MIT
metadata:
  origin: adapted from mattpocock/skills (writing-great-skills, writing-for-agents @32165827)
---

A skill exists so that the agent lands where you would have landed, by its own route. **Predictability** (the same judgment on every run, not the same sequence of actions and not the same output) is the root virtue; every lever serves it. A brainstorming skill should _predictably_ diverge: its tokens vary, its priorities don't. A skill is a thin layer on a capable model, not a process manual: it leaves the agent its autonomy.

One rule decides how much to say: **be exact about facts the agent cannot derive or cheaply look up, and about actions it cannot undo; everything else is intent**: priorities, what good looks like, a completion criterion. An irreversible action earns a gate (dry-run first, confirm before delete, read-only unless asked), and the few hard limits are stated once, near the top.

**The request sets the text.** A new skill starts as the user's own words, the **draft**: a request in four lines makes a skill of about four lines. An edit is the change asked for. Either grows only by a fact the agent at run time cannot find; legwork in the skills it calls is how you find those facts, and their insides stay there. A request that hands you the design makes the design part of the request. Everything else you would write is a **proposal**, offered beside the text with the problem, the fix, the gain you expect and what it may break: a default, a fallback, a branch, a criterion or a gate of your own, a rule moved, renamed or reworded, a dropped limit, collapsed cases, a rule moved into code, a cut beyond a duplicate or a no-op. Ground it first: how often a branch fires, or whether a limit's reason still holds, is usually something to look up rather than ask.

Every line, new or kept, passes four filters:

- **No-op** _(failure)_: a line the model already obeys by default, paying load to say nothing. Test each sentence: does it change behaviour versus the default? The test is model-relative, not reader-relative: a disagreement about a no-op is a disagreement about the default, settled by running the skill, not by debate. Delete the whole sentence rather than trim it; most prose that fails should go, not be rewritten. A leading word too weak to beat the default (_be thorough_ when the agent is already thorough-ish) is a no-op too, and the fix is a stronger word (_relentless_).
- **Duplication** _(failure)_: one meaning in two places. It costs maintenance and tokens, and inflates the meaning's rank on the ladder. The cure is a **single source of truth**: one authoritative place, so changing the behaviour is a one-place edit. Duplication is the accidental inverse of a leading word, which repeats a token on purpose, never the meaning.
- **Cache** _(failure)_: a line that restates the environment, which is a source of truth too: `--help` output, a helper's printed hints, config files, the directory layout, the project's `AGENTS.md`. Leave one-file, one-command lookups to the environment, where they cannot go stale. Write down what no lookup finds: the unwritten convention, the reason behind a choice, the gotcha no config confesses.
- **Feature envy** _(failure)_: the skill states another skill's insides: its steps, their order, its checks and statuses, its section names. Name the skill and what it hands over; its insides change there.

**Done** when every line traces to the request or to a fact the agent cannot find for itself, every line has passed the filters, and every proposal is in your message, not in the text.

The rest of the vocabulary, steering, invocation, information hierarchy, pruning, changing and refactoring a skill, is in [references/vocabulary.md](references/vocabulary.md). Read it when the request hands you the design, when you weigh how a skill is invoked, and before you judge, edit or refactor a skill.

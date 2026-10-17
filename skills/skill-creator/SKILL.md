---
name: skill-creator
description: Reference for writing, editing and reviewing skills — what to be exact about, what to leave to the agent, and the vocabulary that makes a skill predictable.
disable-model-invocation: true
license: MIT
metadata:
  origin: adapted from mattpocock/skills (skills/productivity/writing-great-skills)
---

A skill exists so that the agent lands where you would have landed, by its own route. **Predictability** — the same judgment on every run, not the same sequence of actions — is the root virtue; every lever below serves it. A skill is a thin layer on a capable model, not a process manual: it carries your **intent** and leaves the agent its autonomy.

**Bold terms** are defined in [`GLOSSARY.md`](GLOSSARY.md); look them up there for the full meaning.

## What a skill is made of

Three kinds of content, mixing freely:

- **Reference** — facts the agent consults on demand: definitions, rules, examples, parameters.
- **Interface** — how to drive a tool: commands, flags, field names, IDs, paths, status strings, the shape of a request or an output. Exact throughout, because none of it can be derived and a guess costs a round trip or a wrong write.
- **Process** — how the work proceeds. Written as intent, not as a script.

One rule decides how much to say: **be exact about facts the agent cannot derive and about actions it cannot undo; everything else is intent.** An undoable action earns a gate — dry-run first, confirm before delete, read-only unless asked — and the few hard limits, stated once, near the top, where compaction will not drop them.

**Intent** has three parts, and a process skill is mostly these:

1. **Priorities**, in order — what wins when things conflict ("the patch is the source of truth, not its description").
2. **What good looks like** — an example or a contrast that lets the agent recognise the target ("a ticket is a note to yourself: 2–5 words, no sections").
3. A **completion criterion** — what _done_ means, not how to get there ("done when every side the question spans is backed by cited source"). It replaces the list of steps: a checkable, exhaustive criterion drives the **legwork** a script would try to enumerate.

A **step** survives only where its order is forced — by a dependency (schema before call: the names are not guessable) or by an irreversible action (triage, then the gate, then the browser). Say why the order is forced. The test for every other step: could a competent agent, given the facts and the completion criterion, derive it? Then delete it — it is **over-scripting**. A step that exists to reassure a human — a confirmation, a report-back between steps, a gate with nothing irreversible behind it — is **ceremony**: state a default instead, and name the real decision points where a human must be asked.

## Invocation

Two choices, trading different costs:

- A **model-invoked** skill keeps a **description**, so the agent can fire it autonomously _and_ other skills can reach it (you can still type its name too). It contributes to **context load** — the description sits in the window every turn. Mechanics: omit `disable-model-invocation`, and write a model-facing description with rich trigger phrasing ("Use when the user wants…, mentions…").
- A **user-invoked** skill strips the description from the agent's reach: only you, typing its name, can invoke it — and no other skill can. Zero context load, but it spends **cognitive load**: _you_ are the index that must remember it exists. Mechanics: set `disable-model-invocation: true`; the `description` becomes human-facing — a one-line summary, trigger lists stripped.

Pick model-invocation only when the agent must reach the skill on its own, or another skill must. If it only ever fires by hand, make it user-invoked and pay no context load.

When user-invoked skills multiply past what you can remember, that piled-up cognitive load is cured by a **router skill**: one user-invoked skill that names the others and when to reach for each.

## Writing the description

A model-invoked **description** does two jobs — state what the skill is, and list the **branches** that should trigger it. Every word increases **context load**, so a description earns even harder pruning than the body:

- **Front-load the skill's leading word** — the description is where it does its invocation work.
- **One trigger per branch.** Synonyms that rename a single branch are **duplication** — "build features using TDD … asks for test-first development" is one branch written twice. Collapse them; keep only genuinely distinct branches.
- **Cut identity that's already in the body.** Keep the description to triggers, plus any "when another skill needs…" reach clause.

## Information hierarchy

Rank each piece of content by how immediately the agent needs it — the **information hierarchy**, a ladder:

1. **In-skill intent** — the primary tier of a process skill: priorities, what good looks like, the completion criterion, and the few steps whose order is forced. What the agent must hold while it works.
2. **In-skill reference** — a definition, rule, interface detail or fact in `SKILL.md`, consulted on demand. Often a legitimately flat peer-set (every rule of a review on one rung) — a fine arrangement, not a smell. _This skill is all reference._
3. **External reference** — reference pushed out of `SKILL.md` into a separate file, reached by a **context pointer**, loaded only when the pointer fires. Spans _disclosed_ reference — a sibling file like `GLOSSARY.md`, still part of the skill — through fully **external reference** that lives outside the skill system and any skill can point at.

Push too little down and the top bloats; push too much and you hide material the agent actually needs. That tension is the whole decision.

**Progressive disclosure** is the move down the ladder — out of `SKILL.md` into a linked file — so the top stays legible. Some skills are used in more than one way, and each distinct way is a **branch** — different runs taking different paths through the skill. Branching is the cleanest disclosure test: inline what every branch needs, and push behind a pointer what only some branches reach. A **context pointer**'s _wording_, not its target, decides when and how reliably the agent reaches the material; keep pointers one hop deep.

Where the ladder decides _how far down_ a piece sits, **co-location** decides _what sits beside it_ once there: keep a concept's definition, rules, and caveats under one heading rather than scattered, so reading one part brings its neighbours with it.

## When to split

**Granularity** is how finely you divide skills, and each cut spends one of the two loads, so split only when the cut earns it. Two cuts:

- **By invocation** — split off a **model-invoked** skill when you have a distinct **leading word** that should trigger it on its own, or another skill must reach it. You pay **context load** for the new always-loaded **description**, so that independent reach has to be worth it.
- **By sequence** — split a run of forced **steps** when the steps still ahead (a step's **post-completion steps**) tempt the agent to rush the one in front of it (**premature completion**). Sharpen the completion criterion first; split only if the rush survives that.

## Pruning

Keep each meaning in a **single source of truth**: one authoritative place, so changing the behaviour is a one-place edit.

Check every line for **relevance**: does it still bear on what the skill does?

Then hunt **no-ops** sentence by sentence, not just line by line: run the no-op test on each sentence in isolation, and when one fails, delete the whole sentence rather than trim words from it. Be aggressive — most prose that fails should go, not be rewritten.

## Leading words

A **leading word** is a compact concept already living in the model's pretraining that the agent thinks with while running the skill (e.g. _lesson_, _fog of war_, _tracer bullets_). Repeated throughout the text (though a strong one may be needed only once), it accumulates a distributed definition and anchors a whole region of behaviour in the fewest tokens, by recruiting priors the model already holds.

It serves predictability twice. In the body it anchors _execution_: the agent reaches for the same behaviour every time the word appears. In the description it anchors _invocation_: when the same word lives in your prompts, docs, and code, the agent links that shared language to the skill and fires it more reliably.

Hunt for opportunities to refactor skills to use leading words. A triad spelled out at three sites (**duplication**), a description spending a sentence to gesture at one idea — each is a passage begging to **collapse** into a single token:

- "fast, deterministic, low-overhead" -> _tight_ — one quality restated across a phase — into a single pretrained word (a _tight_ loop).
- "a loop you believe in" -> _red_ — converts a fuzzy gate into a binary observable state (the loop goes _red_ on the bug, or it doesn't).

## Changing a skill

When a skill misbehaves, the fix is almost never one more sentence.

- **Root cause, not symptom.** "The agent skipped step 3" is a symptom. The cause is usually a priority that was never stated, a step that should not exist, or two instructions pulling in opposite directions. Find it and fix that; do not bolt on a reminder.
- **Subtract before you add.** Try removing, merging or rewording first. A skill that grows with every incident is accumulating **sediment**, not improving.
- **A pile of patches is a design smell** — same as in code. When the third fix lands in the same place, the design is wrong one level up: the skill's scope, the split between skills, the shared procedure, or a helper script that should own the mechanism. Redesign there instead of adding the next patch (**patch pile**).

## Failure modes

Use these to diagnose issues the user may be having with the skill.

- **Over-scripting** — steps the agent would have derived from the facts and the completion criterion. Costs tokens and attention, and takes the route out of the agent's hands where its judgment was the point. The cure is the step test above: keep facts, limits and forced order; delete the rest.
- **Ceremony** — a step that exists to reassure a human rather than to change the outcome: a confirmation with nothing irreversible behind it, a report-back between steps, a stage gate for its own sake. Replace with a default and a named decision point.
- **Premature completion** — ending a step before it's genuinely done, attention slipping to _being done_. Defence, in order: sharpen the completion criterion first (cheap, local); only if it is irreducibly fuzzy _and_ you observe the rush, hide the post-completion steps by splitting (the sequence cut).
- **Duplication** — the same meaning in more than one place. Costs maintenance and tokens, and inflates a meaning's prominence on the ladder past its real rank.
- **Sediment** — stale layers that settle because adding feels safe and removing feels risky. The default fate of any skill without a pruning discipline.
- **Patch pile** — the same place fixed three times. Not a pruning problem: a sign that the design one level up is wrong.
- **Sprawl** — a skill simply too long, even when every line is live and unique. Hurts readability and maintainability and wastes tokens. The cure is the ladder: disclose **reference** behind pointers, and split by **branch** or sequence so each path carries only what it needs.
- **No-op** — a line the model already obeys by default, so you pay load to say nothing. The test: does it change behaviour versus the default? A weak leading word (_be thorough_ when the agent is already thorough-ish) is a no-op; the fix is a stronger word (_relentless_), not a different technique.

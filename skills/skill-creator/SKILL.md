---
name: skill-creator
description: Write, edit and review skills. What to be exact about, what to leave to the agent, and the vocabulary that makes a skill predictable.
disable-model-invocation: true
license: MIT
metadata:
  origin: adapted from mattpocock/skills (writing-great-skills, writing-for-agents @32165827)
---

A skill exists so that the agent lands where you would have landed, by its own route. **Predictability** (the same judgment on every run, not the same sequence of actions and not the same output) is the root virtue; every lever below serves it. A brainstorming skill should _predictably_ diverge: its tokens vary, its priorities don't. A skill is a thin layer on a capable model, not a process manual: it carries your **intent** and leaves the agent its autonomy.

## What a skill is made of

Three kinds of content, mixing freely:

- **Reference**: facts the agent consults on demand: definitions, rules, examples, parameters.
- **Interface**: how to drive a tool: commands, flags, field names, IDs, paths, status strings, the shape of a request or an output. Exact throughout, because a guess costs a round trip or a wrong write.
- **Process**: how the work proceeds. Written as intent, not as a script.

One rule decides how much to say: **be exact about facts the agent cannot derive or cheaply look up, and about actions it cannot undo; everything else is intent.** An irreversible action earns a gate (dry-run first, confirm before delete, read-only unless asked), and the few hard limits are stated once, near the top.

**Intent** has three parts, and a process skill is mostly these:

1. **Priorities**, in order: what wins when things conflict ("the patch is the source of truth, not its description").
2. **What good looks like**: an example or a contrast that lets the agent recognise the target ("a ticket is a note to yourself: 2–5 words, no sections"). State the target, not the ban: "write one-line comments", not "don't write long ones". A ban names the thing it forbids and keeps it in view (_don't think of an elephant_); a prohibition earns its place only as a hard limit you cannot phrase positively, and even then sits beside the positive target. Steering by bans is **negation**.
3. A **completion criterion**: what _done_ means, not how to get there ("done when every side the question spans is backed by cited source"). It replaces the list of steps.

## Steps and completion criteria

A **step** survives only where its order is forced: by a dependency (schema before call: the names are not guessable) or by an irreversible action (the dry-run before the write). Say why the order is forced, and write whatever survives as the fact, limit or forced order it is. The test for every other step: could a competent agent, given the facts and the completion criterion, derive it? Then it is **over-scripting**: delete it. It costs tokens and attention, takes the route out of the agent's hands where its judgment was the point, and breaks differently from a derived route, because the agent follows it past the case it was written for. A step that exists to reassure a human rather than change the outcome (a confirmation with nothing irreversible behind it, a report-back between steps, a gate for its own sake) is **ceremony**, paid every run in turns and autonomy: state a default instead, and name the real decision points where a human must be asked: irreversible actions, scope, taste.

Every forced step ends on its own completion criterion, and so does the skill. A criterion is a lever twice:

- **Clarity**: can the agent tell done from not-done? A vague bound ("understanding reached") invites **premature completion**: ending a step before it is done, attention slipping to _being done_. The visible steps still ahead (the **post-completion steps**) supply the pull; the criterion's clarity is the resistance. Sharpen the bound first (local and cheap); only if it is irreducibly fuzzy _and_ you observe the rush, hide the later steps by splitting the sequence. Hiding works only while the later steps are out of view: a subagent, or a part the human starts next; a nested call that returns to its caller leaves the caller's later steps in view and clears nothing.
- **Demand**: how much it requires. "Every modified model accounted for" forces thorough work where "produce a change list" does not. Demand drives **legwork** (the digging the agent does instead of asking the user, latent in the wording rather than written as its own step), and it binds flat reference too: "every rule applied" is how an all-reference skill carries an exhaustiveness bar. A skill that quits early with no steps to rush through has thin legwork, not premature completion: raise the demand.

The strongest criteria are both checkable and exhaustive.

## Invocation

Two choices, each spending one of two loads:

- A **model-invoked** skill keeps a **description**, so the agent can fire it on its own (you can still type its name). It pays **context load**: the description sits in the window every turn, spending tokens and attention whether or not it fires. Mechanics: omit `disable-model-invocation`, and write a model-facing description carrying the trigger branches ("Use when the user wants…, mentions…").
- A **user-invoked** skill hides the description from the agent: only you, typing its name, can invoke it. Zero context load, but it spends **cognitive load**: _you_ are the index that must remember it exists. Not a cost to minimise: it is the price of human agency, so spend it where human judgment matters and remove it where it does not. Mechanics: set `disable-model-invocation: true`; the `description` becomes a human-facing one-line summary, no trigger list.

Pick model-invocation only when the agent must reach the skill on its own. Material several skills need is a file they point at, not a skill another skill invokes ([`../../shared/CONVENTIONS.md`](../../shared/CONVENTIONS.md) says why).

When user-invoked skills multiply past what you can remember, a **router skill** cures the piled-up cognitive load: one user-invoked skill that names the others and when to reach for each. It can only hint, never fire them.

## Context pointers

A **context pointer** names out-of-context material and encodes the condition for reaching it. A model-invoked skill's description is the top-level one; a line in `SKILL.md` naming a sibling file or a `../../shared/` file is the same object one level down. The pointer's _wording_, not its target, decides when the agent reaches the material, and how reliably. A must-have target behind a weakly worded pointer is a variance bug: sharpen the wording first, and inline the material only if sharpening fails. Keep pointers one hop deep.

A pointer does two jobs: state what the material is, and list the **branches** that should trigger reaching it (a branch is a distinct case the skill handles, so different runs take different paths through it). An always-loaded pointer costs on every turn, so the description earns even harder pruning than the body:

- **Front-load the leading word**: the description is where it does its invocation work.
- **One trigger per branch.** Synonyms that rename a single branch are one branch written twice: "build features using TDD … asks for test-first development". Collapse them.
- **Cut identity the body already carries.**

## Information hierarchy

Rank each piece of content by how immediately the agent needs it, a ladder:

1. **In-skill intent**: priorities, what good looks like, the completion criterion, and the few forced steps. What the agent must hold while it works.
2. **In-skill reference**: a definition, rule, interface detail or fact in `SKILL.md`, consulted on demand. Often a legitimately flat peer-set (every rule of a review on one rung), which is fine, not a smell.
3. **Disclosed reference**: pushed out of `SKILL.md` into a sibling file or into `shared/`, reached by a context pointer, loaded only when the pointer fires.

Push too little down and the top bloats; push too much and you hide material the agent actually needs.

**Progressive disclosure** is the move down the ladder, so the top stays legible. It protects the hierarchy more than it saves tokens: in-file reference that should be disclosed buries the intent and turns attending to it into a coin-flip. Branching is the cleanest test: inline what every branch needs, and disclose what only some branches reach.

**Co-location** decides what sits beside a piece once the ladder has placed it: keep a concept's definition, rules and caveats under one heading rather than scattered, so reading one part brings its neighbours. The test: the skill reads like documentation written for the agent.

**Sprawl** is a skill simply too long, even when every line is live and unique: attention thins across the excess, and every extra line is one more to keep relevant. The cure is the ladder: disclose what only some branches reach, and split by sequence or invocation.

## When to split

Each cut spends one of the two loads, so split only when it earns that:

- **By invocation**: split off a model-invoked skill when a distinct **leading word** you actually use in your prompts should trigger it on its own.
- **By sequence**: split a run of forced steps where the post-completion steps tempt the agent to rush the one in front of it (the last resort under Clarity). The reverse holds too: merging sequences exposes each step to the steps that follow, inviting premature completion.

## Leading words

A **leading word** is a compact concept already living in the model's pretraining that the agent thinks with while running the skill (_lesson_, _fog of war_, _tracer bullets_). Repeated as a token (a strong one may need only one use), never as a sentence, it accumulates a distributed definition and anchors a whole region of behaviour in the fewest tokens, by recruiting priors the model already holds. A coined word works if you define it, but it recruits no priors: you pay in definition tokens what a pretrained word gives free, so reach for an existing word first.

It anchors twice. In the body, _execution_: the agent reaches for the same behaviour every time the word appears, and inside flat reference it focuses attention on a class of thing to look for. In the description, _invocation_: when the same word lives in your prompts, docs and code, the agent links that shared language to the skill and fires it more reliably.

Hunt for restatements a leading word retires: a triad spelled out at three sites, a description spending a sentence to gesture at one idea. Assume every skill carries some; go find them.

- "fast, deterministic, low-overhead" → _tight_ (a _tight_ loop).
- "a loop you believe in" → _red_, turning a fuzzy gate into a binary observable state (the loop goes _red_ on the bug, or it doesn't).

## Pruning

- Keep each meaning in a **single source of truth**: one authoritative place, so changing the behaviour is a one-place edit. **Duplication** (one meaning in two places) costs maintenance and tokens, and inflates the meaning's rank on the ladder. It is the accidental inverse of a leading word, which repeats a token on purpose, never the meaning.
- The **environment** is a source of truth too: `--help` output, a helper's printed hints, config files, the directory layout, the project's `AGENTS.md`. A line that restates it is a **cache**, earning its load only when the lookup is expensive. Cache what the agent cannot find by looking: the unwritten convention, the reason behind a choice, the gotcha no config confesses. Leave one-file, one-command lookups to the environment, where they cannot go stale.
- Check every line for **relevance**: does it still bear on what the skill does? A line loses it by never bearing on the task (exposition, a branch that should be disclosed) or by going stale. Without a pruning discipline the default fate is **sediment**: stale layers that settle because adding feels safe and removing feels risky. The discipline is _subtract before you add_: remove, merge or reword before writing a new line.
- Hunt **no-ops** sentence by sentence: a line the model already obeys by default pays load to say nothing. The test (does it change behaviour versus the default?) is model-relative, not reader-relative: a disagreement about a no-op is a disagreement about the default, settled by running the skill, not by debate. When a sentence fails, delete the whole sentence rather than trim it; most prose that fails should go, not be rewritten. The test grades leading words too: one too weak to beat the default (_be thorough_ when the agent is already thorough-ish) is a no-op, and the fix is a stronger word (_relentless_).

## Changing a skill

- **Root cause, not symptom.** "The agent skipped step 3" is a symptom. The cause is usually a priority never stated, a step that should not exist, or two instructions pulling in opposite directions; the bold failure modes above name the usual ones. Fix the cause: a reminder bolted onto the symptom is the next layer of sediment.
- **A pile of patches is a design smell.** When the third fix lands in the same place (a `never`, a reminder, a special case per incident), the design is wrong one level up: the skill's scope, the split between skills, the shared procedure, or a helper that should own the mechanism. Redesign there (**patch pile**); cutting the patches leaves the design that needed them.

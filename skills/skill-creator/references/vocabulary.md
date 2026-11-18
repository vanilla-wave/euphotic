# Skill vocabulary

The terms `SKILL.md` points here for: what to steer with, how a skill is invoked, where content sits, how a skill is pruned, changed and refactored. They judge a text; what the text says is set by the request.

Three kinds of content mix freely:

- **Reference**: facts the agent consults on demand: definitions, rules, examples, parameters.
- **Interface**: how to drive a tool: commands, flags, field names, IDs, paths, status strings, the shape of a request or an output. Exact throughout, because a guess costs a round trip or a wrong write.
- **Process**: how the work proceeds. Written as intent, not as a script.

A **branch** is a distinct case the skill handles, so different runs take different paths through it.

## Steering

- **Priorities**, in order: what wins when things conflict ("the patch is the source of truth, not its description").
- **What good looks like**: an example or a contrast that lets the agent recognise the target, typical of the class of cases the skill handles: one any run could meet ("a ticket is a note to yourself: 2–5 words, no sections").
- **Negation** _(failure)_: steering by bans. A ban names the thing it forbids and keeps it in view (_don't think of an elephant_), so state the target: "write one-line comments", not "don't write long ones". A prohibition or gate against hypothetical misbehaviour earns its place only from an observed incident or irreversible harm, and even then only as a hard limit you cannot phrase positively, beside the positive target; without an incident it is ceremony — write the positive default. Precedent from your own systems and sessions outweighs a plausible theoretical risk.
- **Completion criterion**: what _done_ means, not how to get there ("done when every side the question spans is backed by cited source"). It replaces the list of steps. Every forced step ends on its own criterion, and so does the skill. The strongest criteria are both checkable and exhaustive.
- **Clarity**: can the agent tell done from not-done? A vague bound ("understanding reached") invites premature completion.
- **Premature completion** _(failure)_: ending a step before it is done, attention slipping to _being done_. The **post-completion steps** (the visible steps still ahead) supply the pull; clarity is the resistance. Sharpen the bound first (local and cheap); only if it is irreducibly fuzzy _and_ you observe the rush, hide the later steps by splitting the sequence. Hiding works only while the later steps are out of view: a subagent, or a part the human starts next; a nested call that returns to its caller leaves the caller's later steps in view and clears nothing. Merging sequences does the reverse: it exposes each step to the steps that follow.
- **Demand**: how much the criterion requires. "Every modified model accounted for" forces thorough work where "produce a change list" does not. It binds flat reference too: "every rule applied" is how an all-reference skill carries an exhaustiveness bar.
- **Legwork**: the digging the agent does instead of asking the user, latent in the wording rather than a step of its own. Demand drives it. A skill that quits early with no steps to rush through has thin legwork, not premature completion: raise the demand.
- **Step**: survives only where its order is forced: by a dependency (schema before call: the names are not guessable) or by an irreversible action (the dry-run before the write). Say why the order is forced, and write the step as the fact, limit or forced order it is.
- **Over-scripting** _(failure)_: a step a competent agent would derive from the facts and the completion criterion. Delete it. It costs tokens and attention, takes the route out of the agent's hands where its judgment was the point, and breaks differently from a derived route, because the agent follows it past the case it was written for.
- **Ceremony** _(failure)_: a step that reassures a human rather than changing the outcome (a confirmation with nothing irreversible behind it, a report-back between steps, a gate for its own sake), paid every run in turns and autonomy. State a default instead, and name the real decision points where a human must be asked: irreversible actions, scope, taste.
- **Leading word**: a compact concept already in the model's pretraining that the agent thinks with (_lesson_, _fog of war_, _tracer bullets_). Repeated as a token (a strong one may need only one use), never as a sentence, it anchors a whole region of behaviour in the fewest tokens, by recruiting priors the model already holds. In the body it anchors execution, and inside flat reference it focuses attention on a class of thing to look for; in the description it anchors invocation, when the same word lives in your prompts, docs and code. A coined word works if you define it, but it recruits no priors: you pay in definition tokens what a pretrained word gives free, so reach for an existing one first. Hunt for restatements a leading word retires (a triad spelled out at three sites, a sentence gesturing at one idea), and assume every skill carries some: "fast, deterministic, low-overhead" → _tight_ (a _tight_ loop); "a loop you believe in" → _red_, turning a fuzzy gate into a binary observable state (the loop goes _red_ on the bug, or it doesn't).
- **Bold** claims a term, so reserve it for real terms and leading words. Bolded ordinary verbs or item headings are usually a symptom, not the fault: invented steps or sections propped up by fabricated terminology — fix the structure, and the bold goes with it.

## Invocation

- **Model-invoked**: the skill keeps a description, so the agent can fire it on its own (you can still type its name). Mechanics: omit `disable-model-invocation`, and write a model-facing description carrying the trigger branches ("Use when the user wants…, mentions…"). Pick it only when the agent must reach the skill on its own.
- **Context load**: the cost of always-loaded material on the agent's window: the description sits there every turn, spending tokens and attention whether or not it fires.
- **User-invoked**: `disable-model-invocation: true`. Only you, typing its name, can invoke it; the `description` becomes a human-facing one-line summary, no trigger list. Zero context load.
- **Cognitive load**: the cost on you: you are the index that must remember which skills exist. Not a cost to minimise: it is the price of human agency, so spend it where human judgment matters and remove it where it does not.
- **Router skill**: one user-invoked skill that names the others and when to reach for each, the cure when user-invoked skills multiply past what you can remember. It can only hint, never fire them.
- **Context pointer**: a line that does two jobs for out-of-context material: says what it is, and lists the branches that should reach it. The description is the top-level one; a line in `SKILL.md` naming a sibling file or a `../../shared/` file is the same object one level down. Its _wording_, not its target, decides when the agent reaches the material, and how reliably: a must-have target behind a weakly worded pointer is a variance bug, so sharpen the wording first and inline the material only if that fails. Keep pointers one hop deep.
- **Description**: an always-loaded pointer, so it earns harder pruning than the body. Front-load the leading word. One trigger per branch: synonyms that rename one branch are one branch written twice ("build features using TDD … asks for test-first development"). Cut identity the body already carries.
- **Split by invocation**: a new model-invoked skill, when a distinct leading word you actually use in your prompts should trigger it on its own. Its description is new permanent context load, so that independent reach has to be worth it.

## Information hierarchy

- **Ladder**: rank content by how immediately the agent needs it. (1) In-skill intent and the few forced steps: what the agent holds while it works. (2) In-skill reference, consulted on demand; often a legitimately flat peer-set (every rule of a review on one rung), which is fine. (3) Disclosed reference, in a sibling file or in `shared/`, reached by a pointer, loaded only when it fires; material several skills need is such a file, not a skill another skill invokes (`shared/CONVENTIONS.md` at the plugin root says why). Push too little down and the top bloats; push too much and you hide what the agent needs.
- **Progressive disclosure**: the move down the ladder. It protects the hierarchy more than it saves tokens: reference that should be disclosed buries the intent and turns attending to it into a coin-flip. Test by branch: inline what every branch needs, disclose what only some reach.
- **Sprawl** _(failure)_: a skill simply too long, even when every line is live and unique: attention thins across the excess, and every extra line is one more to keep relevant. The cure is the ladder: progressive disclosure, and a split by sequence or invocation.
- **Co-location**: once the ladder has placed a piece, keep a concept's definition, rules and caveats under one heading, so reading one part brings its neighbours. The test: the skill reads like documentation written for the agent.

## Pruning

The line filters, no-op, duplication, cache and feature envy, are in `SKILL.md`; these two judge the folder over time.

- **Relevance**: does a line still bear on what the skill does? A line loses it by never bearing on the task (exposition, a branch that should be disclosed) or by going stale. It covers the whole folder: `SKILL.md`, scripts, references, tests, fixtures. A file no agent loads still goes stale; a dead command or a superseded run log is cut like dead prose.
- **Sediment** _(failure)_: stale layers that settle because adding feels safe and removing feels risky; the default fate of a skill without a pruning discipline. The cure is _subtract before you add_: remove, merge or reword before writing a new line.

## Changing a skill

- **Root cause, not symptom.** "The agent skipped step 3" is a symptom. The cause is usually a priority never stated, a step that should not exist, or two instructions pulling in opposite directions; the failure modes above name the usual ones. Fix the cause: a reminder bolted onto the symptom is the next layer of sediment.
- **Overfitting** _(failure)_: the change fitted to the incident that prompted it — its ticket, its screen, its words — in a rule or an example. It cures that one case and shows every later run a case it will not meet; write the fix for the class the incident belongs to.
- **Patch pile** _(failure)_: the same place fixed again and again, a `never`, a reminder, a special case per incident. By the third fix the design is wrong one level up: the skill's scope, the split between skills, the shared procedure, or a helper that should own the mechanism. Redesign there; cutting the patches leaves the design that needed them.
- **Count the decisions.** A skill's size is mostly the number of distinctions it asks the agent to make. Cases that end in the same action are one case: merge them. A two-tier budget that could be one number, or a report field nobody reads, is a proposal.
- **A rule with no judgment in it belongs in code**: a lint, a hook or a helper that refuses, which a sentence cannot be. The move is a cut only if the prose goes with it; otherwise it is a new feature. Code also drops the judgment the prose left room for, so test the rule on real cases before proposing the move.
- **Offer the refactor.** When the edit grew the skill or shows one of the smells below, propose a refactor; it waits for a yes.

## Refactoring

A **refactor** rewrites a skill with its behaviour fixed: every decision it makes survives, the wording and the structure are free. It removes sediment. Done when the skill reads as written in one pass and every decision of the old text is in the new one; a decision you would drop is a proposal.

The **smells** an edit can show:

- **Shotgun surgery**: one requirement changed three or more places in the skill.
- **Divergent change**: one section changed for two requirements that have nothing to do with each other.
- **Feature envy** and **duplication**, two of the filters in `SKILL.md`, are smells too.

One smell shows before the edit, **preparatory refactoring**: the requirement fits no sentence already there and would take a new section or an exception to get in. Ask before the edit: propose reshaping the section it lands in, behaviour fixed, until the change is easy, and on a yes reshape first, then make the change.

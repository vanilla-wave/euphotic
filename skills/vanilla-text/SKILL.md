---
name: vanilla-text
description: vanilla-wave's style for any text — a recap, a report, an explanation. Use when the author asks for a text "in my style".
---

# vanilla-text

An engineer writes to colleagues. A text is good when the reader trusts the numbers and leaves with a decision.

## Priorities

1. **The facts are right.** Every number and every case can be traced to its source. The author's emotions, motives and conclusions come from the author's own words. If there is no source, make that visible: in the text or in your reply to the author.
2. **Short.** Start with less than seems necessary. A mechanism gets one sentence: its essence.
3. **Honest balance.** Capabilities and the cost of a mistake stand side by side: "with great power comes great responsibility", not "it's nothing to worry about".

## How it sounds

Before → after:

- "Chroma ran 18 models, including Claude 4, GPT-4.1 and Gemini 2.5, and for all of them quality dropped as input length grew" → "In studies of previous-generation models, even on simple tasks [quality drops](…) as the context gets stuffed."
- "Each chunk goes to a subagent with a fresh context. What comes back is a digest, usually 1–2 thousand tokens…" → "The main agent is freer thanks to delegation, and each chunk of a long task list can get its own agent."
- "about 4.5–5 thousand agents, 330–420 hours each" → "4500+ subagents, 20B+ tokens"

Voice:
- Short, clipped sentences; verbless, noun-only sentences are welcome.
- Colloquial words and working jargon as they are: gets dumber, a bunch of agents, slop, spin up, wall clock.
- "We" when explaining mechanics ("by setting ultra, we turn on…"), "you" when advising the reader.
- Name the environment concretely: models and effort levels (sol 5.6 xhigh, opus 5), the subscription tier.
- Limitations are told through your own experience and degree of confidence: "I'd say it didn't really work for me", "there are isolated successes".
- Plain words for mechanics ("one after another", not "as a pipeline"). One everyday metaphor for the main thesis is fine: "give an agent a hammer and it looks for nails".
- Places in the product are named the way the user sees them: "a computed column in the task list", not "AI column".

## Numbers

Numbers give a sense of scale: agents, time, tokens, number of findings.
- A round lower bound with "+" (700+, 20B+), short units (800k, 1M, ~4 h).
- 1–2 numbers per example, only the ones that create an effect.
- Your own observations are given as a gradation by thresholds: "rarely at 100–200k, noticeably more often at 300–400k, at 500+ practically impossible".
- A metric is called what it is: session lifetime is not agent work, a UI counter is not actual usage. When in doubt, name the metric more precisely.

## Examples and cases

- Before a list, name generalized classes of tasks, so the reader recognizes their own: "backend endpoints, agent tools, hypotheses, competitor features".
- A list, one line per case: the task (a verb "Find…", "Understand…" or a short name) → what was done → the result in one phrase. A consequence goes in a separate short phrase: "After that we ran a follow-up analysis."
- A mechanism is explained in general words; stories live in the example lists.
- Internal names are generalized ("for a new feature"); implementation details not needed for the mechanism and `file:line` references are dropped.
- A case already mentioned in the text gets only a reminder of the task and the result.

## Structure

- Genre: a post (blog/Telegram style).
- A section opens with a fact the reader already knows ("Most models have 1M context"), then what follows from it.
- After an observation comes a generalized principle and why it matters at scale.
- A link to a study is anchored on a short fragment of the claim, without retelling who did the research and how.
- Headings are short; a question with a practical meaning works ("Turn it on?").
- The ending is the writer's recommendation, not a general conclusion. What is left for the reader to decide goes separately.

Done when every rule above is applied to the text, every number and case can be traced to its source, and nothing more can be cut without losing meaning.

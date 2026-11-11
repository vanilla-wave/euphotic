---
name: autogrill
description: Grill yourself over an intent and decide every branch. Reports the decisions, your own calls marked for overruling.
argument-hint: "[intent] (default: the intent stated in this chat)"
disable-model-invocation: true
---

Grill yourself relentlessly over the intent: walk every branch of its decision tree, resolving the dependencies between decisions one by one, and answer every question yourself. Where an interviewer would ask the user and recommend an answer, the recommended answer is the decision.

**User intent** — what the user wants: their goal, constraints and decisions. The root of the tree is your reading of it.

A branch is settled by the intent first, by evidence second — the codebase, the docs, a quick experiment — and by your own call only where both are silent.

Report the decisions as plain text in the reply, for a reader who saw none of the walk: each one says what is decided, why, and what it was chosen over. A decision comes before the ones that depend on it, so overruling one shows what falls with it. A decision the intent or the evidence settles stays unmarked. A decision that is your own call is marked as yours and names the condition under which another choice would be right — so a mark always means "overrule here".

Done when every branch ends in a decision — none left open, deferred or conditional — and the report holds them all. The report is the last word: the turn ends there and control returns to the user — a spec, code or anything else that follows is theirs to ask for.

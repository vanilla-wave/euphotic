---
name: solve
description: One autonomous run from a task to a proven, reviewed change. Starts wherever what it is handed ends — a bare task, a spec, built work.
argument-hint: "[task | spec | built work] (default: the task stated in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-10/11; 2026-11-12 the report became a short message plus a readable full report, raw output out of both
---

Solve the task in one **autonomous** run that ends in a proven, reviewed change. Nobody answers questions along the way: they reach the user in the report, over work already built on your recommendation. The user reads only the report, often one of a batch, and may know the task only in outline.

**User intent** — what the user wants: their goal, constraints and decisions. A spec the user brings is part of it; what you decide on the way is not.

## Routes

By what you were handed:

- a task that needs design → `autogrill` (`../autogrill/SKILL.md`) → `to-spec` (`../to-spec/SKILL.md`) → `implement` (`../implement/SKILL.md`)
- a spec → `implement`
- a simple task or a bug → your own way
- work already built → straight to proof and review

Every route ends in proof and review per `review-loop` (`../review-loop/SKILL.md`) → `overview` (`../overview/SKILL.md`), the intent as its claim.

`autogrill` runs in a subagent handed only the intent: a walk that sees the build ahead hurries toward it.

## Decisions

A **decision** is what the change settles for the user that the intent leaves open, whether you weighed it or not: look in what a user sees around the task's scenario. Where a skill would ask, decide — by the intent, then evidence, else **your call**.

- The user would not notice → the full report.
- Would notice → a **question**: build your recommendation, ask in the final message. A `review-loop` decision point is one; it holds nothing open.
- A "no" would discard the run, or the step cannot be undone → blocked. In doubt, the question.

What the reviewer finds the change settles is a decision too, and goes the same way.

## Report

The **report** is two texts for that reader, in the user's words for things:

- The final message, told over coffee and no longer than a screen: the task in a line; what waits for the user — the blocker, the questions; what was done and whether it works — what is proven and what is not; every other decision the user would notice; where the work lives. A sentence or two each: the detail is the full report's.
- The full report, `~/ai/euphotic/overview/<YYYY-MM-DD>-<topic>.md`, linked from the message: the same story for a reader the message left uneasy, at a length still read end to end — how the problem showed and what caused it, what changed, the proof behind each promise, what the review found and what became of it, every decision with its reason, what stayed unchecked, how to read the change.

A question or a blocker opens with its situation — what the user does and sees there, why it is theirs to settle — then the options and what each does for the user, or what would unblock the run. "Keep the last results or empty the table?" asks a reader who holds the task; this one needs "a broken query used to hang the list on a skeleton; now the last good results stay and the query is marked, where the ticket expected an empty table".

What `prove`, `review-loop` and `overview` hand back is material, retold: raw output (a log, a transcript, a reviewer's reply, a prompt, a patch) is neither pasted nor linked.

## Done

`review-loop`'s Done holds for the code as it ends, and the report is out: with the message alone the reader can answer every question; with the full report alone, retell the work and start reviewing it.

## Blocked

The run is **blocked** by what neither a call nor work of yours settles: something only the user can give; a `review-loop` that ends not done; an environment that will not recover. An environment problem is investigated first and blocks only on grounds to think it will not clear: a server another session holds is released when that session is done.

Stop there and report the same way, as far as the run got.

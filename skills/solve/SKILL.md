---
name: solve
description: One autonomous run from a task to a proven, reviewed change. Starts wherever what it is handed ends — a bare task, a spec, built work.
argument-hint: "[task | spec | built work] (default: the task stated in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-10/11; 2026-11-12 blocked when the task's own problem is not seen or its cause not found; 2026-11-13 the report opens with what only the run can tell a reader outside it, the rest is its owners' parts as given
---

Solve the task in one **autonomous** run that ends in a proven, reviewed change. The user is outside the run: nobody answers questions along the way, and at its end they come to it cold — one task of a batch, no longer in their head. All that reaches them is the **report**; questions go there, over work already built on your recommendation.

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

- The user would not notice → the report, with its reason.
- Would notice → a **question**: build your recommendation, ask in the report. A `review-loop` decision point is one; it holds nothing open.
- A "no" would discard the run, or the step cannot be undone → blocked. In doubt, the question.

What the reviewer finds the change settles is a decision too, and goes the same way.

## Report

The **report** is one file, `~/ai/euphotic/overview/<YYYY-MM-DD>-<topic>.md`. It opens with what only the run can tell that reader:

- the task as its user met it — what they did, what they expected and what they saw instead, or what they lacked;
- what waits for the user — the blocker, the questions — each from its situation, what the user does and sees there and why it is theirs to settle, to the options and what each does for the user, or what would unblock the run;
- where the work stands and where it lives.

Then the parts, each as its owner gave it: `review-loop`'s report — the proof and the review; the decisions the user would not notice; the `overview`. Nothing raw is added: a log, a transcript, the reviewer's reply, a prompt, a patch is neither pasted nor linked.

The final message is that opening, as written, and the link.

## Done

`review-loop`'s Done holds for the code as it ends, and the report is out: from the final message alone, a reader who never saw the task can say what it was and where it stands, and answer what waits for them.

## Blocked

The run is **blocked** by what neither a call nor work of yours settles: something only the user can give; the task's own problem not seen or its cause not found; a `review-loop` that ends not done; an environment that will not recover. An environment problem is investigated first and blocks only on grounds to think it will not clear: a server another session holds is released when that session is done.

Stop there and report the same way, as far as the run got: a part the run did not reach is absent, and what was tried and seen stands in its place.

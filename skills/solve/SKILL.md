---
name: solve
description: One autonomous run from a task to a proven, reviewed change. Starts wherever what it is handed ends — a bare task, a spec, built work.
argument-hint: "[task | spec | built work] (default: the task stated in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-10/11
---

Solve the task in one **autonomous** run that ends in a proven, reviewed change. Nobody answers questions along the way: they reach the user in the final message, over work already built on your recommendation. The user reads only that message, and may know the task only in outline.

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

- The user would not notice → the report.
- Would notice → a **question**: build your recommendation, ask in the final message — the options and what each does for the user. A `review-loop` decision point is one; it holds nothing open.
- A "no" would discard the run, or the step cannot be undone → blocked. In doubt, the question.

What the reviewer finds the change settles is a decision too, and goes the same way.

## Done

`review-loop`'s Done holds for the code as it ends. The final message opens with the questions, then a **summary** told over coffee: what the problem was, what was done, whether it works — what is proven and what is not. Then, in the message itself, every other decision the user would notice. Under it, for a reader the summary left uneasy: where the work lives, on a path that outlives the session; the `review-loop` report; the overview.

## Blocked

The run is **blocked** by what neither a call nor work of yours settles: something only the user can give; a `review-loop` that ends not done; an environment that will not recover. An environment problem is investigated first and blocks only on grounds to think it will not clear: a server another session holds is released when that session is done.

Stop there. The report is as short as that same reader can follow: what is done and where it lives, what the problem is, what would unblock it, the questions and decisions made on the way.

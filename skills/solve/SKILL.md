---
name: solve
description: One autonomous run from a task to a proven, reviewed change. Starts wherever what it is handed ends — a bare task, a spec, built work.
argument-hint: "[task | spec | built work] (default: the task stated in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-10/11
---

Solve the task in one **autonomous** run that ends in a proven, reviewed change. Nobody answers questions along the way; the user reads only the final message, and may know the task only in outline.

**User intent** — what the user wants: their goal, constraints and decisions. A spec the user brings is part of it; what you decide on the way is not.

## Stages

Design per `autogrill` (`../autogrill/SKILL.md`) — in a subagent handed only the intent: a walk that sees the build ahead hurries toward it → spec per `to-spec` (`../to-spec/SKILL.md`) → build per `implement` (`../implement/SKILL.md`) → proof and review per `review-loop` (`../review-loop/SKILL.md`) → `overview` (`../overview/SKILL.md`), the intent as its claim.

Start where what you were handed ends. A bug fix or a simple task, where the intent and the code settle what to change, is built per `tdd` (`../tdd/SKILL.md`) with no design, spec or tickets.

## No one to ask

Where a skill asks the user or hands the turn back, go on: decide by the intent, then by evidence, else by **your call** — what was chosen, over what, and when another choice would be right. A call stays marked as yours to the end: in the spec, in the final message, and to the reviewer among `Claims to verify`.

## Done

`review-loop`'s Done holds for the code as it ends. The final message opens with a **summary** told over coffee: what the problem was, what was done, whether it works — what is proven and what is not. Under it, for a reader the summary left uneasy: where the work lives, your calls, the `review-loop` report, the overview.

## Blocked

The run is **blocked** by what neither a call nor work of yours settles: something only the user can give; a `review-loop` that ends not done; an environment that will not recover. An environment problem is investigated first and blocks only on grounds to think it will not clear: a server another session holds is released when that session is done.

Stop there. The report is as short as that same reader can follow: what is done and where it lives, what the problem is, what would unblock it.

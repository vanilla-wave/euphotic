---
name: review-loop
description: Drive the work done in this chat to proven and reviewed — prove it once, then review and fix until nothing is open.
argument-hint: "[scope] (default: the work already done in this chat)"
disable-model-invocation: true
metadata:
  short-description: Prove, review and fix the work until nothing is open
  origin: design discussion 2026-11-08; loop idea from no-mistakes, decision boundary from rifty (user fork); 2026-11-11 the review pass split out into feature-review, prove added; 2026-11-12 prove moved out of the loop, taken once; 2026-11-13 the fresh-context reviewer launch moved back in from feature-review, which keeps the review itself
---

Drive the work to proven and reviewed against the user intent. First make sure that what the work was started for is done; only then look at the details.

**User intent** — what the user wants: their goal, constraints and decisions.

Before the loop, once: prove the whole change per `prove` (`../prove/SKILL.md`), the user intent as its claim. A refuted promise is fixed before any review is spent on it.

## Reviewer

Every pass is a review per `feature-review` (`../feature-review/SKILL.md`) by a fresh-context reviewer.

- Launch a fresh-context full agent through your own CLI, non-interactively, with your model, mode and permissions where the interface lets you set them. The `review-loop` section of `~/ai/euphotic/preferences.md`, if present, overrides the reviewer or model. With no external CLI available: a fresh-context subagent on your model, and tell the user at that moment that the review is downgraded.
- Pass it the text of `feature-review` verbatim, then these blocks and nothing else of yours — your choices, assumptions, check results and reasoning stay with you:
  - `User intent` — in the user's formulations and decisions.
  - `Spec` — the path to the spec, when the work was built from one.
  - `Scope` — where this task's changes live: the paths, refs or commands that reach them (committed, uncommitted and new files alike; unrelated dirty changes stay out), with pointers to the project rules and recorded decisions that apply.
- A re-pass is a full pass over the work as it stands, with `Previous findings` — their ids and dispositions — as one more block.

## Loop

- Every change after a pass — yours, or the reviewer's own stray edit or rewritten snapshot — gets a re-pass within the budget.
- The budget is 3 passes (reviewer launches) unless the user sets another. A failed launch is not a pass.
- The same blocker survives a fix and a re-pass with no new evidence: change the diagnosis, the approach or the experiment. Spent budget changes neither a blocker's standing nor Done.

## Answering the findings

- Fixes and their method are yours; the reviewer's suggested fix is advisory. Stay inside the agreed scope: old debt that does not block this result adds no work.
- Reject a blocker with counter-evidence. Answer every should: fix it, disprove it, promote it to a blocker, or defer it with a reason for the report. Judge labels by the criteria, not by how a finding arrived. Rarity of a scenario or smallness of cost alone does not cancel a proven violation of a requirement. There is no arbiter: a disagreement over a blocker that evidence leaves open goes into the report as unresolved.
- Ask the user only at a real decision point: a reachable scenario, options with materially different outcomes for the user, and a choice the intent, answers and accepted decisions leave open. Present the scenario, the outcomes and your recommendation. Independent work continues; the dependent choice waits for the answer.
- A finding is closed by a pass over the actual code: a "fixed" claim or a green test alone closes nothing.

## Done

Every load-bearing promise was proven before review, or carries the reason it could not be. For the code as it ends: every blocker is fixed and verified against the actual code, or rejected with evidence; every should is answered or deferred with a reason.

The report gives the proof as taken before review, then the review axis by axis — the checks and their results, the findings with their dispositions, what stayed unchecked — and names every rejected blocker and every deferred should. A blocker awaiting the user's answer, or an unsettled disagreement over a blocker, means not done: report the task as blocked on exactly that. If the budget runs out before the latest changes are reviewed, the result stays unverified and the work is not done.

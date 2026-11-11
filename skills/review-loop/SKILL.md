---
name: review-loop
description: Drive the work done in this chat to proven and reviewed — prove, review, fix, repeat until nothing is open.
argument-hint: "[scope] (default: the work already done in this chat)"
disable-model-invocation: true
metadata:
  short-description: Prove, review and fix the work until nothing is open
  origin: design discussion 2026-11-08; loop idea from no-mistakes, decision boundary from rifty (user fork); 2026-11-11 the review pass split out into feature-review, prove added
---

Drive the work to proven and reviewed against the user intent. First make sure that what the work was started for is done; only then look at the details.

**User intent** — what the user wants: their goal, constraints and decisions.

## Loop

- Prove the whole change per `prove` (`../prove/SKILL.md`), the user intent as its claim. A refuted promise is fixed before any review is spent on it.
- Review it per `feature-review` (`../feature-review/SKILL.md`), the proof among its `Claims to verify`.
- Every change after a pass — yours, or the reviewer's own stray edit or rewritten snapshot — gets a re-pass within the budget, and new proof for the promises it touches.
- The budget is 3 passes (reviewer launches) unless the user sets another. A failed launch is not a pass.
- The same blocker survives a fix and a re-pass with no new evidence: change the diagnosis, the approach or the experiment. Spent budget changes neither a blocker's standing nor Done.

## Answering the findings

- Fixes and their method are yours; the reviewer's suggested fix is advisory. Stay inside the agreed scope: old debt that does not block this result adds no work.
- Reject a blocker with counter-evidence. Answer every should: fix it, disprove it, promote it to a blocker, or defer it with a reason for the report. Judge labels by the criteria, not by how a finding arrived. Rarity of a scenario or smallness of cost alone does not cancel a proven violation of a requirement. There is no arbiter: a disagreement over a blocker that evidence leaves open goes into the report as unresolved.
- Ask the user only at a real decision point: a reachable scenario, options with materially different outcomes for the user, and a choice the intent, answers and accepted decisions leave open. Present the scenario, the outcomes and your recommendation. Independent work continues; the dependent choice waits for the answer.
- A finding is closed by a pass over the actual code: a "fixed" claim or a green test alone closes nothing.

## Done

For the code as it ends: every load-bearing promise is proven, or carries the reason it could not be; every blocker is fixed and verified against the actual code, or rejected with evidence; every should is answered or deferred with a reason.

The report gives the proof, then the review axis by axis — the checks and their results, the findings with their dispositions, what stayed unchecked — and names every rejected blocker and every deferred should. A blocker awaiting the user's answer, or an unsettled disagreement over a blocker, means not done: report the task as blocked on exactly that. If the budget runs out before the latest changes are reviewed, the result stays unverified and the work is not done.

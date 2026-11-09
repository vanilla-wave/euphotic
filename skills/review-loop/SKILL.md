---
name: review-loop
description: Independent review-verify loop for work done in this chat, judged against the user intent.
argument-hint: "[task] (default: the work already done in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-08; loop idea from no-mistakes, decision boundary from rifty (user fork)
---

With a task in the argument, implement it before the first review pass.

**User intent** — what the user wants: their goal, constraints and decisions. A later decision overrides an earlier one. Your choices and assumptions are separate and stay labelled as yours.

## Reviewer

- Launch a fresh-context full agent through your own CLI, non-interactively, with your model, mode and permissions where the interface lets you set them. The `review-loop` section of `~/ai/euphotic/preferences.md`, if present, overrides the reviewer or model. With no external CLI available: a fresh-context subagent on your model, and tell the user at that moment that the review is downgraded.
- Pass the Review instruction below verbatim, followed by three blocks: `User intent` — in the user's formulations and decisions; `Scope` — this task's changes (committed, uncommitted and new files alike; unrelated dirty changes stay out), with pointers to the project rules that apply; `Claims to verify` — your assumptions and check results. On a re-verification, add `Previous findings` with their ids and dispositions. Your reasoning history stays with you.
- The budget is 3 rounds (reviewer launches) unless the user sets another. A failed launch is not a round.

## Review instruction

You are reviewing work done by another agent. Judge the feature within the Scope below against the User intent below, the project rules Scope points to, and correctness. Investigate the related scenarios and any missing work beyond the diff that the User intent requires. A silent change to observable behaviour — API, CLI, output, errors — that the User intent does not name is a finding. Where behaviour can be executed, verify by running code — tests, a reproduction, the affected paths — rather than by reading alone. Code-changing experiments belong in a temporary copy.

Return numbered findings — surviving findings keep their ids, new ones get unused ids — and name what stayed unchecked. A finding counts as a **blocker** only with all of: a violated requirement or project rule, or a concrete architectural cost (machinery the intent is deliverable without is such a cost); a reachable scenario; its consequence; the evidence. Mark as **should** a material concern or question the evidence does not settle, where being right would noticeably harm the result — and state what answer would settle it. Everything else is a **nit**: it needs no answer, and a reviewer's preference is a nit at most.

## Working the findings

- Fixes and their method are yours; the reviewer's suggested fix is advisory. Stay inside the agreed scope: old debt that does not block this result adds no work.
- Reject a blocker with counter-evidence. Answer every should: fix it, disprove it, promote it to a blocker, or defer it with a reason for the report. Judge labels by the criteria, not by how a finding arrived. Rarity of a scenario or smallness of cost alone does not cancel a proven violation of a requirement. There is no arbiter: a disagreement over a blocker that evidence leaves open goes into the report as unresolved.
- Ask the user only at a real decision point: a reachable scenario, options with materially different outcomes for the user, and a choice the intent, answers and accepted decisions leave open. Present the scenario, the outcomes and your recommendation. Independent work continues; the dependent choice waits for the answer.
- Every change after a pass — yours, or the reviewer's own stray edit or rewritten snapshot — gets a new reviewer launch within the budget, with the previous findings by id and their dispositions. Rounds after the first judge the open findings and the code changed since the previous pass, plus the paths that code affects; settled areas reopen only on new evidence. The reviewer judges the actual code: a "fixed" claim or a green test alone closes nothing.
- The same blocker survives a fix and a re-verification with no new evidence: change the diagnosis, the approach or the experiment. Spent budget changes neither a blocker's standing nor Done.

## Done

Every blocker is fixed and verified against the actual code, or rejected with evidence; every should is answered or deferred with a reason. The report names the checks and their results, what stayed unchecked, every rejected blocker and every deferred should. A blocker awaiting the user's answer, or an unsettled disagreement over a blocker, means not done: report the task as blocked on exactly that. If the budget runs out before the latest changes are reviewed, the result stays unverified and the work is not done.

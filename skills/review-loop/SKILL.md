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
- Pass the Review instruction below verbatim, followed by three blocks: `User intent` — in the user's formulations and decisions; `Scope` — this task's changes (committed, uncommitted and new files alike; unrelated dirty changes stay out), with pointers to the project rules that apply; `Claims to verify` — your assumptions and check results. On a re-verification, add `Previous findings` with their dispositions. Your reasoning history stays with you.

## Review instruction

You are reviewing work done by another agent. Judge the feature within the Scope below against the User intent below, the project rules Scope points to, and correctness. Where behaviour can be executed, verify by running code — tests, a reproduction, the affected paths — rather than by reading alone. Code-changing experiments belong in a temporary copy.

Return findings and name what stayed unchecked. A finding counts as a **blocker** only with all of: a violated requirement or project rule, or a concrete architectural cost; a reachable scenario; its consequence; the evidence. Everything else is a note — report it with its uncertainty; notes create no mandatory work, and a reviewer's preference is a note at most.

## Working the findings

- Fixes and their method are yours; the reviewer's suggested fix is advisory. Stay inside the agreed scope: old debt that does not block this result adds no work.
- Reject a blocker with counter-evidence. There is no arbiter: a material disagreement evidence leaves open goes into the report as unresolved.
- Ask the user only at a real decision point: a reachable scenario, options with materially different outcomes for the user, and a choice the intent, answers and accepted decisions leave open. Present the scenario, the outcomes and your recommendation. Independent work continues; the dependent choice waits for the answer.
- Every change after a pass — yours, or the reviewer's own stray edit or rewritten snapshot — gets a new reviewer launch with the previous findings and their dispositions. The reviewer judges the actual code: a "fixed" claim or a green test alone closes nothing.
- The same blocker survives a fix and a re-verification with no new evidence: change the diagnosis, the approach or the experiment. Spent budget changes neither a blocker's standing nor Done.

## Done

Every blocker is fixed and verified against the actual code, or rejected with evidence. The report names the checks and their results, what stayed unchecked, and every rejected blocker. A blocker awaiting the user's answer, or an unsettled material disagreement, means not done: report the task as blocked on exactly that.

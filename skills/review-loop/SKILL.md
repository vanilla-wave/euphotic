---
name: review-loop
description: Independent review-verify loop for work done in this chat, judged against the user intent.
argument-hint: "[task] (default: the work already done in this chat)"
disable-model-invocation: true
metadata:
  origin: design discussion 2026-11-08; loop idea from no-mistakes, decision boundary from rifty (user fork)
---

Close the loop on work done in this chat: an independent reviewer checks the result against the **user intent**, you fix what holds up, and the actual result gets re-verified.

**User intent** is what the user wants to achieve: their goal, constraints, exclusions and acceptance criteria, as amended by their later answers (a later decision overrides an earlier one). Your own design choices and assumptions are not part of it — label them as yours wherever you hand them over. Intent is never a retelling of the implementation: "retry after transient network errors, never duplicate a payment, keep the public interface" — not "added retry to the HTTP client".

Invoked with a task, do the task first, then run the loop on its result before reporting done. Invoked with no task, run it on the work this chat has produced. Either way the loop is part of the task, not its end: when it closes, carry on with whatever else the user asked for and fold the outcome into your report on the whole task.

## The reviewer

The reviewer is a full agent with a fresh context, not your own second look. Default: the same agent and mode as you — launch your own CLI non-interactively (`codex exec`, `claude -p`, `agent -p`; confirm the flags with `--help`), matching your model and permissions where the interface lets you set them, so it works under the same limits you do. If `~/ai/euphotic/preferences.md` exists, its `review-loop` section may name a different reviewer or model; that choice wins. If no external CLI is available, fall back to a fresh-context subagent inheriting your model — and tell the user at that moment that the review is downgraded, not in the final report.

Hand the reviewer the user intent (the user's formulations and decisions, not your paraphrase of the result), pointers to the project rules that apply, and the scope of the changes — including uncommitted and new files that belong to this task; unrelated dirty changes are out of scope. Your assumptions and your own check results go in labelled as claims to verify, not as facts. Your reasoning history stays behind.

The brief: judge the feature within the bounds of the task against the user intent, the project's rules and correctness, reading whatever surrounding code that takes. It may run checks; experiments that change code belong in a temporary copy. It returns findings, not fixes, and names what it did not check. A finding earns its place with a violated requirement or project rule — or a concrete architectural cost — plus a reachable scenario, its consequence and the evidence. A reviewer's preference alone creates no work.

## Working the findings

You own the fixes and choose how to make them; a reviewer's suggested fix is a suggestion. Verify each finding's grounds before acting on it, and stay inside the agreed scope — old debt that does not block this result does not expand the task. Push back on a finding that does not hold, with the counter-evidence. There is no arbiter: a material disagreement you cannot settle with evidence goes into your report as unresolved — neither silently closed nor automatically escalated to the user.

Ask the user only at a real decision point: a reachable scenario where the options differ materially in the result for the user and the intent, their answers and accepted decisions do not already settle the choice. Facts you can establish, technical routes to the same result, and trouble caused by your own chosen implementation are yours to work through first. Present the decision with the scenario, the differing outcomes and your recommendation; while waiting, independent work continues and the dependent choice is not taken by silence.

Any change after a review pass — including anything the pass itself changed (a stray edit, a rewritten snapshot), never silently accepted — is re-verified by a new reviewer launch, given the previous findings and their dispositions. The reviewer judges the actual result — "fixed" and a green test do not close a finding by themselves. The same finding surviving a fix and a re-verification without new evidence means the loop is making no progress: change the diagnosis, the approach or the experiment. Spent budget never downgrades a defect or closes the loop as success.

## Done

Done when every material finding is settled — fixed and verified against the actual code, or rejected with evidence — and your report on the task names the checks and their results, what stayed unchecked, and every material pushback. A finding awaiting the user's answer, or a material disagreement evidence has not settled, means the loop is not done: end by reporting the task as blocked on exactly that, never as a finished result. "No findings" is a statement of what the reviewer checked, not a proof that nothing is wrong.

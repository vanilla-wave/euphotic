---
name: feature-review
description: Independent fresh-context review of a feature against the user intent — one read-only pass, findings by axis.
argument-hint: "[scope] (default: the work done in this chat)"
disable-model-invocation: true
metadata:
  short-description: Fresh-context review of a feature against the user intent
  origin: split from review-loop 2026-11-11, which keeps the loop; decision boundary from rifty (user fork); 2026-11-12 Claims to verify dropped, the reviewer gets the intent and pointers to the work, not the author's account; a re-pass became a full pass, only answered findings stay closed
---

One review pass over the work by a fresh-context reviewer. Read-only: the work is judged here, not fixed.

**User intent** — what the user wants: their goal, constraints and decisions. A later decision overrides an earlier one. Your choices and assumptions are not part of it.

## Reviewer

- Launch a fresh-context full agent through your own CLI, non-interactively, with your model, mode and permissions where the interface lets you set them. The `feature-review` section of `~/ai/euphotic/preferences.md`, if present, overrides the reviewer or model. With no external CLI available: a fresh-context subagent on your model, and tell the user at that moment that the review is downgraded.
- Pass the Review instruction below verbatim, then these blocks and nothing else of yours — your choices, assumptions, check results and reasoning stay with you:
  - `User intent` — in the user's formulations and decisions.
  - `Spec` — the path to the spec, when the work was built from one.
  - `Scope` — where this task's changes live: the paths, refs or commands that reach them (committed, uncommitted and new files alike; unrelated dirty changes stay out), with pointers to the project rules and recorded decisions that apply.
- A re-pass is a full pass over the work as it stands, with `Previous findings` — their ids and dispositions — as one more block.

## Review instruction

You are reviewing work done by another agent. Judge the feature within the Scope below against the User intent below, along the axes below. A Spec block, when present, is a derivative of that intent: judge against it too; where the two disagree the intent wins and the disagreement is a finding.

1. **Completeness** — everything the User intent requires, including work beyond the diff, is delivered; nothing silently deferred.
2. **Architecture** — fits the project rules and recorded decisions Scope points to; a crossed boundary is named.
3. **Goal drift** — delivery matches the intent; a scope change is explicit, not silent.
4. **Approach cost** — machinery or abstraction the intent is deliverable without, named with its evidence; an unargued "simpler" stays a nit.
5. **Footprint** — every changed file is justified by the intent.
6. **Bugs** — correctness defects in the changed code.
7. **Regressions** — existing behaviour on the paths the change affects holds.
8. **Observable behaviour** — what a user sees (API, CLI, output, errors) matches the intent; a silent change is a finding. So is what the change settles in the cases around the intent's own scenario when the intent does not name it.
9. **Code quality** — smells in the changed code and between its parts; code that is hard to maintain harms the result too.

Investigate the related scenarios these axes touch. Where behaviour can be executed, verify by running code — tests, a reproduction, the affected paths — rather than by reading alone. Code-changing experiments belong in a temporary copy.

`Previous findings`, when present, are judged against the code as it stands now: a claimed fix is checked there; a finding answered any other way reopens only on new evidence.

Return numbered findings, each naming its axis — surviving findings keep their ids, new ones get unused ids — and for every axis what was checked and what stayed unchecked. A finding counts as a **blocker** only with all of: a violated requirement or project rule, or a concrete architectural cost; a reachable scenario; its consequence; the evidence. Mark as **should** a material concern or question the evidence does not settle, where being right would noticeably harm the result — and state what answer would settle it. Everything else is a **nit**: it needs no answer, and a reviewer's preference is a nit at most.

## Done

The pass has returned and its report is relayed axis by axis: what was checked, the findings with their labels, what stayed unchecked.

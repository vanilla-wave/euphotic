---
name: feature-review
description: Review of a feature against the user intent — one read-only pass, findings by axis.
argument-hint: "[scope] (default: the work done in this chat)"
disable-model-invocation: true
metadata:
  short-description: Review of a feature against the user intent
  origin: split from review-loop 2026-11-11, which keeps the loop; decision boundary from rifty (user fork); 2026-11-12 Claims to verify dropped; a re-pass became a full pass, only answered findings stay closed; 2026-11-13 the fresh-context launch moved back to review-loop, the skill is the review itself; 2026-11-16 the report is written for a human (outcomes, verdicts, Take — shape from arch-review), a work's own claim stands in for a missing intent, Previous findings moved to review-loop
---

One review pass over the work against the user intent. Read-only: the work is judged here, not fixed.

**User intent** — what the user wants: their goal, constraints and decisions. A later decision overrides an earlier one. The author's choices and assumptions are not part of it. Where the chat holds no intent for the work — someone else's PR or branch — the work's own claim stands in: the PR body, else the commit messages.

Judge the work in scope along the axes below. A spec the work was built from is a derivative of the intent: judge against it too; where the two disagree the intent wins and the disagreement is a finding.

1. **Completeness** — everything the user intent requires, including work beyond the diff, is delivered; nothing silently deferred.
2. **Architecture** — fits the project rules and recorded decisions that apply; a crossed boundary is named.
3. **Goal drift** — delivery matches the intent; a scope change is explicit, not silent.
4. **Approach cost** — machinery or abstraction the intent is deliverable without, named with its evidence; an unargued "simpler" stays a nit.
5. **Footprint** — every changed file is justified by the intent.
6. **Bugs** — correctness defects in the changed code.
7. **Regressions** — existing behaviour on the paths the change affects holds.
8. **Observable behaviour** — what a user sees (API, CLI, output, errors) matches the intent; a silent change is a finding. So is what the change settles in the cases around the intent's own scenario when the intent does not name it.
9. **Code quality** — smells in the changed code and between its parts; code that is hard to maintain harms the result too.

Investigate the related scenarios these axes touch. Where behaviour can be executed, verify by running code — tests, a reproduction, the affected paths — rather than by reading alone. Code-changing experiments belong in a temporary copy.

## Findings

A finding counts as a **blocker** only with all of: a violated requirement or project rule, or a concrete architectural cost; a reachable scenario; its consequence; the evidence. Mark as **should** a material concern or question the evidence does not settle, where being right would noticeably harm the result — and state what answer would settle it. Everything else is a **nit**; a reviewer's preference is a nit at most.

## Report

Written for a human who has not read the change and reads only this. Lead with the verdict and its reason in a line; a verdict is the heaviest label among the findings it covers, or `pass`.

- **Outcomes** — what the change delivers for a user or the system, one level above the diff, numbered; no files, no mechanisms.
- The axes in order, each with its verdict and what that verdict rests on. Findings are numbered in the order they appear; each carries its label and says what holds or breaks for which outcome before how.
- **Open** — what stays undelivered or unchecked, by outcome.
- **Take** — your view of the change as a whole, as opinion: its direction, what you would do differently, what worries you that no axis caught, what to ask the author.

## Done

The report is out: every axis carries its verdict and what it rests on, every finding its evidence.

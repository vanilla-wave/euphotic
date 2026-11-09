---
name: overview
description: Read-only overview of a change set (PR, branch, or uncommitted work) — what it does and how to read it, so unfamiliar work is understood before it is reviewed.
argument-hint: "[PR number | base ref]  (default: remote default branch)"
disable-model-invocation: true
metadata:
  short-description: Understand a change set before reviewing it
  origin: split from arch-review 2026-11-09; its judging half lives in review-loop and code-review
---

Work here, in this context: the understanding stays available for what
follows. Read-only — no edits, no verdict, no severity. Judging the work is
another skill's job; this one ends where a review can begin.

## Input

- PR number → `gh pr view <n> --json body,headRefName,baseRefName`; diff the
  branch against `origin/<baseRefName>`.
- base ref → current branch against it.
- empty → remote default branch (`origin/HEAD`, else `origin/main`).

Uncommitted changes are part of the change set. Read the full diff and every
changed test. The diff is the evidence; a PR body, a commit message or an
implementer's summary is a claim to check against it, never a substitute
for reading it.

## The claim

What the work says it delivers. Take the first that exists:

1. what the user names on invocation — a ticket, a spec, or a sentence;
2. the PR body;
3. commit messages on the branch.

Nothing usable → say so, ask the user what the change was meant to do, and
still map what the diff shows.

## Report

Describe, don't judge. Where the claim and the diff disagree, state both
sides in **Open**, with `file:line`, and leave them there: no severity, no
required fix, no merge call. The body assigns no importance; anything that
reads as an opinion belongs in **Take**.

- **What** — 3–7 numbered lines (`W1…`) one level above the diff: the
  outcomes the change delivers for a user or the system; no files, no
  mechanisms.
- **Map** — the change by area, each area tied to its outcome (`→ W2`).
  Name what can be skimmed (generated code, mechanical moves) and any file
  that ties to no outcome.
- **Observable behavior** — what a user sees (API, CLI, output, errors):
  before → after, as the diff shows it.
- **Reading order** — where to start: the few entry points that unlock the
  rest, in order, and what to read only on demand.
- **Open** — what the claim leaves uncovered, by outcome; what the diff
  alone could not settle and was taken on faith from a summary.
- **Take** — last. First person, opinion labelled as opinion, no citations
  required. Direction; what feels off or surprisingly good; what I would
  have done differently; what worries me; questions I would ask the author.
  No merge call, no severity, no restating the sections above. Five to
  fifteen lines.

## Done

The reader can retell the change in their own words and start a review:
what it claims, what it visibly does, where to look first, and what remains
unverified. Every outcome in What is accounted for in the map; every
claim-vs-diff gap sits in Open, none passes in the body as fact.

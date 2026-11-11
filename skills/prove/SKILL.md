---
name: prove
description: Evidence pack that the work does what it claims — real runs on the real surface, a verdict per promise.
argument-hint: "[claim | spec | PR] (default: the work done in this chat)"
disable-model-invocation: true
metadata:
  short-description: Evidence pack that the work does what it claims
  origin: design discussion 2026-11-11; proof standards from pstack (prove-it-works, create-verification-skill; michael-denyer/pstack-claude@36f43364)
---

Prove that the work does what it claims: hand over observations, not opinion. The code is neither judged nor fixed here; a refuted promise goes into the report as the result.

**Claim** — what the work promises. Take the first that exists: what the user names on invocation (a ticket, a spec, a sentence); the spec the work was built from; the PR body; the user intent in this chat. Reduce it to its load-bearing promises — what a user or the system would notice if it were broken; usually one to five. The rest stays out of the report.

## Proof

**Proof** — an observation that would have looked different had the promise been false. Any evidence counts; its strength is labelled honestly:

- **measured** — you performed the action and saw the result on the real thing, on the surface where a user touches it (UI, CLI, API, a file, a database row); or a check that fails without this change.
- **inferred** — derived: reading the code, a green test nobody saw red, a run on a stand-in for the real target. Name what stayed unobserved.

Scaffolding around the real thing (an isolated profile, a stubbed dependency) keeps the evidence measured; the report names it.

Good proof shows the action and the resulting state; a side effect (a write, a file, a message sent) is confirmed by a second, independent read; a contrast wherever it is cheap: before → after, or the same run with the change switched off. A check that is a command goes into the report verbatim, so the reader can repeat it. A check you can run is yours to run, not the human's.

## Means and limits

Use whatever the repo and the environment offer: a project verify/run skill or a recorded launch recipe, a dev server, a browser, the CLI, a testing stand, reads of databases and logs, the existing tests, a throwaway script.

The user sets the limits: what they said on invocation, the `prove` section of `~/ai/euphotic/preferences.md`, the project rules. These widen (writes on testing, a browser) as well as forbid. Without an explicit grant: local and reversible, shared environments read-only. A promise that cannot be proven inside the limits stays unproven, naming the grant that would settle it.

Stop what you started; the evidence outlives the cleanup.

## Report

A verdict per promise:

- **proven** — measured evidence covers the whole promise;
- **partly** — part is measured, the rest inferred or uncovered: name which;
- **unproven** — no proof was reached: the prerequisite, the route attempted, what was missing;
- **refuted** — an observation contradicts the promise, or a named part of it: what you did, what you saw.

```
Proof: 3 proven · 1 unproven
C1 <promise> — proven
   did: … → saw: … · evidence: <command | path | output excerpt>
C4 <promise> — unproven: needs writes on testing (not granted)
Not checked: <what a reader would assume was covered>
```

A few lines per promise. What does not fit a line (screenshots, transcripts, scripts) goes to `~/ai/euphotic/proofs/<date>-<topic>/`, secrets redacted, with the path in the report.

## Done

Every load-bearing promise carries a verdict with its evidence or a named reason; nothing inferred stands under proven; everything this run started is stopped.

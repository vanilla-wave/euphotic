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

**Proof** — an observation that would have looked different had the promise been false. The bar is the **strongest proof** the promise admits: its user's own scenario on the surface they use, with nothing the promise depends on replaced by a stand-in. A stand-in the promise does not pass through (a local build of the same code, an isolated profile, seeded data) leaves the proof whole, and the report names it; a shared or production environment is the bar only where the promise depends on what only it has — its config, data, integrations, load. Reach for the strongest proof with every means you have; a means you can build in the run (a script, a fixture, a seeded database) is in reach. A weaker proof stands only where the strongest is out of reach.

Every proof carries its strength, labelled honestly:

- **measured** — you performed the action and saw the result on the real thing, on the surface where a user touches it (UI, CLI, API, a file, a database row); or a check that fails without this change.
- **inferred** — derived: reading the code, a green test nobody saw red, a run on a stand-in for what the promise depends on. Name what stayed unobserved.

Good proof shows the action and the resulting state; a side effect (a write, a file, a message sent) is confirmed by a second, independent read; a contrast wherever it is cheap: before → after, or the same run with the change switched off. A check that is a command goes into the report verbatim, so the reader can repeat it. A check you can run is yours to run, not the human's.

## Means

Use whatever the repo and the environment offer: a project verify/run skill or a recorded launch recipe, a dev server, a browser, the CLI, a testing stand, reads of databases and logs, the existing tests, a throwaway script.

Stop what you started; the evidence outlives the cleanup.

## Report

A verdict per promise:

- **proven** — the strongest proof, measured, covers the whole promise;
- **partly** — measured evidence covers part of the promise, or all of it short of the strongest proof: name what is inferred or uncovered, and the strongest proof with what kept it out — a missing access, environment or tool, a state that cannot exist yet;
- **unproven** — nothing measured was reached: what was inferred, the prerequisite, the route attempted, what was missing;
- **refuted** — an observation contradicts the promise, or a named part of it: what you did, what you saw.

```
Proof: 2 proven · 1 partly · 1 unproven
C1 <promise> — proven
   did: … → saw: … · evidence: <command | path | output excerpt>
C2 <promise> — partly: the flow measured locally, its <service> call inferred (a stub); strongest — the user's scenario against <service> on testing, no access to it
   did: … → saw: … · evidence: …
C4 <promise> — unproven: no access to <service> on testing
Not checked: <what a reader would assume was covered>
```

A few lines per promise. What does not fit a line (screenshots, transcripts, scripts) goes to `~/ai/euphotic/proofs/<date>-<topic>/`, secrets redacted, with the path in the report.

## Done

Every load-bearing promise carries a verdict with its evidence or a named reason; nothing short of the strongest proof stands under proven, and every proof short of it names the strongest and what kept it out; everything this run started is stopped.

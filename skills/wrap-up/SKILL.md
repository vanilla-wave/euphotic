---
name: wrap-up
description: Harvest the session's findings into a markdown doc — learned, decided, measured, ruled out — with doubts marked.
argument-hint: "[topic] (default: the whole session)"
disable-model-invocation: true
---

## Instruction

Harvest what the session learned into one markdown document, narrowed to the topic in the argument if one is given. The reader wants the data, not how to continue the work — that is `handoff`.

Done when every item of these kinds, from the first message to the last, is in the doc or off-topic:

- **Findings** — facts established, each with its evidence (file:line, command output, observed behaviour).
- **Decisions** — what was chosen and why, with the rejected alternatives.
- **Numbers** — measurements, timings, sizes, counts worth keeping.
- **Dead ends** — what was tried and ruled out, and why, so nobody retries it.
- **Open questions** — raised but unanswered.

A summary launders guesses into facts; don't. An item the session never checked — inferred, recalled, taken on someone's word — carries its doubt beside it: what it rests on and what would settle it.

> Retries are capped at 3 — *unverified: from the README, not the code; `grep -rn MAX_RETRIES src/` settles it*

Checked items stay unmarked, so a mark always means doubt.

Durable artifacts (commits, diffs, PRs, ADRs, plans) by path or URL, not retold. Redact secrets and PII.

Save as `~/ai/euphotic/wrap-ups/<YYYY-MM-DD>-<topic>.md` and print the path.

## Fresh context

If the user asks for the document to be written with a fresh context or by a subagent, spawn one with no conversation history and pass it the Instruction, the topic and the path to this session's transcript. Report the path it returns.

---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

## Instruction

Write a handoff document: a briefing for the agent that picks this work up tomorrow, shaped by the focus the user gave in the argument.

Done when that agent can resume without the transcript: where things stand and what of it is verified, decisions and why, dead ends, open questions.

Durable artifacts (plans, ADRs, issues, commits, diffs) by path or URL, not retold. Include a "Suggested skills" section. Redact secrets and PII.

Save as `~/ai/euphotic/handoffs/<date>-<topic>.md` and print the path.

## Fresh context

If the user asks for the document to be written with a fresh context or by a subagent, spawn one with no conversation history and pass it the Instruction, the focus and the path to this session's transcript. Report the path it returns.

---
name: handoff
description: Write a handoff document so a fresh agent can continue this session's work. Argument - what the next session will focus on; start it with "fresh" to have the document written by a fresh agent from the transcript rather than from memory.
disable-model-invocation: true
argument-hint: "[fresh] What will the next session be used for?"
---

Write a handoff document so a fresh agent can continue this session's work. The user's argument is the next session's focus: the document serves that focus, it is not a diary.

## Who writes it

By default you do, from the conversation. `scripts/transcript-digest` (path relative to this skill's directory) prints the transcript path for the Records section and how full your context is.

If the argument asks for a fresh author (`fresh`, «свежим»), you are the witness, not the author: after hundreds of thousands of tokens the agent that did the work is anchored to what it did last and blind to what it never wrote down. Run `scripts/transcript-digest --session ${CLAUDE_SESSION_ID}`, then start an agent with no conversation history - the runtime's subagent, or `codex exec` / `agent -p` / `claude -p` - and give it exactly three things: this file's path (the section "The document" is its brief), the digest path, and the user's focus verbatim. Add nothing from memory. Report the path it returns.

## The document

A fresh author works from the digest: read it whole; where it is ambiguous, the raw record has the tool output - `sed -n '<n>p' <transcript>`.

Done when the next agent can resume without opening the transcript: the goal and whose it is, where the work stands and what of it is verified, decisions with their reasons, what was tried and ruled out, open questions, the focus for the next session. Files, commands and URLs exactly as they appear. This is a briefing for a colleague taking over tomorrow morning: durable artifacts (plans, ADRs, issues, commits, diffs) are referenced by path or URL, not retold. Include a "Suggested skills" section - which skills the next agent should invoke and for what - and the transcript path as the record of last resort. Redact secrets and PII.

Save as `handoff-<slug>-<YYYY-MM-DD>.md` in the OS temporary directory, not the workspace, and print the full path.

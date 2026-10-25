---
name: handoff
description: Write a handoff document so a fresh agent can continue this session's work. Argument - what the next session will focus on.
disable-model-invocation: true
argument-hint: "What will the next session be used for?"
context: fork
agent: general-purpose
---

A handoff is written by its first reader. The agent that did the work is the worst witness of its own session: after hundreds of thousands of tokens it is anchored to what it did last and blind to what it never wrote down. So the author is a fresh agent that reads the record and writes down what it needed in order to understand it - which is exactly what the next agent will need.

**If this conversation has history before this prompt, you are the witness, not the author.** Run `scripts/transcript-digest` (path relative to this skill's directory) and hand this file's body, the paths it printed and the user's argument verbatim to a fresh agent with no history - the runtime's own subagent, or `codex exec` / `agent -p` / `claude -p`. Add nothing from memory. Report the document path it returns.

Otherwise you are the author:

1. `scripts/transcript-digest --session ${CLAUDE_SESSION_ID}` prints the digest - what was said and done, without tool output - and the transcript path. Where the digest is ambiguous, the raw record has the tool output: `sed -n '<n>p' <transcript>`.
2. Read the whole digest before writing. Done when the next agent can resume without opening the transcript: the goal and whose it is, where the work stands and what of it is verified, decisions with their reasons, what was tried and ruled out, open questions, and the focus the user gave for the next session - the document serves that focus, it is not a diary. Files, commands and URLs exactly as they appear.

A handoff is a briefing for a colleague taking over tomorrow morning. Durable artifacts (plans, ADRs, issues, commits, diffs) are referenced by path or URL, not retold. Include a "Suggested skills" section - which skills the next agent should invoke and for what - and the transcript path as the record of last resort. Redact secrets and PII.

Save as `handoff-<slug>-<YYYY-MM-DD>.md` in the OS temporary directory, not the workspace, and print the full path.

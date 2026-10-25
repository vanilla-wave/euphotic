---
name: explain
description: Build a self-contained HTML explainer of what the session established about a topic, and open it.
argument-hint: "[topic] (default: inferred from the discussion)"
disable-model-invocation: true
---

The topic is the argument; without one, infer from the discussion what the user wants explained, and name it.

Settle the explainer's parameters from the request or the discussion; the defaults apply when neither says:

- **Scope** — how far beyond the topic it reaches; decides what's in. Default: one level above the topic, enough to show how it fits the surrounding project.
- **Depth** — how finely each point is unpacked. Default: one step more detailed than the recent messages.
- **Purpose** — what the user wants the explainer for: to see how something works, to prepare a decision, to get back on track after getting lost. No default: often there is none. When there is one, the overview is built for it — what serves the purpose leads and gets the full depth; the rest keeps its usual depth, so the overview loses nothing.

Consolidate what the session established about the topic into one self-contained HTML file, in the session's language: sections big-picture first, each led by a one-sentence plain-language takeaway, an infographic (table, flow, badges…) wherever it beats prose.

Done when every part of the topic the session touched — with its decisions, rejected options and caveats — is in the file and, with a purpose, the leading sections alone give the reader what they came for. The topic is a floor: cover it whole.

Save to `~/ai/euphotic/<YYYY-MM-DD>-<topic-slug>.html`, open it in the browser, print the path with the topic, purpose, scope and depth used.

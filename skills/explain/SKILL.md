---
name: explain
description: Build a self-contained HTML explainer of what the session established about a topic, and open it.
argument-hint: "[topic] (default: inferred from the discussion)"
disable-model-invocation: true
---

The topic is the argument; without one, infer from the discussion what the user wants explained, and name it.

Settle two parameters of the explainer from the request or the discussion; the defaults apply when neither says:

- **Scope** — how far beyond the topic it reaches; decides what's in. Default: one level above the topic, enough to show how it fits the surrounding project.
- **Depth** — how finely each point is unpacked. Default: one step more detailed than the recent messages.

Consolidate what the session established about the topic into one self-contained HTML file, in the session's language: sections big-picture first, each led by a one-sentence plain-language takeaway, an infographic (table, flow, badges…) wherever it beats prose.

Done when every claim, decision, number and caveat the session established about the topic — from its first mention to the latest — is in the file. The topic is a floor: cover it whole.

Save to `~/ai/euphotic/<YYYY-MM-DD>-<topic-slug>.html`, open it in the browser, print the path with the topic, scope and depth used.

---
name: grill-me
description: Grill the user about a plan or design, one question at a time, until every branch of the decision tree is resolved.
disable-model-invocation: true
---

Interview the user relentlessly about the plan until shared understanding: walk every branch of the design tree, resolving the dependencies between decisions one by one.

One question per turn, asked as plain text in the reply with your recommended answer — not through an option picker or question tool. A question the codebase can answer is answered by exploring it, not by asking.

Done when every branch is resolved and the shared understanding is stated in a short summary. The summary is the last word: the turn ends there and control returns to the user — a plan, code or anything else that follows is theirs to ask for.

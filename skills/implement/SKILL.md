---
name: implement
description: "Implement a spec with parallel subagents in separate worktrees."
disable-model-invocation: true
---

You have been provided a spec.

The goal is the entire spec implemented on a single **integration branch**, with every ticket resolved.

The tickets are not a list of steps. They are a **task graph** with blocking relationships between them. This means there is always a **frontier** of tickets which are ready to be grabbed.

Communication to and from subagents should be sparse. Communicate primarily through **context pointers**: to the spec, tickets, research notes, and previous commits. Don't duplicate information already available via pointers.

**Implementer subagents** should be run in the background where possible for maximum concurrency. All subagents inherit your model; do not override it.

## Steps

1. Read the spec and split it into standalone **tracer-bullet** tickets with blocking relationships, one file per ticket in `.scratch/<feature>/`.

2. (optional) Use an **exploration subagent** to conduct any exploration required by the tickets - relevant codebase files or external documentation. Ensure the exploration subagent can save files - it should save its markdown notes in a directory outside the repo, accessible by all future subagents. This lets **implementer subagents** focus on implementation rather than exploration.

3. If already in a worktree, use its current branch as the integration branch; otherwise, create the integration branch. If the user asks for a PR, open a draft PR after the first merge in step 5 (a branch with no commits ahead of main can't open one).

4. Use **implementer subagents** to implement each ticket, each in its own worktree on its own branch. Each implementer subagent:
   - confirms its worktree is based on the integration branch before starting, and resets onto it if not;
   - prepares its own environment in its worktree;
   - builds the ticket per `tdd` (`../tdd/SKILL.md`);
   - merges the integration branch tip into its own branch before reporting done

5. Once an **implementer subagent** completes, merge its work to the integration branch with a **merger subagent**.

6. If this changes the **frontier** of available tickets, kick off more **implementer subagents** to work on the new tickets. This allows for maximum concurrency.

7. Once all tickets are complete, run `code-review` (`../code-review/SKILL.md`) on the integration branch. Fix all issues raised by the code review in a single **implementer subagent**.

8. If a draft PR exists, mark it ready for review. Otherwise, report the integration branch.

9. Clean up all **implementer subagent** worktrees and any `.scratch/<feature>/` ticket files created for this run.

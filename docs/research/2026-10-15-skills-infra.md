# A personal skills store for Claude Code / Codex / Cursor - research and first plan

Date: 2026-10-15. Versions on this machine: Claude Code 2.1.300, codex-cli 0.162.1, cursor `agent` 2026.10.01, `skills` CLI 1.5.9.
Sources: four parallel research passes over official docs, Codex source code and GitHub issues; full URL list at the end.
Legend: **V** - verified against docs/code, **I** - inferred.

> Superseded for distribution by `2026-10-16-portable-plugin.md` (the repo became a plugin instead of a
> symlink farm). The findings on skill mechanics, shared procedures and skill chaining still apply.

## 0. TL;DR

1. A canonical root already exists on this machine - `~/.agents/skills`. **Codex and Cursor read it natively, Claude Code does not** (issue #31005 open). Claude needs per-skill symlinks in `~/.claude/skills/<name>`; that is documented and dedup-safe (V).
2. First plan: the repo as the source, `~/.agents/skills/<name>` and `~/.claude/skills/<name>` as symlinks into it, third-party skills (mattpocock) staying under `npx skills` next to them.
3. Auto-update: launchd `git pull --ff-only` hourly plus (optional) a Claude `SessionStart` hook doing `git pull` with `reloadSkills: true` - this is **the official example in the hooks docs** (V). Codex and Cursor pick up file changes themselves (watcher / at start).
4. **The worry about "private skills" is justified, but the fix is to not make them skills.** Shared procedures are plain files the parent `SKILL.md` tells the agent to read. All three agents handle that identically; it costs no listing budget and never shows up in `/`. Invoking `Skill(X)` from inside a skill exists only in Claude Code and is broken there (#17351, #56912). Rules that must hold every time belong in hooks, not prose.
5. All three CLIs support plugin marketplaces from git, but for a personal repo it looked like an extra layer at the time (cached copies, `/plugin:skill` namespacing, Cursor auto-update only on Teams). Revisited the next day - see the follow-up doc.

## 1. Facts per agent

| | Claude Code | Codex | Cursor |
|---|---|---|---|
| User-level roots | `~/.claude/skills/<name>` (V). **Does not read `~/.agents/skills`** (V, #31005) | `~/.agents/skills` (V); `~/.codex/skills` deprecated but still scanned (V, code) | `~/.agents/skills`, `~/.cursor/skills` + compat `~/.claude/skills`, `~/.codex/skills` (V) |
| Nesting | only `<root>/<name>/SKILL.md`, one level (V/I) | recursive to depth 6; **any** `SKILL.md` at any depth becomes a skill (V, code) | recursive (V) |
| Symlinks | per-skill symlink documented; one target loads once (V) | symlinked directories followed; symlink to a `SKILL.md` *file* is not (#31592); **no dedup by realpath** - symlink + original = duplicate in the picker (V) | followed (IDE since 2.5, CLI since 2026.06.22); duplicates collapsed since 3.17, precedence undefined (V) |
| Frontmatter | full set: `disable-model-invocation`, `user-invocable`, `allowed-tools`, `context: fork`, `agent`, `hooks`, `paths`, `argument-hint` ... (V) | only `name`, `description`; **ignores `disable-model-invocation`** (V/I). Hiding = `agents/openai.yaml` -> `policy.allow_implicit_invocation: false` (V) | `name`, `description`, `paths`, `disable-model-invocation`, `user-invocable` (V); rest ignored (I) |
| What the model sees | listing of name + description; budget **1% of context** (`skillListingBudgetFraction`, env `SLASH_COMMAND_TOOL_CHAR_BUDGET`); description <= 1536 (V) | `## Skills` list `- name: description (file: path)`; budget 2% of context, cap 10000 chars (V) | `<available_skills>` in the system prompt (V) |
| Hidden from the model | `disable-model-invocation: true` -> description not in context, **and not invocable from another skill** (V, explicit error). `user-invocable: false` -> model may, user does not see it in `/`, consumes budget (V) | `allow_implicit_invocation: false` -> skill absent from the prompt entirely; only `$name` typed by the user (V; bug #23454) | `disable-model-invocation: true` -> `/name` only (V) |
| Invocation mechanism | Skill tool; `SKILL.md` content injected as one message (V) | **no Skill tool**: the model reads `SKILL.md` by the path from the catalog; `$name` from the user injects the content (V, code) | `/name` attaches to the message; auto by description (V) |
| Skill -> skill | possible through the Skill tool, but: #17351 (open since 2026-01) - the parent workflow **does not resume** after a nested call; #56912 - `allowed-tools: Skill(child)` ignored -> permission prompt; #77795 - dmi skills invisible from subagents (V) | no mechanism; `$other` inside a `SKILL.md` body **is not parsed** as a mention (I, parser code) -> the model simply reads the file by path. A hidden skill has no path in the catalog -> state it explicitly | undocumented; forum: "one skill call another" works as an ordinary instruction (V/I) |
| Shared files outside the skill dir | not forbidden; `${CLAUDE_SKILL_DIR}` exists (V); relative paths resolve from cwd (I) | prompt tells the model to resolve relative to the `SKILL.md` directory (V); sandbox does not block reads (V) | relative from skill root (V); reads any path (I) |
| Auto-update of plain skills | **`SessionStart` hook with `git pull` + `reloadSkills: true` is the official example** (V); file-watch reload within a session (V) | none; watcher sees file changes (V) | none; no cloud sync (V) |
| Plugins / marketplace | `.claude-plugin/marketplace.json`; `autoUpdate` per marketplace (off for non-Anthropic), check <= 10 min after the first message, applied at next start (V) | `.agents/plugins/marketplace.json`; **all git marketplaces auto-upgrade at app-server start** (V, code); not in the IDE extension (V) | git marketplace; auto-refresh Teams only; `enabled_plugins` -> local folders (V) |

Machine-specific notes:
- `~/.codex/skills/tracker-issues -> ~/.agents/skills/tracker-issues` -> duplicate in Codex (both roots scanned). Remove.
- `~/.codex/skills/arc-finder` is a real directory; `~/.claude/skills/arc-finder` points at it.
- `~/.claude/skills/use-yandex` **and** `~/.agents/skills/use-yandex` are both real directories; the Claude copy is newer (WebSocket transport, 2026-09-09).
- `~/.claude/skills/write-a-skill` - dangling symlink (in the lock file, no directory).
- `~/.claude/skills/{memory-hygiene,test-tracker-release}` - real directories, Claude only.
- Cursor sees both `~/.agents/skills/x` and `~/.claude/skills/x` (compat) -> duplicates; cured by turning off "Include Third-Party Plugins, Skills, and Other Configs" in the IDE (IDE-only setting, V), after which Cursor reads only `~/.agents/skills` + `~/.cursor/skills`. No CLI setting; rely on dedup in 3.17+.
- 10 of the 12 own skills carry `disable-model-invocation: true`. Codex ignores it -> they are implicitly invocable there. Needs `agents/openai.yaml` with `allow_implicit_invocation: false` (generate from the frontmatter).

## 2. Reliability of skill composition - evidence

- Anthropic best practices: nothing about invoking a skill from a skill; only "split into reference files", "keep references one level deep", "Claude may partially read files referenced from other referenced files" (V).
- Claude Code troubleshooting: "Claude skipped a rule that must hold every time: move the rule into a hook" (V). After compaction the first 5000 tokens of each skill survive, 25000 in total (V).
- Empirics: an instruction "Use Skill(research)" injected through a UserPromptSubmit hook fires 4-5 times out of 10 ("coin flip", scottspence.com, V). A directive description ("ALWAYS invoke... Do not X directly") raises activation 37% -> 100% (Seleznov, 650 runs; I - from a snippet), but "does the model follow the steps inside" is a separate problem (Bara, I).
- The agentskills.io spec says nothing about cross-skill references; issue #137 unanswered; discussion #374 recommends "read from a (shared) external source" or "a build process that syncs information into the skill directories" (V).
- How others do it (V): mattpocock extracts the shared procedure into a model-invoked skill `grilling` with the rule "a user-invoked skill may invoke model-invoked skills, never another user-invoked one"; obra/superpowers holds its chains together with a SessionStart hook injecting `using-superpowers` ("you ABSOLUTELY MUST invoke the skill") plus `**REQUIRED SUB-SKILL:**` markers; there are no deterministic transitions between skills. sci-brain#50: `../shared` breaks under per-skill installs -> "Turn `_shared` into a skill".

Ranking by reliability (I):
1. Hooks / preload (`skills:` on a subagent, `hooks:` in a skill's frontmatter) - the only deterministic option. Claude only; Codex and Cursor have their own `hooks.json` but without skill scope.
2. Inlining the critical steps at the top of `SKILL.md`.
3. "Read `<path>` and follow it" pointing at a shared file - works in all three; reliability = ordinary instruction following, improved by imperative phrasing, a checklist and one level of nesting.
4. "Invoke skill X" - Claude-only, with open bugs. A supplementary signal at most, not a mechanism.

## 3. Architecture considered on this day (symlink variant)

```
euphotic/
├── skills/<name>/SKILL.md (+ agents/openai.yaml generated, references/, scripts/)
├── skills/_shared/                  # not a skill (no SKILL.md!); shared procedures/templates
├── bin/link      # idempotent symlinks ~/.agents/skills/<name>, ~/.claude/skills/<name> -> repo; cleans dangling ones
├── bin/build     # generates agents/openai.yaml
├── bin/check     # lint
├── bin/install   # bootstrap on a new machine: clone -> link -> launchd -> third-party skills via npx skills
├── launchd/com.vanilla-wave.euphotic-sync.plist   # hourly: git pull --ff-only && bin/link
└── third-party.txt
```

Why this shape: per-skill symlinks rather than symlinking the whole `~/.claude/skills` (documented for Claude; `npx skills` conflicts with a symlinked directory, vercel#209); `_shared` physically under `skills/` plus sibling symlinks so `../_shared/x.md` resolves both through the symlink path and the real path; nothing in `~/.codex/skills` because Codex reads `~/.agents/skills` itself.

Deferred alternative: the repo as a marketplace for all three. Pros - "login" with one command, auto-upgrade in Claude (`autoUpdate`) and Codex (at start). Cons - three cached copies instead of live edits, `/plugin:skill` in Claude, Cursor auto-update Teams only, Codex plugins not in the IDE, duplicates if `~/.agents/skills` and the plugin are both connected. Chosen the next day anyway, because portability mattered more than live edits.

## 4. Migration from the current state

1. Move the 12 own skills out of `~/.agents/skills/*` (not in the lock file) plus `arc-finder`, `memory-hygiene`, `test-tracker-release`; reconcile the two `use-yandex` copies.
2. Remove `~/.codex/skills/{arc-finder,tracker-issues}` and the dangling `write-a-skill`.
3. Generate `agents/openai.yaml` for the dmi skills.
4. Cursor IDE: turn off third-party imports (kills duplicates); verify with `agent` -> `/skills`.
5. Verify: `claude` -> `/context` (Skills row), `codex` -> `/skills`, `agent` -> `/skills` - one instance of each.

## 5. Open questions at the time

- Multiple machines? If not, auto-update is a no-op.
- Vendor third-party skills (mattpocock) into the repo, dotfiles-style, or keep them under `npx skills` (lock + `skills update -g -y` on a schedule; bug #162 - for **private** GitHub repos the hash is empty and update does not work)? Recommendation: keep under `npx skills`.
- Work skills kept in another repo are a separate source.
- Publishing (marketplace manifests) now or later?

## 6. Sources

Claude Code: code.claude.com/docs/en/{skills,hooks,settings-reference,env-vars,memory,sub-agents,plugins/*}; platform.claude.com best-practices; CHANGELOG; issues anthropics/claude-code #31005, #17351, #29191, #79560, #86763, #56912, #77795, #64606, #19308.
Codex: learn.chatgpt.com/docs/{build-skills,plugins,build-plugins,config-file/config-reference,developer-commands,sandboxing}; developers.openai.com/plugins; openai/codex `codex-rs/ext/skills/src/*`, `core-plugins/src/manager.rs`; issues #22275, #31592, #11314, #23454, #32169, #44172, #23987, #39421, #22078, #5237; openai/skills #420.
Cursor: cursor.com/docs/{skills,context/rules,plugins,reference/plugins,reference/third-party-hooks,cli/*}; changelog 2.4/2.5; forum 149017, 149693, 156001, 156577, 157274, 160677, 161142, 163569, 166056, 169575.
Standard / tooling: agentskills.io/specification; agentskills issues #137, discussion #374; vercel-labs/skills README/AGENTS.md/src/cli.ts, issues #162, #209, #386; mattpocock/skills; obra/superpowers (hooks.json, using-superpowers, writing-skills, RELEASE-NOTES); MattKay02/skills#1; sci-brain#50; copilot-cli#1090; scottspence.com; dev.to/rulestack; runkids/skillshare; skilz-cli; chezmoi/stow examples.

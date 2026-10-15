# A portable skills plugin: formats and connection (Claude Code / Codex / Cursor)

Date: 2026-10-16. Follow-up to `2026-10-15-skills-infra.md` after the goal was sharpened: not symlinks but a **portable plugin** connected with one command. **V** - verified (docs / source / reference repos), **I** - inferred.

## Decision

The repo is the marketplace and the single plugin `euphotic`. One set of `skills/<name>/SKILL.md`, manifests for the three agents next to it, no `version` anywhere - every agent tracks the commit SHA, pushing is releasing.

## Formats (V)

| | Claude Code | Codex | Cursor |
|---|---|---|---|
| Plugin manifest | `.claude-plugin/plugin.json` (optional, only `name` required) | root `plugin.json` per the open standard agent-plugins.org (TSC: Amazon, Cursor, Microsoft, OpenAI, Vercel); fallback order in source: `.codex-plugin/plugin.json` -> `.claude-plugin/plugin.json` -> `.cursor-plugin/plugin.json` | `.cursor-plugin/plugin.json` (only `name`) **or** root `plugin.json`; `cursor/plugins/origin-apps` ships all three at once |
| Marketplace | `.claude-plugin/marketplace.json`; `source: "./"` = repo root ("`"."` on its own means the root itself") | `.agents/plugins/marketplace.json`, `source: {local, path: "./"}` (source: `"." \| "./"` -> root); fallback order: `.agents/plugins/api_marketplace.json` -> `.claude-plugin/marketplace.json` -> `.cursor-plugin/marketplace.json` | `.cursor-plugin/marketplace.json`; `source: "<subdir>"`; **`"."` is undocumented** - verify with a real install |
| Skills | default scan of `skills/` (one level); `skills: [...]` adds directories | `skills/` automatically (the standard forbids a `skills` field in the root `plugin.json`); recursive | `"skills": "./skills/"` or default; the field replaces auto-discovery |
| Namespace | `/euphotic:foo`; **bare `/foo` works when the name is free** (skills.md) | `$euphotic:foo` (source `namespace.rs`) | `/foo` |
| Per-skill metadata | the whole frontmatter | `agents/openai.yaml`: `interface.{display_name,short_description}`, `policy.allow_implicit_invocation` | frontmatter `disable-model-invocation`, `paths` |
| Hooks | `hooks/hooks.json` (PascalCase events, `${CLAUDE_PLUGIN_ROOT}`) | `extensions.com.openai.hooks` in the root `plugin.json`; hook plugins are excluded from the directory | `hooks/hooks.json` `{version:1, hooks:{sessionStart:[...]}}` (camelCase, `${CURSOR_PLUGIN_ROOT}`) - formats are incompatible, superpowers keeps separate files |

## Connecting and updating (V)

| | Install | Zero-command | Auto-update | Private repo |
|---|---|---|---|---|
| Claude | `claude plugin marketplace add owner/repo` + `claude plugin install euphotic@euphotic` (since 2.1.292: `claude plugin install euphotic --marketplace owner/repo`) | `settings.json`: `extraKnownMarketplaces.euphotic{source:{github,repo},autoUpdate:true}` + `enabledPlugins["euphotic@euphotic"]=true` -> clones and installs at start | off by default for third-party; `autoUpdate: true` -> check <= 10 min after the first message, applied at the next start / `/reload-plugins` | git credentials (`gh auth setup-git`), SSH agent; `owner/repo` tries SSH, then HTTPS |
| Codex | `codex plugin marketplace add owner/repo` + `codex plugin add euphotic@euphotic` -> writes `[plugins."euphotic@euphotic"] enabled=true` | `[marketplaces.euphotic] source_type="git" source=...` + `[plugins...]` (auto-install after pasting - I) | **all git marketplaces upgrade at app-server start** (CLI + desktop, PR #17425); `--ref` pins | SSH/HTTPS URLs accepted, system git credentials (I) |
| Cursor | `agent plugin marketplace add <git url>` -> install only interactively via `/plugin` (no `install` in the CLI, forum 2026-07) | `enabled_plugins` in `~/.cursor/settings.json` -> local folders (schema undocumented) | **none** for personal marketplaces (Teams: Auto Refresh via the GitHub App); `agent plugin marketplace update` goes through Cursor's servers, bug "0 plugins indexed" (2026-08) | `add` reads locally, `update` from Cursor's servers -> private / self-hosted break; fallback - clone into `~/.cursor/plugins/local/<name>` (symlinks pointing outside are ignored) |

The Codex IDE extension does not support plugins (V) - only `~/.agents/skills` there.

## Reference repos (V)

- **obra/superpowers**: `.claude-plugin/{plugin,marketplace}.json` (`source:"./"`), `.codex-plugin/plugin.json` (`skills:"./skills/"`, `hooks:{}`), `.cursor-plugin/plugin.json` (`hooks:"./hooks/hooks-cursor.json"`), `.agents/plugins/marketplace.json` (`source:{url:"./"}`), plus Antigravity/Devin/Kimi/Hermes/OpenCode/Pi/Gemini. Internal references are `superpowers:<skill>` with `**REQUIRED SUB-SKILL:**`; no Codex hooks, bootstrap through the `using-superpowers` description. RELEASE-NOTES: "Codex no longer ships a SessionStart hook", "Codex marketplace sources expect a .agents/plugins/marketplace.json".
- **mattpocock/skills**: only `.claude-plugin/{plugin,marketplace}.json` plus `agents/openai.yaml` beside every SKILL.md; Codex installs through the `.claude-plugin` fallback; "Codex still drops symlinks". Cursor via `npx skills add` (copies). The shared procedure `grilling` is a separate model-invoked skill; `grill-me` is literally "Call the Skill tool with "grilling"".
- **anthropics/skills**: one `skills/` -> several plugins via `plugins[].skills: [...]` in marketplace.json (Claude-only trick).
- **openai/plugins**: `.agents/plugins/marketplace.json` + `plugins/<name>/.codex-plugin/plugin.json`; the new direction is a root `plugin.json` + `extensions.com.openai`.
- **cursor/plugins**: `.cursor-plugin/marketplace.json` with `source:"<subdir>"`; `origin-apps` carries root `plugin.json` + `.cursor-plugin` + `.claude-plugin`.

## Empirical results in this session

- Claude Code injects a plugin skill's content with the first line `Base directory for this skill: <abs path>` -> relative `../../shared/...` references are resolvable. Codex: the prompt tells the model to resolve relative to the `SKILL.md` directory; Cursor: "relative paths from the skill root".
- In the Codex cache of superpowers (`openai-curated`) the skills carry `agents/openai.yaml`, the manifest is `.codex-plugin/plugin.json` with `"skills": "./skills/"`; the `openai-curated` marketplace snapshot uses `source:{local, path:"./plugins/<name>"}`.
- With real skills in the repo: `claude -p --plugin-dir .` listed `euphotic:<name>` for exactly the skills without `disable-model-invocation`; `codex plugin marketplace add <local path>` parsed `.agents/plugins/marketplace.json` + root `plugin.json` and showed `euphotic@euphotic`; `agent -p --plugin-dir .` (Cursor) loaded a probe skill that existed only in the plugin, and showed skills twice while the old copies still lived in `~/.agents/skills` / `~/.claude/skills`.

## Risks / things to verify at the first real install

1. Cursor `source: "."` in `.cursor-plugin/marketplace.json` is undocumented; if it fails -> a `plugins/euphotic/` subdirectory or a clone in `~/.cursor/plugins/local`.
2. Claude: `plugin.json` + `marketplace.json` in the same `.claude-plugin/` with `source:"./"` - superpowers and mattpocock do it; `claude plugin validate .` passes here; still confirm with a real install.
3. Codex zero-command (pasting into config.toml without `codex plugin add`) - auto-install is undocumented.
4. Renaming the marketplace breaks the `[plugins."x@mkt"]` key in Codex (#51609) - the name `euphotic` is fixed.
5. Duplicates: while the plugin is installed **and** old copies remain in `~/.agents/skills` / `~/.claude/skills` / `~/.codex/skills`, every agent sees each skill twice. Remove the originals after installing.
6. Codex ignores `disable-model-invocation` -> `agents/openai.yaml` with `allow_implicit_invocation:false` is generated by `bin/build`; Cursor documents `user-invocable` only for the CLI.

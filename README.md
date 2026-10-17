# euphotic

Personal agent skills for Claude Code, Codex and Cursor, shipped as one plugin.
Named after the thin sunlit layer of the ocean where almost all the growth happens.

## Principles

- **Thin process layer.** The least process that keeps the agent reliably useful. One job per skill, no ceremony.
- **High agent autonomy.** The agent plans, picks tools and finishes. Humans are asked only at real decision points: irreversible actions, scope, taste.
- **Intent-driven.** Exact only about what the agent cannot derive (interfaces) and cannot undo (gates, hard limits). Everything else is intent: priorities, what good looks like, a completion criterion. Steps only where the order is forced.

The operative version is the `skill-creator` skill.

## Connect

The repo root is the marketplace and the single plugin `euphotic`. No versions: every agent tracks the commit SHA, pushing is releasing.

**Claude Code** - skills appear as `/euphotic:<name>` (bare `/<name>` when free).
```bash
claude plugin marketplace add vanilla-wave/euphotic
claude plugin install euphotic@euphotic
```
Auto-update is off for third-party marketplaces; this `~/.claude/settings.json` turns it on and installs on next start:
```json
{
  "extraKnownMarketplaces": { "euphotic": { "source": { "source": "github", "repo": "vanilla-wave/euphotic" }, "autoUpdate": true } },
  "enabledPlugins": { "euphotic@euphotic": true }
}
```

**Codex** (CLI and desktop; not the IDE extension) - skills appear as `$euphotic:<name>`. Git marketplaces refresh on every start.
```bash
codex plugin marketplace add vanilla-wave/euphotic
codex plugin add euphotic@euphotic
```

**Cursor** - skills appear as `/<name>`. No auto-refresh for personal marketplaces: `agent plugin marketplace update euphotic`.
```bash
agent plugin marketplace add https://github.com/vanilla-wave/euphotic   # then /plugins to install
```

**Without installing**: `claude --plugin-dir .`, `agent --plugin-dir .`, `codex plugin marketplace add .`

## Develop

- `AGENTS.md` - repo rules. `shared/CONVENTIONS.md` - packaging and the portable frontmatter subset.
- `bin/build` - regenerates `skills/*/agents/openai.yaml` (Codex metadata); commit the output.
- `bin/check` - lint + `claude plugin validate`.
- `docs/research/` - why it is built this way.

## Layout

```
plugin.json                        Agent Plugins manifest (Codex, Cursor)
.claude-plugin/                    Claude Code manifest + marketplace
.agents/plugins/marketplace.json   Codex marketplace
.cursor-plugin/                    Cursor manifest + marketplace
skills/<name>/SKILL.md             the skills
shared/                            material reused by several skills (not a skill)
bin/                               build, check
```

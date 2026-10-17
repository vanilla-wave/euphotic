# euphotic

Personal agent skills for Claude Code, Codex and Cursor, shipped as one plugin.

The euphotic zone is the thin, sunlit top layer of the ocean where almost all of the growth happens.
This repo is that layer for an agent: a thin layer on top of a capable model, not a process manual.

## Principles

- **Thin process layer.** The least process that keeps the agent reliably useful. One job per skill, no ceremony.
- **High agent autonomy.** The agent plans, picks tools and finishes. Humans are asked only at real decision points: irreversible actions, scope, taste.
- **Intent-driven.** Exact only about what the agent cannot derive (interfaces) and cannot undo (gates, hard limits). Everything else is intent: priorities, what good looks like, a completion criterion. Steps only where the order is forced.

The operative version is the `skill-creator` skill.

## Connect

The repo root is the marketplace and the single plugin `euphotic`. No versions: every agent tracks the commit SHA, pushing is releasing.

<details>
<summary><b>Claude Code</b> - skills appear as <code>/euphotic:&lt;name&gt;</code> (bare <code>/&lt;name&gt;</code> when free)</summary>

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
</details>

<details>
<summary><b>Codex</b> (CLI and desktop; not the IDE extension) - skills appear as <code>$euphotic:&lt;name&gt;</code></summary>

```bash
codex plugin marketplace add vanilla-wave/euphotic
codex plugin add euphotic@euphotic
```
Git marketplaces refresh on every start.
</details>

<details>
<summary><b>Cursor</b> - skills appear as <code>/&lt;name&gt;</code></summary>

```bash
agent plugin marketplace add https://github.com/vanilla-wave/euphotic   # then /plugins to install
```
No auto-refresh for personal marketplaces: `agent plugin marketplace update euphotic`.
</details>

<details>
<summary><b>Without installing</b></summary>

```bash
claude --plugin-dir .
agent  --plugin-dir .
codex plugin marketplace add .
```
</details>

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

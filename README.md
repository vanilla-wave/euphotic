# euphotic

Personal agent skills for **Claude Code**, **Codex** and **Cursor**, shipped as one plugin.

The euphotic zone is the thin, sunlit top layer of the ocean where almost all of the growth happens.
This repo is that layer for an agent: a thin layer on top of a capable model, not a process manual.

## Principles

**Thin process layer.** A skill adds the least process that still makes the agent reliably useful:
one job per skill, a short `SKILL.md`, no ceremonies, no stage gates, no reporting for its own sake.
A step that exists only to make a human feel in control does not survive review.

**High agent autonomy.** The agent is trusted to plan, pick tools and finish. Skills do not script
what a competent agent can work out, do not ask for confirmation mid-task, and state defaults so
work never stalls. Humans are pulled in at real decision points - irreversible actions, scope
changes, matters of taste - and nowhere else.

**Intent-driven.** A skill says what matters, not what to type: priorities in order, what "good"
looks like, the trade-offs to make under pressure, and the few things never to do. Everything else
is left to judgment.

## Connect

The repo root is both the marketplace and the single plugin `euphotic`. There is no `version`
anywhere on purpose: every agent tracks the commit SHA, so pushing is releasing.

### Claude Code
```bash
claude plugin marketplace add vanilla-wave/euphotic
claude plugin install euphotic@euphotic
```
Zero-command alternative that also enables auto-update (off by default for third-party
marketplaces) - add to `~/.claude/settings.json`:
```json
{
  "extraKnownMarketplaces": {
    "euphotic": { "source": { "source": "github", "repo": "vanilla-wave/euphotic" }, "autoUpdate": true }
  },
  "enabledPlugins": { "euphotic@euphotic": true }
}
```
Claude clones and installs on the next start, checks for updates after the first message of a
session and applies them on the following start (`/reload-plugins` applies immediately).
Skills appear as `/euphotic:<name>`; bare `/<name>` works when the name is free.

### Codex (CLI and desktop app; the IDE extension does not support plugins)
```bash
codex plugin marketplace add vanilla-wave/euphotic
codex plugin add euphotic@euphotic
```
Equivalent `~/.codex/config.toml`:
```toml
[marketplaces.euphotic]
source_type = "git"
source = "https://github.com/vanilla-wave/euphotic.git"

[plugins."euphotic@euphotic"]
enabled = true
```
Git marketplaces are refreshed automatically when Codex starts; `codex plugin marketplace upgrade
euphotic` forces it. Skills appear as `$euphotic:<name>`.

### Cursor (IDE and `agent` CLI)
```bash
agent plugin marketplace add https://github.com/vanilla-wave/euphotic
```
then `/plugin` -> Marketplace -> install `euphotic` (there is no non-interactive install yet).
Refresh with `agent plugin marketplace update euphotic`; personal marketplaces do not auto-refresh
(Teams only). If the repo is private and the update fails, clone it instead:
`git clone <url> ~/.cursor/plugins/local/euphotic` and `git pull` on a schedule.
Skills appear as `/<name>`.

### Try without installing
```bash
claude --plugin-dir /path/to/euphotic
agent  --plugin-dir /path/to/euphotic
codex plugin marketplace add /path/to/euphotic && codex plugin add euphotic@euphotic   # local, no auto-update
```

## Develop

- `AGENTS.md` - how skills are written here (the principles, applied).
- `shared/CONVENTIONS.md` - the portable frontmatter subset and how skills share procedures.
- `bin/build` - regenerates `skills/*/agents/openai.yaml` (Codex metadata) from frontmatter. Commit the output.
- `bin/check` - lint: frontmatter, portability, manifest consistency, `claude plugin validate`.
- `docs/research/` - why it is built this way.

## Layout
```
plugin.json                       Agent Plugins 1.0 manifest (Codex, Cursor)
.claude-plugin/plugin.json        Claude Code manifest
.claude-plugin/marketplace.json   Claude Code marketplace (Codex reads it as a fallback too)
.agents/plugins/marketplace.json  Codex marketplace
.cursor-plugin/plugin.json        Cursor manifest
.cursor-plugin/marketplace.json   Cursor marketplace
skills/<name>/SKILL.md            the skills (+ generated agents/openai.yaml)
shared/                           procedures, templates, scripts reused by several skills (not a skill)
bin/                              build + check
```

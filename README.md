# euphotic

Personal agent skills, packaged once as a plugin for **Claude Code**, **Codex** and **Cursor**.
The repo root is both the marketplace and the single plugin `euphotic`; the skills live in `skills/<name>/`.

## Connect

### Claude Code
```bash
claude plugin marketplace add vanilla-wave/euphotic
claude plugin install euphotic@euphotic
```
Zero-command alternative (also turns on auto-update, which is off by default for third-party marketplaces) - add to `~/.claude/settings.json`:
```json
{
  "extraKnownMarketplaces": {
    "euphotic": { "source": { "source": "github", "repo": "vanilla-wave/euphotic" }, "autoUpdate": true }
  },
  "enabledPlugins": { "euphotic@euphotic": true }
}
```
Claude clones and installs on the next start, checks for updates after the first message of a session and applies them on the following start (`/reload-plugins` applies immediately). Skills appear as `/euphotic:<name>`; bare `/<name>` works when the name is free.

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
Git marketplaces are refreshed automatically when Codex starts; `codex plugin marketplace upgrade euphotic` forces it. Skills appear as `$euphotic:<name>`.

### Cursor (IDE and `agent` CLI)
```bash
agent plugin marketplace add https://github.com/vanilla-wave/euphotic
```
then `/plugin` -> Marketplace -> install `euphotic` (there is no non-interactive install yet). Refresh with `agent plugin marketplace update euphotic`; personal marketplaces do not auto-refresh (Teams only).
If the repo is private and the marketplace update fails, clone it instead: `git clone <url> ~/.cursor/plugins/local/euphotic` and `git pull` on a schedule. Skills appear as `/<name>`.

### Try without installing
```bash
claude --plugin-dir /path/to/euphotic
agent  --plugin-dir /path/to/euphotic
codex plugin marketplace add /path/to/euphotic && codex plugin add euphotic@euphotic   # local marketplace, no auto-update
```

## Develop
- `shared/CONVENTIONS.md` - how skills are written here and how they share procedures.
- `bin/build` - regenerates `skills/*/agents/openai.yaml` (Codex metadata) from frontmatter. Commit the output.
- `bin/check` - lint: frontmatter, portability, manifest consistency, `claude plugin validate`.
- No `version` field anywhere on purpose: every agent tracks the commit SHA, so pushing is releasing.

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
docs/research/                    why it is built this way
```

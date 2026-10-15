# Переносимый плагин со скиллами: форматы и подключение (Claude Code / Codex / Cursor)

Дата: 2026-10-16. Продолжение `2026-10-15-skills-infra.md` после уточнения цели: не симлинки, а **переносимый плагин**, подключаемый одной командой. **V** — проверено (доки/исходники/эталонные репы), **I** — вывод.

## Решение

Репа = маркетплейс = единственный плагин `euphotic`. Один набор `skills/<name>/SKILL.md`, манифесты для трёх агентов рядом, `version` нигде не указан — все агенты трекают commit SHA, push = релиз.

## Форматы (V)

| | Claude Code | Codex | Cursor |
|---|---|---|---|
| Манифест плагина | `.claude-plugin/plugin.json` (опц., нужен только `name`) | root `plugin.json` по open-стандарту agent-plugins.org (TSC: Amazon, Cursor, Microsoft, OpenAI, Vercel); fallback-порядок в коде: `.codex-plugin/plugin.json` → `.claude-plugin/plugin.json` → `.cursor-plugin/plugin.json` | `.cursor-plugin/plugin.json` (только `name`) **или** root `plugin.json`; `cursor/plugins/origin-apps` шипит все три сразу |
| Маркетплейс | `.claude-plugin/marketplace.json`; `source: "./"` = корень репы («`"."` on its own means the root itself») | `.agents/plugins/marketplace.json`, `source: {local, path: "./"}` (код: `"." \| "./"` → корень); fallback-порядок: `.agents/plugins/api_marketplace.json` → `.claude-plugin/marketplace.json` → `.cursor-plugin/marketplace.json` | `.cursor-plugin/marketplace.json`; `source: "<subdir>"`; **`"."` не документирован** — проверить реальной установкой |
| Скиллы | дефолтный скан `skills/` (один уровень); `skills: [...]` добавляет каталоги | `skills/` авто (стандарт запрещает поле `skills` в root plugin.json); рекурсивно | `"skills": "./skills/"` или дефолт; поле заменяет авто-скан |
| Namespace | `/euphotic:foo`; **bare `/foo` работает, если имя свободно** (skills.md) | `$euphotic:foo` (код `namespace.rs`) | `/foo` |
| Per-skill метаданные | frontmatter целиком | `agents/openai.yaml`: `interface.{display_name,short_description}`, `policy.allow_implicit_invocation` | frontmatter `disable-model-invocation`, `paths` |
| Хуки | `hooks/hooks.json` (PascalCase события, `${CLAUDE_PLUGIN_ROOT}`) | `extensions.com.openai.hooks` в root plugin.json; хук-плагины не попадают в каталог | `hooks/hooks.json` `{version:1, hooks:{sessionStart:[…]}}` (camelCase, `${CURSOR_PLUGIN_ROOT}`) — форматы несовместимы, superpowers держит отдельные файлы |

## Подключение и обновление (V)

| | Установка | Zero-command | Автообновление | Private repo |
|---|---|---|---|---|
| Claude | `claude plugin marketplace add owner/repo` + `claude plugin install euphotic@euphotic` (с 2.1.292: `claude plugin install euphotic --marketplace owner/repo`) | `settings.json`: `extraKnownMarketplaces.euphotic{source:{github,repo},autoUpdate:true}` + `enabledPlugins["euphotic@euphotic"]=true` → клонирует и ставит при старте | off по умолчанию для сторонних; `autoUpdate: true` → проверка через ≤10 мин после первого сообщения, применение на следующем старте / `/reload-plugins` | git-креды (`gh auth setup-git`), SSH-агент; `owner/repo` пробует SSH, потом HTTPS |
| Codex | `codex plugin marketplace add owner/repo` + `codex plugin add euphotic@euphotic` → пишет `[plugins."euphotic@euphotic"] enabled=true` | `[marketplaces.euphotic] source_type="git" source=…` + `[plugins…]` (автоустановка после вставки — I) | **все git-маркетплейсы апгрейдятся на старте app-server** (CLI + desktop, PR #17425); `--ref` пинит | SSH/HTTPS URL принимаются, системные git-креды (I) |
| Cursor | `agent plugin marketplace add <git url>` → установка только интерактивно `/plugin` (нет `install` в CLI, форум 07.2026) | `enabled_plugins` в `~/.cursor/settings.json` → локальные папки (схема не документирована) | **нет** для личных маркетплейсов (Teams: Auto Refresh через GitHub App); `agent plugin marketplace update` — через серверы Cursor, баг «0 plugins indexed» (08.2026) | `add` читает локально, `update` — с серверов Cursor → private/self-hosted ломаются; fallback — клон в `~/.cursor/plugins/local/<name>` (симлинки наружу игнорируются) |

Codex IDE-расширение плагины не поддерживает (V) — там только `~/.agents/skills`.

## Эталоны (V)

- **obra/superpowers**: `.claude-plugin/{plugin,marketplace}.json` (`source:"./"`), `.codex-plugin/plugin.json` (`skills:"./skills/"`, `hooks:{}`), `.cursor-plugin/plugin.json` (`hooks:"./hooks/hooks-cursor.json"`), `.agents/plugins/marketplace.json` (`source:{url:"./"}`), + Antigravity/Devin/Kimi/Hermes/OpenCode/Pi/Gemini. Внутренние ссылки — `superpowers:<skill>` + `**REQUIRED SUB-SKILL:**`; Codex-хуков нет, бутстрап через description `using-superpowers`. RELEASE-NOTES: «Codex no longer ships a SessionStart hook», «Codex marketplace sources expect a .agents/plugins/marketplace.json».
- **mattpocock/skills**: только `.claude-plugin/{plugin,marketplace}.json` + `agents/openai.yaml` рядом с каждым SKILL.md; Codex ставится через fallback на `.claude-plugin`; «Codex still drops symlinks». Cursor — через `npx skills add` (копии). Общая процедура `grilling` = отдельный model-invoked скилл, `grill-me` целиком: «Call the Skill tool with "grilling"».
- **anthropics/skills**: один `skills/` → несколько плагинов через `plugins[].skills: [...]` в marketplace.json (Claude-only приём).
- **openai/plugins**: `.agents/plugins/marketplace.json` + `plugins/<name>/.codex-plugin/plugin.json`; новое направление — root `plugin.json` + `extensions.com.openai`.
- **cursor/plugins**: `.cursor-plugin/marketplace.json` с `source:"<subdir>"`; `origin-apps` несёт root `plugin.json` + `.cursor-plugin` + `.claude-plugin`.

## Эмпирика в этой сессии

- Claude Code инжектит контент скилла из плагина с первой строкой `Base directory for this skill: <abs path>` → относительные ссылки `../../shared/…` резолвимы. Codex: промпт велит резолвить относительно каталога SKILL.md; Cursor: «relative paths from the skill root».
- В Codex-кэше superpowers (`openai-curated`) скиллы несут `agents/openai.yaml`, манифест `.codex-plugin/plugin.json` с `"skills": "./skills/"`; маркетплейс-снимок `openai-curated` — `source:{local, path:"./plugins/<name>"}`.

## Риски / что проверить при первой установке

1. Cursor `source: "."` в `.cursor-plugin/marketplace.json` — не документирован; если не взлетит → `plugins/euphotic/` подкаталог или `~/.cursor/plugins/local` клон.
2. Claude: комбинация `plugin.json` + `marketplace.json` в одном `.claude-plugin/` с `source:"./"` — так делают superpowers и mattpocock, но прогнать `claude plugin validate .` и реальный install.
3. Codex zero-command (вставка в config.toml без `codex plugin add`) — автоустановка не задокументирована.
4. Переименование маркетплейса ломает ключ `[plugins."x@mkt"]` в Codex (#51609) — имя `euphotic` фиксируем.
5. Дубли: пока плагин установлен **и** старые копии лежат в `~/.agents/skills` / `~/.claude/skills` / `~/.codex/skills`, каждый агент видит скилл дважды. После установки оригиналы снести.
6. Codex игнорирует `disable-model-invocation` → `agents/openai.yaml` с `allow_implicit_invocation:false` генерируется `bin/build`; у Cursor `user-invocable` поддержан только в CLI-доках.

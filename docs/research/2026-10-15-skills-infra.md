# Личное хранилище скиллов для Claude Code / Codex / Cursor — ресерч и план

Дата: 2026-10-15. Версии на машине: Claude Code 2.1.300, codex-cli 0.162.1, cursor `agent` 2026.10.01, `skills` CLI 1.5.9.
Источники: 4 параллельных ресерча по официальным докам + исходникам Codex + GitHub issues; полный список URL в конце.
Легенда: **V** — проверено по докам/коду, **I** — вывод.

## 0. TL;DR

1. Канонический корень уже существует — `~/.agents/skills`. Его читают **нативно Codex и Cursor**, но **не Claude Code** (issue #31005 открыт). Claude нужны per-skill симлинки в `~/.claude/skills/<name>` — это документировано и дедуп-безопасно (V).
2. Репа `euphotic` = источник: `skills/<name>/` + `skills/_shared/` + `bin/link|build|check` + launchd-плист для `git pull`. `~/.agents/skills/<name>` и `~/.claude/skills/<name>` — симлинки на репу. Сторонние скиллы (mattpocock) остаются под `npx skills` рядом, в репу не тащим.
3. Автообновление: launchd `git pull --ff-only` раз в час + (опц.) Claude `SessionStart`-хук с `git pull` и `reloadSkills: true` — это **официальный пример из доков хуков** (V). Codex/Cursor подхватывают изменения файлов сами (watcher / при старте).
4. **Опасение про «приватные скиллы» оправдано, но решение — не делать их скиллами.** Общие процедуры = обычные файлы в `skills/_shared/`, которые родительский SKILL.md велит прочитать. Во всех трёх агентах это одинаково работает, не ест бюджет листинга и не светится в `/`. Вызов `Skill(X)` из скилла — механизм только Claude Code, и он сломан (#17351, #56912). Must-always правила → хуки, не текст.
5. Плагин-маркетплейсы умеют все три CLI, но для личного репо это лишний слой (кэш-копии, namespacing `/plugin:skill`, Cursor-автообновление только на Teams). Добавить манифесты позже, если захочется шарить с другими.

## 1. Факты по агентам

| | Claude Code | Codex | Cursor |
|---|---|---|---|
| User-корни | `~/.claude/skills/<name>` (V). **`~/.agents/skills` не читает** (V, #31005) | `~/.agents/skills` (V), `~/.codex/skills` — deprecated, но сканируется (V, код) | `~/.agents/skills`, `~/.cursor/skills` + compat `~/.claude/skills`, `~/.codex/skills` (V) |
| Вложенность | только `<root>/<name>/SKILL.md`, один уровень (V/I) | рекурсия до глубины 6, **любой** SKILL.md на любой глубине = скилл (V, код) | рекурсивно (V) |
| Симлинки | per-skill симлинк документирован; один таргет → грузится один раз (V) | симлинк-каталог следуется; симлинк на *файл* SKILL.md — нет (#31592); **дедупа по realpath нет** → симлинк + оригинал = дубль в пикере (V) | следуются (IDE с 2.5, CLI с 2026.06.22); дубли схлопываются с 3.17, приоритет не определён (V) |
| Frontmatter | полный набор: `disable-model-invocation`, `user-invocable`, `allowed-tools`, `context: fork`, `agent`, `hooks`, `paths`, `argument-hint`… (V) | только `name`, `description`; **`disable-model-invocation` игнорирует** (V/I). Скрытие — `agents/openai.yaml` → `policy.allow_implicit_invocation: false` (V) | `name`, `description`, `paths`, `disable-model-invocation`, `user-invocable` (V); остальное игнорирует (I) |
| Что видит модель | листинг name+description; бюджет **1% контекста** (`skillListingBudgetFraction`, env `SLASH_COMMAND_TOOL_CHAR_BUDGET`); description ≤1536 (V) | `## Skills` список `- name: description (file: path)`; бюджет 2% контекста, cap 10000 chars (V) | `<available_skills>` в system prompt (V) |
| Скрытый от модели | `disable-model-invocation: true` → описания нет в контексте, **и из другого скилла не вызвать** (V, явная ошибка). `user-invocable: false` → модель может, юзер в `/` не видит, бюджет ест (V) | `allow_implicit_invocation: false` → скилла нет в промпте вообще; только `$name` от юзера (V; баг #23454) | `disable-model-invocation: true` — только `/name` (V) |
| Механизм вызова | Skill tool, содержимое SKILL.md инжектится одним сообщением (V) | **Skill tool нет**: модель сама читает SKILL.md по пути из каталога; `$name` от юзера инжектит содержимое (V, код) | `/name` прикрепляет к сообщению; авто — по description (V) |
| Скилл→скилл | возможно через Skill tool, но: #17351 (open с 01.2026) — родительский воркфлоу **не продолжается** после вложенного вызова; #56912 — `allowed-tools: Skill(child)` игнорируется → permission prompt; #77795 — из сабагента dmi-скиллы невидимы (V) | механизма нет; `$other` в теле SKILL.md как mention **не парсится** (I, код парсера) → модель просто читает файл по пути. Для hidden-скилла пути в каталоге нет → указывать явно | не документировано; форум: «one skill call another» работает как обычная инструкция (V/I) |
| Shared-файлы вне папки | не запрещено; `${CLAUDE_SKILL_DIR}` есть (V); относительные пути резолвятся от cwd (I) | промпт велит резолвить относительно каталога SKILL.md (V); sandbox чтение не блокирует (V) | relative from skill root (V); чтение любых путей (I) |
| Автообновление «сырых» скиллов | **SessionStart-хук `git pull` + `reloadSkills: true` — официальный пример** (V); file-watch reload в сессии (V) | нет; watcher видит изменения файлов (V) | нет; cloud sync нет (V) |
| Плагины/маркетплейс | `.claude-plugin/marketplace.json`; autoUpdate per marketplace (off для не-Anthropic), проверка через ≤10 мин после 1-го сообщения, применяется на след. запуске (V) | `.agents/plugins/marketplace.json`; **все git-маркетплейсы автоапгрейдятся на старте app-server** (V, код); не в IDE-расширении (V) | git-маркетплейс; авторефреш только Teams; `enabled_plugins` → локальные папки (V) |

Нюансы по текущей машине:
- `~/.codex/skills/tracker-issues -> ~/.agents/skills/tracker-issues` → дубль в Codex (оба корня сканируются). Снести.
- `~/.codex/skills/arc-finder` — реальная папка, `~/.claude/skills/arc-finder` → на неё. Переехать в репу.
- `~/.claude/skills/use-yandex` — реальная папка, **и** `~/.agents/skills/use-yandex` — реальная папка. Две копии, возможно разъехались. Сверить, оставить одну.
- `~/.claude/skills/write-a-skill` — битый симлинк (в lock есть, папки нет). Снести.
- `~/.claude/skills/{memory-hygiene,test-tracker-release}` — реальные папки только в Claude. Переехать в репу.
- Cursor видит и `~/.agents/skills/x`, и `~/.claude/skills/x` (compat) → дубли; лечится выключением «Include Third-Party Plugins, Skills, and Other Configs» в IDE (IDE-only настройка, V) — тогда Cursor читает только `~/.agents/skills` + `~/.cursor/skills`. В CLI настройки нет, полагаемся на дедуп 3.17+ (проверить `agent` → `/skills`).
- Из твоих 12 собственных скиллов 10 — `disable-model-invocation: true`. В Codex это игнорируется → они там implicit-invocable. Нужен `agents/openai.yaml` с `allow_implicit_invocation: false` (генерировать из frontmatter).

## 2. Надёжность композиции скиллов — evidence

- Anthropic best practices: ни слова о вызове скилла из скилла; только «split into reference files», «keep references one level deep», «Claude may partially read files referenced from other referenced files» (V).
- Claude Code troubleshooting: «Claude skipped a rule that must hold every time: move the rule into a hook» (V). После compaction от каждого скилла остаются первые 5000 токенов, суммарно 25000 (V).
- Эмпирика: инструкция «Use Skill(research)» через UserPromptSubmit-хук срабатывает 4–5/10 («coin flip», scottspence.com, V). Directive-формулировка в description («ALWAYS invoke… Do not X directly») поднимает активацию 37%→100% (Seleznov, 650 прогонов; I — по сниппету), но «следует ли модель шагам внутри» — отдельная проблема (Bara, I).
- agentskills.io spec про межскилловые ссылки молчит; issue #137 без ответа; discussion #374 — рекомендации: «read from a (shared) external source» или «build process that syncs information into the skill directories» (V).
- Как делают другие (V): mattpocock — общая процедура вынесена в model-invoked скилл `grilling`, правило «user-invoked may invoke model-invoked, never another user-invoked»; obra/superpowers — цепочка держится на SessionStart-хуке, инжектящем `using-superpowers` с «you ABSOLUTELY MUST invoke the skill», и маркерах `**REQUIRED SUB-SKILL:**`; детерминированных переходов между скиллами нет. sci-brain#50: `../shared` ломается при per-skill установке → «Turn `_shared` into a skill».

Ранжирование по надёжности (I):
1. Хуки / preload (`skills:` у сабагента, `hooks:` во frontmatter скилла) — единственное детерминированное. Claude only; у Codex и Cursor свои `hooks.json`, но без skill-scope.
2. Инлайн критичных шагов в начале SKILL.md.
3. «Read `<path>` and follow it» на shared-файл — работает во всех трёх; надёжность = обычный instruction-following, усиливается императивом + чек-листом + одним уровнем вложенности.
4. «Invoke skill X» — Claude-only, с открытыми багами. Только как дополнительный сигнал, не как механизм.

## 3. Рекомендуемая архитектура

```
euphotic/
├── skills/
│   ├── <name>/
│   │   ├── SKILL.md              # кросс-агентный frontmatter: name, description (+ dmi/user-invocable для Claude/Cursor)
│   │   ├── agents/openai.yaml    # ГЕНЕРИРУЕТСЯ bin/build из frontmatter (allow_implicit_invocation ← dmi)
│   │   ├── references/           # приватные материалы скилла
│   │   └── scripts/
│   └── _shared/                  # НЕ скилл (без SKILL.md!), общие процедуры/шаблоны
│       └── procedures/<x>.md
├── bin/
│   ├── link      # идемпотентно: ~/.agents/skills/<name> и ~/.claude/skills/<name> → симлинки на repo/skills/<name>; + _shared; чистит битые
│   ├── build     # генерит agents/openai.yaml; (опц.) копирует _shared → references/_shared для публикуемых скиллов
│   ├── check     # name == dir, desc ≤1024, SKILL.md <500 строк, frontmatter-портабельность, нет SKILL.md под _shared/references, `claude plugin validate`
│   └── install   # bootstrap на новой машине: clone → link → launchd → `npx skills add -g` сторонних из third-party.txt
├── launchd/com.vanilla-wave.euphotic-sync.plist   # раз в час: git pull --ff-only && bin/link
├── third-party.txt               # список сторонних источников для npx skills (lock остаётся в ~/.agents/.skill-lock.json)
├── docs/research/
└── (позже) .claude-plugin/marketplace.json, .agents/plugins/marketplace.json — для шаринга наружу
```

Почему так:
- `_shared` физически под `skills/`, плюс симлинки `~/.agents/skills/_shared` и `~/.claude/skills/_shared` → ссылка `../_shared/procedures/x.md` из SKILL.md резолвится и по пути симлинка, и по realpath. Codex/Cursor каталог без SKILL.md игнорируют.
- Per-skill симлинки, а не симлинк всего `~/.claude/skills`: документировано для Claude; `npx skills` конфликтует с симлинком целого каталога (vercel#209); сторонние скиллы продолжают жить как реальные папки рядом.
- Codex читает `~/.agents/skills` сам → никаких симлинков в `~/.codex/skills`; существующие снести.

Правила для скиллов (кладём в `_shared/conventions.md` + проверяем в `bin/check`):
- Общая процедура = файл в `_shared/`, в SKILL.md: `REQUIRED before step N: read ../_shared/procedures/x.md and follow it`. Один уровень вложенности, ссылка в первых ~100 строках.
- Критичные шаги — инлайном наверху SKILL.md.
- Правило «всегда» — в хук (`hooks:` во frontmatter для Claude; для Codex/Cursor — их `hooks.json`, если реально нужно).
- `disable-model-invocation: true` ⇒ в Claude скилл нельзя вызвать из другого скилла. Если нужна модельная авто-активация + вызов из других — `user-invocable: false` (помнить: ест бюджет листинга).
- `Skill(X)` из скилла — не использовать как механизм.
- Имена: lowercase-дефис, без точек (Codex-парсер), description с триггерными словами в начале, ≤1024.

Автообновление:
- launchd каждый час: `git -C ~/gh/euphotic pull --ff-only --quiet && ~/gh/euphotic/bin/link`.
- Claude `~/.claude/settings.json` → `SessionStart` (`startup|resume`) хук: тот же pull + `{"hookSpecificOutput":{"hookEventName":"SessionStart","reloadSkills":true}}` (официальный пример).
- На машине, где редактируешь — обновлять нечего, правки видны сразу (Claude/Codex file-watch; Cursor — при старте).
- Листинг-бюджет Claude: следить `/context` → строка Skills; при обрезке `skillListingBudgetFraction: 0.02–0.05` в settings.json.

Альтернатива (отложена): репо как маркетплейс для всех трёх. Плюсы — «логин» одной командой, автоапгрейд у Claude (autoUpdate) и Codex (на старте). Минусы — три кэш-копии вместо live-правок, `/plugin:skill` в Claude, Cursor-автообновление только Teams, Codex-плагины не в IDE, при одновременном подключении `~/.agents/skills` и плагина — дубли. Имеет смысл только для публикации.

## 4. Миграция из текущего состояния

1. `git mv`-стиль: перенести 12 собственных из `~/.agents/skills/*` (не в lock) + `arc-finder`, `memory-hygiene`, `test-tracker-release` из Claude/Codex в `euphotic/skills/`. `use-yandex` — сверить 2 копии.
2. `bin/link` → симлинки в `~/.agents/skills` и `~/.claude/skills`; снести `~/.codex/skills/{arc-finder,tracker-issues}`, битый `write-a-skill`.
3. `bin/build` → `agents/openai.yaml` для dmi-скиллов.
4. Cursor IDE: выключить third-party imports (убирает дубли); проверить `agent` `/skills`.
5. launchd + SessionStart-хук.
6. Проверка: `claude` → `/context` (Skills), `codex` → `/skills`, `agent` → `/skills` — один экземпляр каждого.

## 5. Открытые вопросы

- Нужны ли несколько машин? Если нет — автообновление по сути no-op, launchd можно не ставить.
- Вендорить ли сторонние (mattpocock) в репу (dotfiles-стиль, julianarchila) или оставить под `npx skills` (lock + `skills update -g -y` по cron; баг #162 — для **private** GitHub-репо hash пустой, update не работает)? Рекомендация: оставить под `npx skills`.
- Скиллы в `~/arcadia/ai/artifacts/skills/` — отдельный источник; `bin/link` может поддержать N источников.
- Хотим ли сразу публикацию (маркетплейс-манифесты)?

## 6. Источники

Claude Code: code.claude.com/docs/en/{skills,hooks,settings-reference,env-vars,memory,sub-agents,plugins/*}; platform.claude.com best-practices; CHANGELOG; issues anthropics/claude-code #31005, #17351, #29191, #79560, #86763, #56912, #77795, #64606, #19308.
Codex: learn.chatgpt.com/docs/{build-skills,plugins,build-plugins,config-file/config-reference,developer-commands,sandboxing}; developers.openai.com/plugins; openai/codex `codex-rs/ext/skills/src/*`, `core-plugins/src/manager.rs`; issues #22275, #31592, #11314, #23454, #32169, #44172, #23987, #39421, #22078, #5237; openai/skills #420.
Cursor: cursor.com/docs/{skills,context/rules,plugins,reference/plugins,reference/third-party-hooks,cli/*}; changelog 2.4/2.5; forum 149017, 149693, 156001, 156577, 157274, 160677, 161142, 163569, 166056, 169575.
Стандарт/тулинг: agentskills.io/specification; agentskills issues #137, discussion #374; vercel-labs/skills README/AGENTS.md/src/cli.ts, issues #162, #209, #386; mattpocock/skills; obra/superpowers (hooks.json, using-superpowers, writing-skills, RELEASE-NOTES); MattKay02/skills#1; sci-brain#50; copilot-cli#1090; scottspence.com; dev.to/rulestack; runkids/skillshare; skilz-cli; chezmoi/stow примеры.

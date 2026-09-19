# День 4 — Provenance & trust

Дата: 2026-09-19  
Тема: откуда факт (`provenance`), когда ему верить в base context, `evidence` → stale, `supersedes` / `valid_from`, и зачем не врать про источник.

## Мини-повтор дня 3 (слабые зоны)

- Чанки documents **не** в `memory_search` — отдельные эндпоинты, чтобы не ломать baseline.
- Длинный Plaud → `document_add`, не `memory_add`.
- Фильтр `kind` — в **SQL до** кандидатов (PR #80).
- LLM-enrichment чанков **сознательно не** делается.

## Зачем это тебе

Hermes — не «свалка строк», а память с **уровнем доверия**. Base context (`memory_get_context`) автозагружает только категории `projects` / `rules` / `architecture`. Но даже внутри них запись с `provenance=external` **не** попадёт в автоконтекст будущих сессий: её можно найти поиском, но агент не должен молча считать чужой README твоим правилом.

Типичные ошибки:
1. Пометить scraped README как `user`, чтобы «залипло» в каждую сессию.
2. Записать вывод агента как `user` без подтверждения Stan.
3. Забыть `evidence` на факте «в коде сейчас X» — после деплоя факт врёт, но всё ещё в base context.

## Схема доверия

```
memory_add(...)
        │
        ├─ provenance
        │     user     → можно в base context (если категория projects/rules/architecture)
        │     agent    → вывод агента из сессии; осторожнее с автозагрузкой
        │     external → только searchable; НЕ в auto base context
        │
        ├─ evidence? (git|file + path + commit/sha256 [+ expires_at])
        │     checker сверяет источник → при расхождении: STALE
        │     stale: searchable, выкинут из auto base context
        │
        ├─ supersedes=<old_id> → старая запись INVALIDATED (история есть, auto — нет)
        ├─ valid_from=YYYY-MM-DD → memory_search(as_of=...) различает эпохи
        └─ related_ids / expand_links → связанные факты одним хопом
```

`source` в MCP — «кто пишет» (часто имя агента); `provenance` — **откуда знание**. Не путать.

## Evidence (чек)

```python
# Факт про код hermes-memory — с чеком
memory_add(
    content="POST /usage отвечает всегда {\"ok\": true} без auth — by design.",
    category="architecture",
    provenance="agent",  # вывод из кода/сессии; подтвердит user → лучше user
    evidence={
        "source": "hermes-memory",
        "kind": "git",
        "path": "src/main.py",
        "repo": "/opt/hermes-memory",
        "commit": "abc123...",
    },
)

# Предпочтение Stan — без evidence
memory_add(
    content="Отвечать по-русски, пока Stan не попросил иначе.",
    category="rules",
    provenance="user",
)
```

Stale / invalidated остаются **находимыми** (история, audit), но не засоряют автозагрузку.

## Journal vs durable (мост к дню 1)

`kind=journal` (или Easy Apply day log) + `skip_contradictions` — дневник, не правило. Не тащить в contradiction health как конфликт с architecture.

## Мост к учёту (день 5)

Доверие к **расходу** токенов: соседи шлют fire-and-forget `POST /usage` по сети `llm-shared`; `usage_summary` агрегирует. Это не provenance факта, но тот же принцип: измеримое > «кажется дорого».

Живой срез (урок 2026-09-19): за 7 дней ~4871 req, ~$1.36; топ — hermes Voyage rerank + brazilmama Anthropic.

## Код: честный provenance

```python
# Плохо: чужой README как user → попадёт в каждую сессию
memory_add(content=readme_claim, category="architecture", provenance="user")

# Хорошо: external — searchable, не auto
memory_add(content=readme_claim, category="architecture", provenance="external")

# После подтверждения Stan:
memory_add(
    content=readme_claim,
    category="architecture",
    provenance="user",
    supersedes=old_external_id,
)
```

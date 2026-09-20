# День 5 — Usage accounting

Дата: 2026-09-20  
Тема: учёт токенов через `POST /usage`, сеть `llm-shared`, `usage_summary`, кто шлёт / кто нет.

## Мини-повтор дня 4 (provenance — квиз ещё open)

- `provenance` = откуда знание (`user` / `agent` / `external`), не путать с MCP `source` (кто пишет).
- `external` — searchable, но **не** auto base context.
- Не помечать scraped README как `user` «чтобы залипло».
- `evidence` (git/file) → checker → STALE выкидывает из auto, поиск остаётся.

## Зачем это тебе

Hermes — не только память, а **центр учёта** экосистемы на VPS. Соседи (brazilmama, telegram-bot, translate_brazil, hermes сам) после LLM-вызова шлют fire-and-forget `POST /usage` по docker-сети `llm-shared`. Ты видишь расход через MCP `usage_summary` — измеримое вместо «кажется дорого».

Без LLM / без `/usage`: только `susser_telegram_bot` и `Parser_chat_web`.

## Схема

```
LLM call в проекте (brazilmama / translate_brazil / …)
        │
        ├─ локальный лог (напр. data/ai_usage.jsonl) — опционально
        └─ fire-and-forget POST http://hermes-memory-api:8765/usage
                  │  сеть: llm-shared (НЕ 127.0.0.1 из чужого контейнера)
                  ▼
           hermes FastAPI :8765
                  │  без auth by design → всегда {"ok": true}
                  ▼
           usage rows → usage_summary(days=N)
```

Важно: внутри контейнера `http://127.0.0.1:8765/usage` бьёт **в себя**, не в hermes. Compose задаёт hostname сервиса через `llm-shared`.

## Живой срез (урок 2026-09-20, last 7d)

- ~5418 req, ~$1.51 (known prices)
- Топ: hermes-memory · voyage · rerank-2.5-lite (~3428 req, ~$0.98)
- Затем brazilmama Anthropic (haiku/sonnet), Groq, voyage embed
- API cache: embed_query ~84%, memory_search ~1%

## Код: минимальный ingest

```javascript
// fire-and-forget — не блокировать UX бота
fetch(process.env.USAGE_INGEST_URL, {
  method: "POST",
  headers: { "content-type": "application/json" },
  body: JSON.stringify({
    project: "brazilmama",
    provider: "anthropic",
    model: "claude-haiku-4-5-20251001",
    input_tokens: usage.input_tokens,
    output_tokens: usage.output_tokens,
  }),
}).catch(() => {});
```

```python
# MCP / REST: сводка
usage_summary(days=7)
# → requests, $ по проектам/провайдерам/моделям, cache hit-rate
```

## Мост к дню 6

Учёт показывает **сколько** стоит агент-петля; день 6 — почему боты event-driven без LangGraph/CrewAI (дешевле и проще ops).

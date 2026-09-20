# Answers — 2026-09-20 — Usage accounting

Не спойлерить до сдачи квиза в чате.

| # | Ответ | Почему (кратко) |
|---|-------|-----------------|
| 1 | B | `llm-shared` даёт DNS `hermes-memory-api`; `127.0.0.1` из чужого контейнера — сам себе |
| 2 | B | Ingest без auth by design, всегда `{"ok": true}` — не «чинить» |
| 3 | B | Compose: `USAGE_INGEST_URL=http://hermes-memory-api:8765/usage` |
| 4 | B | susser и Parser_chat_web без LLM / без /usage |
| 5 | B | Учёт не должен ронять UX; `.catch(() => {})` |
| 6 | B | Сводка по проектам/провайдерам/моделям + cache |
| 7 | B | На срезе топ $ — Voyage rerank в hermes |
| 8 | B | Query embed кешируется часто; полный search — почти нет |
| 9 | B | external searchable, не auto base context |
| 10 | B | Не врать provenance; scraped → external |

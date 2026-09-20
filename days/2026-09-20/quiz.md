# Quiz — 2026-09-20 — Usage accounting

Ответь в чате без подглядывания в `answers.md`. Формат: `1. B` …

Мини-повтор provenance (день 4): Q9–Q10.

---

### 1
**RU:** Зачем соседним контейнерам сеть `llm-shared` для учёта?  
**EN:** Why do sibling containers need the `llm-shared` network for usage?

- A) Чтобы Postgres слушал `0.0.0.0`
- B) Чтобы `POST /usage` доходил до `hermes-memory-api` по docker DNS, а не в свой loopback
- C) Только для Caddy TLS
- D) Для публичного MCP `:8766`

---

### 2
**RU:** `POST /usage` в hermes…  
**EN:** Hermes `POST /usage`…

- A) Требует `X-API-Key` как остальные write-эндпоинты
- B) Намеренно без auth и всегда отвечает `{"ok": true}` — by design
- C) Только с Bearer `MCP_API_KEY`
- D) Только с localhost Mac

---

### 3
**RU:** Какой URL из контейнера brazilmama правильный для ingest?  
**EN:** Correct usage ingest URL from inside the brazilmama container?

- A) `http://127.0.0.1:8765/usage`
- B) `http://hermes-memory-api:8765/usage` (через `llm-shared`)
- C) `https://181.214.100.54:8766/usage`
- D) `redis://localhost:6379`

---

### 4
**RU:** Кто из экосистемы НЕ шлёт LLM-расход в hermes `/usage`?  
**EN:** Which projects do NOT send LLM usage to hermes `/usage`?

- A) brazilmama и translate_brazil
- B) `susser_telegram_bot` и `Parser_chat_web`
- C) hermes-memory и telegram-bot
- D) Только Caddy

---

### 5
**RU:** Зачем fire-and-forget на `POST /usage`?  
**EN:** Why fire-and-forget for `POST /usage`?

- A) Чтобы блокировать ответ пользователю до записи
- B) Чтобы сбой учёта не ломал UX бота; ошибки глотаются
- C) Чтобы удвоить токены
- D) Чтобы открыть порт `5433` наружу

---

### 6
**RU:** Что показывает MCP `usage_summary`?  
**EN:** What does MCP `usage_summary` show?

- A) Только список документов Plaud
- B) Агрегат req/токенов/$ по проектам·провайдерам·моделям за N дней (+ cache hit-rate)
- C) Только stale evidence
- D) Git blame

---

### 7
**RU:** На срезе урока (~7d) главный $ часто идёт от…  
**EN:** On the lesson snapshot (~7d) the top $ often comes from…

- A) susser LLM
- B) hermes-memory Voyage rerank (много `memory_search`)
- C) Только Whisper
- D) Caddy access logs

---

### 8
**RU:** embed_query cache ~84% vs memory_search ~1% значит…  
**EN:** embed_query cache ~84% vs memory_search ~1% means…

- A) Поиск всегда бесплатный
- B) Повторяющиеся query-эмбеддинги хорошо кешируются; полный search-путь почти не бьёт в тот же кеш
- C) Нужно отключить Voyage
- D) Порт `8765` открыт наружу

---

### 9 (мини-повтор provenance)
**RU:** Запись с `provenance=external` в будущих сессиях…  
**EN:** A memory with `provenance=external` in future sessions…

- A) Всегда автозагружается в `memory_get_context`
- B) Остаётся searchable, но не auto-loaded в base context
- C) Удаляется через сутки
- D) Пишется только в Redis

---

### 10 (мини-повтор provenance)
**RU:** Можно ли помечать scraped README как `user`, чтобы «залипло»?  
**EN:** OK to mark a scraped README as `user` so it sticks in every session?

- A) Да, так и задумано
- B) Нет — это обман доверия; для неподтверждённого текста — `external`
- C) Да, если категория `general`
- D) Только по выходным

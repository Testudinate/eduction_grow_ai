# Quiz — 2026-09-16 — Hermes Memory architecture

Ответь без подглядывания в `answers.md`. Формат: `1. …` …

---

### 1
**RU:** Какая роль у Hermes Memory относительно coding harness (Claude Code и т.п.)?  
**EN:** What is Hermes Memory’s role relative to a coding harness (e.g. Claude Code)?

- A) Полный agent loop + sandbox + TUI  
- B) Кросс-репо / кросс-сессионная память и предпочтения  
- C) Замена CLAUDE.md внутри одного репо  
- D) Только биллинг токенов

---

### 2
**RU:** Какие категории автозагружаются через `memory_get_context`?  
**EN:** Which categories are auto-loaded via `memory_get_context`?

- A) только `general`  
- B) `projects`, `rules`, `architecture`  
- C) `preferences` и все документы  
- D) все категории без исключения

---

### 3
**RU:** Порты 5433 (Postgres) и 8765 (API) в проде Hermes должны слушать…  
**EN:** In Hermes prod, ports 5433 and 8765 should bind to…

- A) `0.0.0.0` для удобства MCP  
- B) только `127.0.0.1`  
- C) публичный IP VPS  
- D) только docker-сеть без localhost

---

### 4
**RU:** Чем `document_search` принципиально отличается от `memory_search`?  
**EN:** How does `document_search` fundamentally differ from `memory_search`?

- A) document_search ищет по коротким фактам без чанков  
- B) document_search работает по чанкам длинных документов; memory_search — по коротким фактам  
- C) это два имени одного эндпоинта  
- D) document_search не использует эмбеддинги

---

### 5
**RU:** Зачем соседним ботам сеть `llm-shared`?  
**EN:** Why do sibling bots use the `llm-shared` Docker network?

- A) Шарить один Postgres на запись  
- B) Fire-and-forget `POST /usage` учёта токенов в Hermes  
- C) Общий SSH на хост  
- D) Публиковать MCP наружу без TLS

---

### 6
**RU:** Критерий «сохранять в память» по правилам Hermes:  
**EN:** Hermes’ rule for what to store in memory:

- A) Всё, что было в сессии  
- B) Любой код длиннее 20 строк  
- C) Только то, без чего потом сломается решение/правило/предпочтение  
- D) Все саммари встреч Plaud целиком в `projects`

---

### 7
**RU:** Какой стек эмбеддингов/rerank у Hermes (по архитектуре)?  
**EN:** Which embeddings/rerank stack does Hermes use (per architecture)?

- A) OpenAI ada-002 only  
- B) Voyage AI (voyage-3 + rerank-2) + pgvector  
- C) Только BM25 без векторов  
- D) Chroma locally without API

---

### 8
**RU:** MCP наружу (:8766) допустим когда…  
**EN:** Exposing MCP (:8766) is acceptable when…

- A) Всегда, ключ не обязателен  
- B) Только вместе с TLS + Bearer, сервисы fail-closed  
- C) Через HTTP без TLS на 80 порту  
- D) Если открыт и Postgres :5433

---

### 9
**RU:** Почему агентные фреймворки (LangGraph, CrewAI, …) сознательно не внедряются в экосистему Stan?  
**EN:** Why are agent frameworks (LangGraph, CrewAI, …) deliberately not adopted in Stan’s stack?

- A) Они запрещены лицензией  
- B) Оркестрация уже в harness; прод — событийные боты «сообщение → RAG → один LLM-вызов»; большинство проектов на Node  
- C) Они не поддерживают Postgres  
- D) Потому что нет GPU

---

### 10
**RU:** Практический вопрос. Дан факт: «Сегодня чинил баг в PR #42, завтра закрою». Куда его класть?  
**EN:** Practical: fact “Fixed a bug in PR #42 today, will close tomorrow.” Where does it belong?

- A) `architecture` via memory_add  
- B) `projects` base context  
- C) Обычно никуда в durable memory — это журнал сессии, не правило  
- D) Обязательно document_add как статья


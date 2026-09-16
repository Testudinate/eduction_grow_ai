# Quiz — 2026-09-17 — Embeddings & hybrid search

Ответь в чате без подглядывания в `answers.md`. Формат: `1. …` …

Мини-повтор дня 1: Q8–Q10.

---

### 1
**RU:** Что такое embedding в контексте Hermes Memory?  
**EN:** What is an embedding in Hermes Memory?

- A) Сжатый лог сессии Claude  
- B) Числовой вектор смысла текста фиксированной размерности  
- C) SQL-индекс только по точным словам  
- D) Bearer-токен MCP

---

### 2
**RU:** Какие модели Voyage сейчас на проде Hermes (embed / rerank)?  
**EN:** Which Voyage models are on Hermes prod (embed / rerank)?

- A) `ada-002` / без rerank  
- B) `voyage-3.5-lite` / `rerank-2.5-lite`  
- C) Только локальный sentence-transformers  
- D) `voyage-code-2` / `rerank-lite-1`

---

### 3
**RU:** Что обязательно сделать при смене `VOYAGE_EMBED_MODEL`?  
**EN:** What must you do when changing `VOYAGE_EMBED_MODEL`?

- A) Ничего — векторы совместимы между моделями  
- B) Перезапустить только MCP-контейнер  
- C) Полный re-embed базы (`python -m src.reembed`), иначе смешанное пространство  
- D) Очистить только Redis-кэш

---

### 4
**RU:** Из каких двух веток состоит гибридный поиск Hermes?  
**EN:** Which two branches make up Hermes hybrid search?

- A) GraphRAG + SQL JOIN  
- B) Vector (pgvector) + keyword (tsvector/FTS)  
- C) Только BM25 и только LLM-judge  
- D) OAuth + rate limit

---

### 5
**RU:** Зачем RRF (Reciprocal Rank Fusion) после двух веток?  
**EN:** Why RRF after the two retrieval branches?

- A) Чтобы биллить Voyage дважды  
- B) Слить ранги веток без калибровки сырых скоров (у вас k=60)  
- C) Заменить rerank полностью  
- D) Нормализовать только кириллицу в URL

---

### 6
**RU:** Когда в пайплайне обычно вызывается Voyage rerank?  
**EN:** When is Voyage rerank typically called in the pipeline?

- A) До embedding запроса  
- B) После слияния кандидатов (RRF), перед финальным top-N  
- C) Только при `memory_add`  
- D) Вместо keyword-ветки

---

### 7
**RU:** Почему keyword-ветка выгодна по стоимости?  
**EN:** Why is the keyword branch cost-efficient?

- A) Она всегда точнее vector на paraphrases  
- B) Не вызывает Voyage embed/rerank на свою ветку; FTS в Postgres  
- C) Пишет результаты в Qdrant бесплатно  
- D) Отключает `matched_by`

---

### 8
**RU:** (повтор) Какие категории автозагружает `memory_get_context`?  
**EN:** (review) Which categories does `memory_get_context` auto-load?

- A) только `general`  
- B) `projects`, `rules`, `architecture`  
- C) `preferences` и все документы  
- D) все категории без исключения

---

### 9
**RU:** (повтор) Порты 5433 и 8765 на проде Hermes должны слушать…  
**EN:** (review) Ports 5433 and 8765 in Hermes prod should bind to…

- A) `0.0.0.0`  
- B) только `127.0.0.1`  
- C) публичный IP VPS  
- D) только docker-сеть без localhost

---

### 10
**RU:** (повтор) Факт «Сегодня чинил баг в PR #42, завтра закрою» — куда?  
**EN:** (review) Fact “Fixed PR #42 today, close tomorrow” — where?

- A) `architecture` via memory_add  
- B) `projects` base context  
- C) Обычно никуда в durable memory — журнал сессии, не правило  
- D) Обязательно `document_add`

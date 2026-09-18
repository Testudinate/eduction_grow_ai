# Quiz — 2026-09-18 — Documents vs memories

Ответь в чате без подглядывания в `answers.md`. Формат: `1. B` …

Мини-повтор дней 1–2: Q8–Q10.

---

### 1
**RU:** Главное отличие documents от memories в Hermes?  
**EN:** Main difference between documents and memories in Hermes?

- A) Documents идут только в Redis, memories — только в Postgres  
- B) Memories — короткие факты (запись целиком); documents — длинный текст, поиск по чанкам  
- C) Documents автозагружаются в `memory_get_context`, memories — нет  
- D) Разницы нет: оба пишутся через `memory_add`

---

### 2
**RU:** Почему чанки documents намеренно не подмешиваются в `memory_search`?  
**EN:** Why are document chunks intentionally not mixed into `memory_search`?

- A) Чанки не имеют embeddings  
- B) Чтобы не менять ретрив/baseline памяти без отдельного eval  
- C) Потому что RRF не умеет два источника  
- D) MCP запрещает два инструмента сразу

---

### 3
**RU:** Типичные параметры chunking в Hermes (`src/chunking.py`)?  
**EN:** Typical Hermes chunking params?

- A) 100 симв., overlap 0  
- B) ~1500 симв., overlap ~200  
- C) Один чанк = весь документ всегда  
- D) Только по словам без overlap

---

### 4
**RU:** Какой MCP-инструмент добавить длинный Plaud-транскрипт?  
**EN:** Which MCP tool to store a long Plaud transcript?

- A) `memory_add` в категорию `architecture`  
- B) `document_add`  
- C) `session_start`  
- D) `publish_linkedin`

---

### 5
**RU:** Нужен фрагмент из длинного отчёта — какой поиск?  
**EN:** Need a fragment from a long report — which search?

- A) Только `memory_get_context`  
- B) `document_search`  
- C) Только `usage_summary`  
- D) `memory_delete`

---

### 6
**RU:** Фильтр `kind` у documents (напр. `plaud-digest`) применяется…  
**EN:** Document `kind` filter is applied…

- A) Только после Voyage rerank в Python  
- B) В SQL до отбора кандидатов  
- C) Клиентом вручную после ответа  
- D) Никогда — kind декоративный

---

### 7
**RU:** LLM-enrichment каждого чанка при add в Hermes…  
**EN:** LLM enrichment of every chunk on add in Hermes…

- A) Обязателен на проде  
- B) Сознательно не делается (дорого/шумно на сотнях чанков Plaud)  
- C) Заменяет embeddings  
- D) Включено только для `memory_add`

---

### 8
**RU:** (повтор) Keyword-ветка дешевле vector, потому что…  
**EN:** (review) Keyword branch is cheaper because…

- A) Всегда точнее на paraphrases  
- B) Не жжёт Voyage на свою ветку; FTS в Postgres  
- C) Пишет в Qdrant бесплатно  
- D) Отключает RRF

---

### 9
**RU:** (повтор) Какие категории в base context?  
**EN:** (review) Which categories are in base context?

- A) только `general`  
- B) `projects`, `rules`, `architecture`  
- C) все пять категорий всегда  
- D) только `preferences`

---

### 10
**RU:** (повтор) Порты 5433 и 8765 на проде bind…  
**EN:** (review) Ports 5433 and 8765 in prod bind to…

- A) `0.0.0.0` публично  
- B) `127.0.0.1`  
- C) Только IPv6  
- D) Случайный порт каждый деплой

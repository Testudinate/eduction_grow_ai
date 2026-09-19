# Quiz — 2026-09-19 — Provenance & trust

Ответь в чате без подглядывания в `answers.md`. Формат: `1. B` …

Мини-повтор дня 3: Q8–Q10.

---

### 1
**RU:** Что задаёт `provenance` у `memory_add`?  
**EN:** What does `provenance` on `memory_add` control?

- A) Только язык ответа агента  
- B) Откуда факт (user/agent/external) и достаточно ли доверия для auto base context  
- C) Размер embedding  
- D) Порт Postgres

---

### 2
**RU:** Запись с `provenance=external` в будущих сессиях…  
**EN:** A memory with `provenance=external` in future sessions…

- A) Всегда автозагружается в `memory_get_context`  
- B) Остаётся searchable, но не auto-loaded в base context  
- C) Удаляется через сутки  
- D) Пишется только в Redis

---

### 3
**RU:** Можно ли помечать scraped README как `user`, чтобы «залипло»?  
**EN:** OK to mark a scraped README as `user` so it sticks in every session?

- A) Да, так и задумано  
- B) Нет — это обман доверия; для неподтверждённого текста — `external`  
- C) Да, если категория `general`  
- D) Только по выходным

---

### 4
**RU:** Зачем поле `evidence` (git/file + path + commit/sha256)?  
**EN:** Why pass `evidence` (git/file + path + commit/sha256)?

- A) Чтобы ускорить Voyage rerank  
- B) Машинопроверяемый чек: при расхождении с источником факт помечается stale  
- C) Обязательно для preferences  
- D) Включает LangGraph

---

### 5
**RU:** Что происходит со stale-памятью?  
**EN:** What happens to a stale memory?

- A) Физически удаляется сразу  
- B) Остаётся searchable, но выпадает из auto-loaded base context  
- C) Блокирует весь MCP  
- D) Конвертируется в document chunk

---

### 6
**RU:** `supersedes=<old_id>` делает со старой записью…  
**EN:** `supersedes=<old_id>` does to the old entry…

- A) Ничего  
- B) Помечает invalidated (история/поиск есть, auto base context — нет) в той же транзакции  
- C) Копирует embedding в Qdrant  
- D) Меняет provenance на external всегда

---

### 7
**RU:** `valid_from` + `memory_search(as_of=...)` нужны чтобы…  
**EN:** `valid_from` + `memory_search(as_of=...)` exist to…

- A) Считать токены  
- B) Различать «что было верно на дату» vs текущее состояние  
- C) Включать RRF  
- D) Биндить 5433 на 0.0.0.0

---

### 8
**RU:** (повтор д3) Почему чанки не в `memory_search`?  
**EN:** (Day3 review) Why chunks stay out of `memory_search`?

- A) У чанков нет FTS  
- B) Чтобы не ломать baseline памяти без отдельного eval  
- C) MCP запрещает два инструмента  
- D) Чанки только в ClickHouse

---

### 9
**RU:** (повтор д3) Длинный Plaud кладём через…  
**EN:** (Day3 review) Long Plaud goes via…

- A) `memory_add` → `architecture`  
- B) `document_add`  
- C) `usage_summary`  
- D) `session_end`

---

### 10
**RU:** (повтор д3) Фильтр `kind` у documents применяется…  
**EN:** (Day3 review) Document `kind` filter is applied…

- A) Только после rerank в Python  
- B) В SQL до отбора кандидатов  
- C) Вручную клиентом после ответа  
- D) Никогда


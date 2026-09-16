# Answers — 2026-09-17 — Embeddings & hybrid search

Не спойлерить до сдачи квиза в чате.

| # | Ответ | Пояснение |
|---|-------|-----------|
| 1 | B | Embedding = вектор смысла фиксированной размерности |
| 2 | B | Прод: voyage-3.5-lite + rerank-2.5-lite |
| 3 | C | Смена embed-модели → полный re-embed, иначе mixed space |
| 4 | B | Hybrid = vector (pgvector) + keyword (tsvector/FTS) |
| 5 | B | RRF сливает ранги; k=60 в Hermes/telegram-bot |
| 6 | B | Rerank после RRF, перед top-N |
| 7 | B | Keyword/FTS не жжёт Voyage на своей ветке |
| 8 | B | base context: projects, rules, architecture |
| 9 | B | 5433/8765 → 127.0.0.1 |
| 10 | C | Одноразовый журнал ≠ durable / projects |

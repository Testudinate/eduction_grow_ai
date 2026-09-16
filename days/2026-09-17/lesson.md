# День 2 — Embeddings & hybrid search

Дата: 2026-09-17 (доставка в чат 2026-09-16 09:00 MSK)  
Тема: как Hermes (и соседние RAG) находят нужный факт — вектор + keyword + RRF + rerank.

## Мини-повтор слабых зон дня 1

- Base context (`memory_get_context`): только `projects`, `rules`, `architecture`.
- Порты `5433` / `8765` на проде — bind `127.0.0.1`.
- Durable memory: «если забыть — что сломается?»; одноразовый журнал сессии туда не кладём.
- Факт «сегодня чинил PR» → обычно session journal, не `projects`.

## Зачем это тебе

Каждый `memory_search` / `document_search` в Hermes — не «магия LLM», а пайплайн ретрива. Тот же каркас (гибрид → RRF → rerank) у тебя в `telegram-bot` и `brazilmama`. Ошибка на этом слое = агент «забыл» правильный факт или подтянул мусор.

## Схема пайплайна Hermes `/memory/search`

```
query
  ├─► embedding (Voyage) ──► vector search (pgvector)
  └─► keyword (tsvector/GIN, websearch_to_tsquery) ──► FTS
              │
              ▼
         RRF merge (k=60)  →  candidates
              │
              ▼
         Voyage rerank     →  top-N
              │
              ▼
         matched_by: vector | keyword | both
```

Keyword-ветка **не** жжёт Voyage. Кандидатов после слияния не больше прежнего `candidate_limit` — гибрид не раздувает bill.

## Embeddings (коротко)

Текст → числовой вектор фиксированной размерности. Близкие по смыслу фразы ближе в пространстве (cosine / inner product в pgvector).

На проде Hermes сейчас: `VOYAGE_EMBED_MODEL=voyage-3.5-lite`, `VOYAGE_RERANK_MODEL=rerank-2.5-lite`.

**Критично:** смена embed-модели требует `python -m src.reembed` по **всей** базе. Иначе смесь пространств → деградация поиска.

## Hybrid + RRF

- **Vector** ловит paraphrases («порт наружу» ≈ «bind localhost»).
- **Keyword** ловит точные токены (имена портов, id, редкие слова).
- **RRF** (Reciprocal Rank Fusion): сливает ранги двух списков без калибровки скоров; у вас `k=60`.

## Rerank

После RRF Voyage rerank переупорядочивает кандидатов под запрос. Оптимизация: если кандидатов ≤ `limit`, rerank можно пропустить.

## Мини-пример: RRF на двух списках

```python
def rrf_fuse(rank_lists: list[list[str]], k: int = 60) -> list[tuple[str, float]]:
    """rank_lists[i] = ids в порядке убывания релевантности ветки i."""
    scores: dict[str, float] = {}
    for ranking in rank_lists:
        for rank, doc_id in enumerate(ranking, start=1):
            scores[doc_id] = scores.get(doc_id, 0.0) + 1.0 / (k + rank)
    return sorted(scores.items(), key=lambda x: x[1], reverse=True)

vector_hits = ["m42", "m7", "m9"]      # семантически близко
keyword_hits = ["m7", "m42", "m100"]  # точное совпадение токена
print(rrf_fuse([vector_hits, keyword_hits])[:3])
# m7 и m42 всплывают выше одиночных попаданий
```

Псевдо-вызов поиска (идея, без прод-секретов):

```python
import httpx

BASE = "http://127.0.0.1:8765"
HEADERS = {"X-API-Key": "..."}  # из .env

def memory_search(q: str, limit: int = 5):
    r = httpx.get(
        f"{BASE}/memory/search",
        headers=HEADERS,
        params={"q": q, "limit": limit},
        timeout=30,
    )
    r.raise_for_status()
    return r.json()  # смотри matched_by / scores

# Точный токен часто даёт matched_by с keyword; paraphrase — vector
```

## Связь с твоими проектами

| Проект | Вектор | Keyword | Слияние | Rerank |
|--------|--------|---------|---------|--------|
| hermes-memory | pgvector + Voyage | Postgres tsvector/GIN | RRF k=60 | Voyage |
| telegram-bot | pgvector | `text_tsv` + GIN | `rrfMerge` k=60 | после капа |
| brazilmama | Qdrant | BM25-lite / Qdrant FT | RRF | Voyage (флаг) |

## Практическое задание

Возьми 3 реальных запроса к своей памяти (1 paraphrase, 1 точный id/порт, 1 смесь). Для каждого угадай, какая ветка должна победить, потом сравни с `matched_by` на проде (если доступен).

## Завтра (план)

Documents vs memories: chunking, `document_search` vs `memory_search`, когда что класть.

# День 1 — Hermes Memory: архитектура и поиск

Дата: 2026-09-16  
Тема: как устроен твой модуль памяти (не «ещё один чат-бот»).

## Зачем это тебе

Hermes Memory — слой **кросс-репо / кросс-сессионных фактов**. Внутри одного репо лучше CLAUDE.md + grep; Hermes нужен, когда знание должно жить между проектами и устройствами.

## Схема (упрощённо)

```
Claude / агент
    │  MCP Streamable HTTP :8766 (Bearer MCP_API_KEY)
    ▼
MCP-сервис
    │  REST FastAPI :8765 (X-API-Key)
    ▼
API → asyncpg → PostgreSQL + pgvector :5433
         ▲
    Voyage AI (voyage-3 embeddings, rerank-2)
```

Соседние боты шлют расход токенов fire-and-forget: `POST /usage` через docker-сеть `llm-shared`.

## Категории памяти

| Категория | Автозагрузка в сессию? | Для чего |
|-----------|------------------------|----------|
| `projects` / `rules` / `architecture` | да (`memory_get_context`) | база, коротко, без дат |
| `preferences` / `general` | нет (только search) | предпочтения, датированные факты |

Критерий записи: *«если забыть — что сломается?»*. Журнал сессий ≠ память.

## Два инструмента поиска

- **`memory_search`** — короткие факты (vector + rerank).
- **`document_search`** — длинные документы (чанки с перекрытием). Намеренно не смешиваются в одном эндпоинте.

## Мини-пример: добавить факт и найти его

```python
# псевдо-клиент к REST (идея, не копипаст с прод-секретами)
import httpx

BASE = "http://127.0.0.1:8765"
HEADERS = {"X-API-Key": "..."}  # только из .env

def memory_add(content: str, category: str = "general"):
    r = httpx.post(
        f"{BASE}/memory",
        headers=HEADERS,
        json={"content": content, "category": category, "provenance": "user"},
        timeout=30,
    )
    r.raise_for_status()
    return r.json()

def memory_search(query: str, limit: int = 5):
    r = httpx.get(
        f"{BASE}/memory/search",
        headers=HEADERS,
        params={"q": query, "limit": limit},
        timeout=30,
    )
    r.raise_for_status()
    return r.json()

# Пример факта, который стоит помнить:
memory_add(
    "Hermes Memory: порты 5433 и 8765 только на 127.0.0.1; 8766 наружу только с TLS.",
    category="architecture",
)
hits = memory_search("какие порты hermes memory слушают снаружи")
```

## Практическое задание (код)

Напиши у себя 10–15 строк: функцию `should_store(fact: str) -> bool`, которая возвращает `False` для одноразовых деталей задачи и `True` для решений/правил. Завтра разберём граничные случаи.

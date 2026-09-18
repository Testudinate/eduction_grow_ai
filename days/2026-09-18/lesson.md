# День 3 — Documents vs memories (chunking)

Дата: 2026-09-18  
Тема: когда класть факт в memory, а когда длинный текст в documents; как устроен chunking и почему `document_search` отделён от `memory_search`.

## Мини-повтор

- Base context (`memory_get_context`): только `projects`, `rules`, `architecture`.
- Порты `5433` / `8765` на проде — bind `127.0.0.1`.
- Durable: «если забыть — что сломается?»
- Keyword дёшев (FTS, без Voyage на свою ветку); vector лучше на paraphrases.
- Смена embed-модели → полный `python -m src.reembed`.

## Зачем это тебе

В Hermes у тебя **два хранилища одного сервиса**:

| | **Memories** | **Documents** |
|---|---|---|
| Что | короткие факты, решения, правила | длинные тексты (Plaud, транскрипты, отчёты) |
| MCP | `memory_add` / `memory_search` / `memory_get_context` | `document_add` / `document_search` / `document_list` |
| Единица поиска | целая запись | **чанк** (кусок документа) |
| Базовый контекст | да (3 категории) | нет — только поиск |

Ошибка выбора слоя: либо раздуваешь base context тысячами символов транскрипта, либо дробишь важное правило на чанки и теряешь его в каждой сессии.

## Схема

```
короткий факт / предпочтение / правило
        │
        ▼
   memory_items  ──► memory_search / memory_get_context

длинный текст (Plaud, PDF-выжимка, лог)
        │
        ▼
   memory_documents
        │  chunking.py (~1500 симв., overlap 200)
        ▼
   memory_document_chunks  ──► document_search
        │
        │  (намеренно НЕ подмешиваются в memory_search)
```

Оба поиска используют **тот же** гибрид, что в дне 2: vector + FTS → RRF (k=60) → Voyage rerank. Разница — **таблица и эндпоинт**, не алгоритм.

## Chunking (коротко)

`src/chunking.py` (миграция 010, с 14.08.2026):

1. Текст режется по абзацам/предложениям.
2. Цель ~**1500** символов на чанк, **overlap 200** (чтобы граница не резала мысль).
3. Каждый чанк получает свой embedding + tsvector.
4. LLM-enrichment чанков **не** делается осознанно (сотни чанков за один Plaud-прогон — дорого и шумно).

Опционально: `DOC_CONTEXTUAL_EMBED` (contextual retrieval без LLM) — включать только после замера `eval_documents.py`.

Фильтр `kind` (meta, напр. `plaud-digest`) режется в SQL **до** кандидатов (PR #80) — не после rerank.

## Когда что вызывать

```python
# Факт «порты 5433/8765 только на 127.0.0.1» → memory (rules/architecture)
memory_add(content="...", category="architecture")

# 40-страничный Plaud-транскрипт → document
document_add(title="Plaud 2026-04-22", content=full_text, kind="plaud-digest")

# Ищем правило деплоя → memory_search
# Ищем «что Саша говорил про чанки Confluence» → document_search
```

Псевдо-разрез чанков (идея overlap):

```python
def chunk_text(text: str, size: int = 1500, overlap: int = 200) -> list[str]:
    chunks, i = [], 0
    while i < len(text):
        chunks.append(text[i : i + size])
        i += size - overlap
    return chunks
```

## Связь с твоими проектами

- **Hermes**: разделение закрыло дыру разбора 27.07; чанки в `memory_search` не мешают baseline памяти.
- **Plaud / Confluence-парсер**: типичный документный пайплайн — мета → блоки → чанки → RAG.
- **brazilmama / telegram-bot**: короткие правила и prefs остаются в memory; длинные гайды/логи — кандидаты в documents.

## Итог одной фразой

Memory = **атомарные факты для агента**; documents = **корпус длинных текстов, разрезанный на чанки**. Поиск раздельный, пайплайн ретрива общий.

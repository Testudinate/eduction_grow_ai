# Curriculum (roadmap)

Темы крутятся вокруг реальных репо: `hermes-memory`, `telegram-bot`, `translate_brazil`, `whatsapp-ai-assistant` / brazilmama, экосистема VPS.

## Волна 1 — фундамент LLM-продукта

1. **Hermes Memory architecture** — FastAPI, MCP, Postgres+pgvector, категории памяти
2. **Embeddings & hybrid search** — Voyage, vector + keyword, RRF, rerank
3. **Documents vs memories** — chunking, document_search vs memory_search
4. **Provenance & trust** — user/agent/external, stale, evidence
5. **Usage accounting** — POST /usage, llm-shared network

## Волна 2 — агенты и боты

6. Event bots vs agent loops (почему без LangGraph/CrewAI)
7. grammY / Telegram long-polling vs webhooks
8. RAG в production: cache, TTL, token cost (brazilmama)
9. Multimodal: STT + vision OCR (translate_brazil / Groq)
10. MCP connectors: Streamable HTTP, Bearer, fail-closed

## Волна 3 — платформа и ops

11. Docker networks, Caddy, secrets
12. Eval / faithfulness (promptfoo, eval scripts)
13. Observability без утечки PII (Langfuse traces)
14. Fallback LLM providers (429/5xx)
15. DeepSeek Harness / coding agents — sandbox, bubblewrap

Порядок может сдвигаться под слабые зоны из `progress.md`.

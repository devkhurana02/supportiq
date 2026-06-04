# SupportIQ Resume — Project Bullet Points

Use these on your resume, LinkedIn, or in interviews. Tailor to the role.

---

## Resume Bullet Points

**Software Engineer / Full-Stack Developer**

- Built **SupportIQ**, a production-ready AI customer support platform using **FastAPI**, **Next.js 15**, and **PydanticAI** (OpenAI GPT-4/o-series), featuring real-time streaming chat via WebSockets and extended thinking mode for complex queries
- Implemented a **RAG (Retrieval-Augmented Generation) pipeline** using **Milvus** vector store with BM25 hybrid search, supporting PDF, DOCX, Google Drive, and S3 document ingestion — enabling cited, knowledge-base-grounded answers with <500ms retrieval latency
- Architected a **multi-channel messaging system** integrating Telegram (long-polling + webhook) and Slack (Socket Mode + Events API) with a unified conversation store and encrypted bot token management
- Designed a layered backend with **async SQLAlchemy + PostgreSQL**, **Redis caching**, and **Celery** distributed task queue (beat scheduler, Flower monitoring) for background document sync and email delivery
- Integrated **Stripe** billing with subscription webhooks, a credit metering system, and checkout flows — wired to a Next.js marketing/pricing frontend
- Containerized all services with **Docker Compose** (dev/staging/prod profiles), **Traefik** for production TLS termination, and **Nginx** reverse proxy; CI-ready with ruff linting, ty type checking, and pytest
- Built the React frontend with **Zustand** state management, `next-intl` i18n (English/Polish), server-side API proxying for cookie-based auth, and a drag-and-drop knowledge base management UI

---

## One-liner (LinkedIn headline / project description)

> AI-powered customer support platform with RAG knowledge base, multi-channel messaging (Telegram/Slack), Stripe billing, and real-time streaming chat — built with FastAPI + Next.js 15 + PydanticAI.

---

## Interview Talking Points

**"Tell me about a complex system you built"**
- Discuss the RAG pipeline architecture: chunking strategies (recursive/markdown/fixed), embedding with text-embedding-3-small, Milvus HNSW index, BM25 hybrid re-ranking, and citation-aware prompting.
- Walk through the event-driven architecture: Celery workers for async document ingestion, Redis pub/sub for real-time updates, WebSocket streaming for chat.

**"How did you handle auth?"**
- JWT access tokens (10-minute expiry) + refresh token rotation stored in Redis; API key auth for service-to-service; Google OAuth2 PKCE flow; bcrypt password hashing with timing-safe comparison.

**"What would you improve?"**
- Add a feedback loop (thumbs up/down on answers) to fine-tune retrieval quality.
- Replace polling-based Telegram with webhook-only mode behind Traefik for lower latency.
- Add a vector index warm-up cache to reduce cold-start RAG latency.

---

## GitHub Link

🔗 https://github.com/devkhurana02/supportiq

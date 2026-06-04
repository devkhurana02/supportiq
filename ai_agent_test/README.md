# SupportIQ — AI-Powered Customer Support & Knowledge Assistant

> A production-ready, full-stack AI platform that delivers intelligent customer support through a RAG-backed knowledge base, multi-channel messaging (Telegram + Slack), real-time streaming chat, and enterprise-grade billing — all orchestrated by a PydanticAI agent.

[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.135+-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-000000?logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## ✨ Features

| Category | Capabilities |
|---|---|
| 🤖 **AI Agent** | PydanticAI + OpenAI (GPT-4/o-series), streaming responses, extended thinking mode, web search, web fetch, chart generation |
| 📚 **RAG Pipeline** | Milvus vector store, OpenAI embeddings, BM25 hybrid search, PDF/DOCX/Drive/S3 ingestion, cited answers |
| 💬 **Multi-Channel** | Telegram bot (polling + webhook), Slack (Socket Mode + Events API), unified conversation history |
| 🔐 **Auth** | JWT + refresh tokens, API key auth, Google OAuth2 (Sign in with Google) |
| 📊 **Billing** | Stripe subscriptions + webhooks, credit system, per-user usage metering |
| ⚡ **Task Queue** | Celery + Redis for async jobs (scheduled sync, email, heavy ingestion), Flower monitoring |
| 🧩 **Knowledge Base** | Multi-collection RAG, Google Drive sync, S3/MinIO sync, OCR support |
| 🌐 **Frontend** | Next.js 15 + React 19 + Tailwind v4, real-time WebSocket chat, i18n (EN/PL), Zustand |
| 📈 **Observability** | Logfire tracing, PII redaction, structured logging, request correlation IDs |
| 🐳 **Infrastructure** | Docker Compose (dev/staging/prod), Nginx reverse proxy, Traefik TLS |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend (Next.js 15)                   │
│  WebSocket Chat · Knowledge Base UI · Billing · Admin Dashboard │
└───────────────────────────┬─────────────────────────────────────┘
                            │ HTTP / WebSocket
┌───────────────────────────▼─────────────────────────────────────┐
│                      FastAPI Backend (Python 3.12)               │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │  PydanticAI  │  │  RAG Engine  │  │  Auth / Billing      │   │
│  │  Agent       │  │  (Milvus +   │  │  (JWT + Stripe)      │   │
│  │  + Tools     │  │   BM25)      │  │                      │   │
│  └──────┬───────┘  └──────┬───────┘  └──────────────────────┘   │
│         │                 │                                      │
│  ┌──────▼─────────────────▼──────────────────────────────────┐  │
│  │           Repositories / Services / Celery Workers         │  │
│  └──────────────────────────────┬─────────────────────────────┘  │
└─────────────────────────────────┼────────────────────────────────┘
                                  │
         ┌────────────────────────┼──────────────────────┐
         ▼                        ▼                       ▼
   PostgreSQL 16             Redis 7                 Milvus 2.6
   (async SQLAlchemy)        (cache + Celery)        (vector store)
```

---

## 🚀 Quick Start

### Prerequisites

| Tool | Version | Install |
|---|---|---|
| **Docker** | 24+ | [docs.docker.com](https://docs.docker.com/get-docker/) |
| **Make** | GNU 3.81+ | Pre-installed on macOS/Linux |
| **uv** | latest | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| **bun** | 1.x | `curl -fsSL https://bun.sh/install \| bash` |

### First-Time Setup

```bash
# 1. Clone the repo
git clone https://github.com/devkhurana02/supportiq.git
cd supportiq

# 2. Copy and configure environment variables
cp backend/.env.example backend/.env
# Edit backend/.env — set OPENAI_API_KEY at minimum (see Configuration below)

# 3. Bootstrap the full stack (build images, start services, run migrations, seed admin)
make bootstrap
```

**Access the running stack:**

| Service | URL |
|---|---|
| API (Swagger UI) | http://localhost:8000/docs |
| Admin Panel | http://localhost:8000/admin — `admin@example.com` / `admin123` |
| Frontend | http://localhost:3000 (run `make dev-frontend`) |
| Flower (Celery) | http://localhost:5555 |
| Milvus | localhost:19530 |

### Day-to-Day Development

```bash
make dev           # Start / restart dev stack (idempotent)
make dev-down      # Stop all services
make dev-logs      # Tail logs
make dev-frontend  # Start Next.js container
make dev-rebuild   # Force-rebuild backend image
```

---

## ⚙️ Configuration

All backend config lives in `backend/.env`. Key variables:

```bash
# Required
OPENAI_API_KEY=sk-...          # OpenAI API key (agent + embeddings)
SECRET_KEY=<openssl rand -hex 32>

# Database (pre-configured for Docker dev)
POSTGRES_HOST=localhost
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=supportiq

# Optional integrations
GOOGLE_CLIENT_ID=...           # Google OAuth
STRIPE_SECRET_KEY=sk_test_...  # Billing
RESEND_API_KEY=re_...          # Transactional email
TAVILY_API_KEY=...             # Web search tool
SLACK_BOT_TOKEN=xoxb-...       # Slack integration
TELEGRAM_WEBHOOK_BASE_URL=...  # Telegram (leave empty for polling)
```

See [`ENV_VARS.md`](ENV_VARS.md) for the full annotated reference and [`MANUAL_STEPS.md`](MANUAL_STEPS.md) for one-time external service setup (Google OAuth, Stripe, Resend).

---

## 📁 Project Structure

```
supportiq/
├── backend/
│   ├── app/
│   │   ├── agents/          # PydanticAI agent, system prompts, tools
│   │   ├── api/routes/v1/   # REST endpoints (auth, conversations, RAG, billing…)
│   │   ├── core/            # Config, security, middleware, logging
│   │   ├── db/              # SQLAlchemy models, session management
│   │   ├── repositories/    # Data access layer (no commits, flush only)
│   │   ├── services/        # Business logic + channel adapters (Telegram, Slack)
│   │   ├── worker/          # Celery app, tasks, background jobs
│   │   └── commands/        # Click CLI (db, user, RAG, celery)
│   └── alembic/             # Database migrations
├── frontend/
│   └── src/
│       ├── app/[locale]/    # Next.js routes (marketing + dashboard)
│       ├── components/      # Chat UI, RAG panel, billing, admin
│       ├── hooks/           # useAuth, useChat, useConversations
│       └── stores/          # Zustand state management
├── docker-compose.dev.yml   # Development stack
├── docker-compose.yml       # Staging stack
├── docker-compose.prod.yml  # Production stack (Traefik TLS)
└── nginx/                   # Nginx reverse proxy config
```

---

## 🛠️ Development Commands

```bash
# Code quality
make lint          # ruff check + format check + ty type check
make format        # Auto-format (ruff)
make test          # Run pytest

# Database
make db-migrate    # Generate new Alembic migration
make db-upgrade    # Apply pending migrations
make db-downgrade  # Roll back one migration

# RAG
uv run supportiq rag-ingest /path/to/docs --collection support
uv run supportiq rag-search "refund policy" --collection support
uv run supportiq rag-collections

# Users / Admin
make create-admin
make user-list

# Celery
make celery-worker
make celery-beat
make celery-flower   # Flower at http://localhost:5555
```

---

## 🌍 Deployment

### Backend — Docker (VPS / Cloud VM)

```bash
cp backend/.env.example backend/.env  # fill in production secrets
make prod          # Traefik TLS + production Docker stack
make prod-logs
```

### Frontend — Vercel

```bash
cd frontend && npx vercel --prod
```

Set in Vercel dashboard:
- `BACKEND_URL` = `https://api.your-domain.com`
- `BACKEND_WS_URL` = `wss://api.your-domain.com`
- `NEXT_PUBLIC_AUTH_ENABLED` = `true`
- `NEXT_PUBLIC_RAG_ENABLED` = `true`

---

## 📚 Documentation

| Guide | Topic |
|---|---|
| [`ENV_VARS.md`](ENV_VARS.md) | Full environment variable reference |
| [`MANUAL_STEPS.md`](MANUAL_STEPS.md) | One-time setup (OAuth, Stripe, email, Milvus cloud) |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Architecture rules, code style, PR checklist |
| [`SECURITY.md`](SECURITY.md) | Vulnerability reporting, security model |
| [`docs/architecture.md`](docs/architecture.md) | Layered architecture deep-dive |
| [`docs/howto/`](docs/howto/) | How-to guides (add endpoint, add tool, add RAG source…) |

---

## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for setup, code style, architecture rules, and the PR checklist.

---

## 📄 License

MIT © [devkhurana02](https://github.com/devkhurana02)

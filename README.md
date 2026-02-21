# Marval-AI-Project

> **AI-Powered Teaching Assistant** — Real-time employee learning, onboarding automation, and intelligent knowledge retrieval.

---

## Overview

Marval-AI is a personalized AI teaching assistant built for internal company use. It answers employee questions in real time, automates onboarding flows, surfaces relevant knowledge from company systems, and delivers personalized skill recommendations — all through a clean, intuitive chat interface.

**Key numbers from our prior implementation (Reality AI Lab):**
- 5,000+ student queries automated
- 40% reduction in instructor response time
- Sub-500ms average response latency

---

## Architecture

```
User Query
   │
   ▼
Next.js Frontend  ──────────────────────────────────┐
   │                                                │
   ▼                                                │
FastAPI Backend                                     │
   │                                                │
   ├── Query Classifier                             │
   │       │                                        │
   ├── RAG Chain (LangChain + LlamaIndex)           │
   │       ├── Redis Vector Search (retrieval)      │
   │       ├── OpenAI Embeddings                    │
   │       └── GPT-4o (generation)                 │
   │                                                │
   ├── Feedback Service → PostgreSQL               │
   └── User/Auth Service → PostgreSQL ◄────────────┘
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14, React, Tailwind CSS, shadcn/ui |
| Backend | FastAPI (Python 3.11+) |
| LLM Orchestration | LangChain, LlamaIndex |
| LLM Provider | OpenAI GPT-4o |
| Embeddings | OpenAI text-embedding-3-small |
| Vector Store | Redis Vector Search |
| Database | PostgreSQL (Supabase) |
| Auth | NextAuth.js |
| Infra | Docker, Vercel (frontend), AWS ECS (backend) |
| Monitoring | LangSmith, Sentry |
| CI/CD | GitHub Actions |

---

## Project Structure

```
marval-ai-project/
├── apps/
│   ├── web/                     # Next.js 14 frontend
│   │   ├── app/
│   │   │   ├── (auth)/          # Login / signup pages
│   │   │   ├── chat/            # Main chat interface
│   │   │   ├── learn/           # Learning modules
│   │   │   ├── admin/           # Admin dashboard
│   │   │   └── api/             # Next.js API routes (BFF)
│   │   ├── components/
│   │   │   ├── chat/            # ChatWindow, MessageBubble, etc.
│   │   │   ├── learn/           # LearningCard, SkillBadge, etc.
│   │   │   └── ui/              # Shared UI primitives
│   │   ├── lib/
│   │   │   ├── api.ts           # API client
│   │   │   └── hooks/           # Custom React hooks
│   │   └── public/
│   │
│   └── api/                     # FastAPI backend
│       ├── main.py              # App entrypoint
│       ├── routers/
│       │   ├── chat.py          # /chat endpoint
│       │   ├── feedback.py      # /feedback endpoint
│       │   ├── ingest.py        # /ingest endpoint (admin)
│       │   └── auth.py          # /auth endpoint
│       ├── services/
│       │   ├── rag/
│       │   │   ├── chain.py     # RAG pipeline (LangChain LCEL)
│       │   │   ├── retriever.py # Redis vector retrieval
│       │   │   └── reranker.py  # Cross-encoder re-ranking
│       │   ├── ingestion/
│       │   │   ├── loader.py    # Document loaders (PDF, Notion, etc.)
│       │   │   ├── chunker.py   # Text chunking strategies
│       │   │   └── embedder.py  # Embedding + indexing
│       │   └── feedback/
│       │       └── service.py   # Feedback logging & analysis
│       └── models/
│           ├── chat.py          # ChatRequest/Response schemas
│           └── feedback.py      # FeedbackEvent schema
│
├── packages/
│   └── shared/                  # Shared TypeScript types
│       └── types.ts
│
├── infra/
│   ├── docker-compose.yml       # Local dev stack
│   ├── docker-compose.prod.yml  # Production overrides
│   └── terraform/               # Cloud infrastructure (IaC)
│       ├── main.tf
│       ├── variables.tf
│       └── modules/
│
├── docs/
│   ├── architecture.md          # This file's details
│   ├── api.md                   # API reference
│   └── deployment.md            # Deployment guide
│
├── scripts/
│   ├── ingest_docs.py           # Bulk document ingestion
│   └── seed_knowledge.py        # Seed dev knowledge base
│
└── .github/
    └── workflows/
        ├── ci.yml               # Lint, test, build
        └── deploy.yml           # Deploy on merge to main
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- Python 3.11+
- Docker & Docker Compose
- OpenAI API Key
- Redis (via Docker or Upstash)

### Quick Start

```bash
# Clone the repo
git clone https://github.com/your-org/marval-ai-project.git
cd marval-ai-project

# Start local infrastructure (Redis, PostgreSQL)
docker-compose up -d

# Install frontend dependencies
cd apps/web
npm install
cp .env.example .env.local  # Add your keys

# Install backend dependencies
cd ../api
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env        # Add your keys

# Run both services
# Terminal 1 — Backend
cd apps/api && uvicorn main:app --reload --port 8000

# Terminal 2 — Frontend
cd apps/web && npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## Environment Variables

### Frontend (`apps/web/.env.local`)
```env
NEXTAUTH_SECRET=your_secret
NEXTAUTH_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:8000
```

### Backend (`apps/api/.env`)
```env
OPENAI_API_KEY=sk-...
REDIS_URL=redis://localhost:6379
DATABASE_URL=postgresql://user:pass@localhost:5432/marval
LANGCHAIN_API_KEY=ls__...      # Optional: LangSmith tracing
LANGCHAIN_TRACING_V2=true
```

---

## Roadmap

| Phase | Timeline | Focus |
|---|---|---|
| Phase 1 | Weeks 1–3 | Scaffold, auth, basic chat + RAG PoC |
| Phase 2 | Weeks 4–7 | Knowledge ingestion, full RAG chain, feedback |
| Phase 3 | Weeks 8–10 | Learning modules, skill recommendations |
| Phase 4 | Weeks 11–13 | Integrations (Confluence, Notion, SSO), perf testing |
| Phase 5 | Week 14+ | Pilot rollout → v1 production |

---

## Contributing

1. Branch from `main`: `git checkout -b feat/your-feature`
2. Make changes + add tests
3. Run `npm run lint && npm run test` (frontend) / `pytest` (backend)
4. Open a PR against `main`

---

## License

Internal use only. © Marval AI Project Team.

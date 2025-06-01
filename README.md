# ⚖️ JurisMind — Indian Legal AI Assistant

[![FastAPI](https://img.shields.io/badge/FastAPI-0.109-009688?style=flat&logo=fastapi)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=flat&logo=react)](https://react.dev)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat&logo=python)](https://python.org)
[![LangChain](https://img.shields.io/badge/LangChain-0.1-1C3C3C?style=flat)](https://langchain.com)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat&logo=docker)](https://docker.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **AI-powered legal research assistant** grounded in Indian statutory law. Retrieval-Augmented Generation (RAG) ensures every answer is cited from authoritative legal texts — no hallucinations, no guesswork.

🌐 **Live Demo:** [https://jurismind.app](https://jurismind.app) · 📄 [Technical Docs](docs/RAG_System_Technical_Document.md) · 🐛 [Report Bug](../../issues)

---

## 📖 Overview

JurisMind is a full-stack legal AI chatbot that answers questions about Indian law using a curated corpus of statutes and codes. The system uses semantic search over FAISS vector indexes to retrieve the most relevant legal provisions before generating an answer, eliminating hallucinated legal advice.

**Legal Corpus:**
- Indian Penal Code (IPC)
- Code of Criminal Procedure (CrPC)
- Code of Civil Procedure (CPC)
- Constitution of India
- Indian Evidence Act
- Various Central Acts & Regulations

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔍 **Semantic Search** | FAISS vector index with HuggingFace embeddings (`all-MiniLM-L6-v2`) |
| 🧠 **RAG Pipeline** | Query → Translate → Enhance → Embed → Retrieve → Generate → Format |
| 📌 **Source Citations** | Every answer references exact sections from the legal corpus |
| 💬 **Conversation Memory** | Persistent multi-turn chat with session management |
| 🌐 **Multilingual** | Query in regional Indian languages; translated internally |
| 📄 **Document Upload** | Upload custom legal documents to extend the knowledge base |
| 🔐 **Auth System** | JWT access tokens + refresh tokens, bcrypt password hashing |
| 🛡️ **Anti-Hallucination** | Retrieval grounding prevents fabricated legal citations |
| 📱 **Mobile App** | React Native (Expo) for iOS & Android |
| 🔒 **Security Hardened** | Rate limiting, account lockout, OWASP headers, AES-256 encryption |
| 👤 **Admin Panel** | User management, usage analytics, system health monitoring |

---

## 🏗️ Architecture

```
┌─────────────┐     ┌─────────────┐     ┌──────────────────────────────┐
│   Browser   │────▶│    Nginx    │────▶│        FastAPI Backend        │
│  React SPA  │     │  (Reverse   │     │  ┌────────────────────────┐  │
│             │     │   Proxy)    │     │  │      RAG Pipeline       │  │
└─────────────┘     └─────────────┘     │  │  Query → Embed → Search │  │
                                        │  │  Retrieve → LLM → Cite  │  │
┌─────────────┐     ┌─────────────┐     │  └────────┬───────────────┘  │
│  Expo App   │────▶│  REST API   │────▶│           │                   │
│ iOS/Android │     │  (JWT Auth) │     └───────────┼───────────────────┘
└─────────────┘     └─────────────┘                 │
                                          ┌──────────┼──────────┐
                                          ▼          ▼          ▼
                                       FAISS      Groq LLM  PostgreSQL
                                    (Vectors)   (llama-3.1)  + Redis
```

---

## 🛠️ Tech Stack

### Backend
| Component | Technology |
|---|---|
| Framework | FastAPI 0.109 |
| LLM | Groq `llama-3.1-8b-instant` (configurable) |
| Embeddings | HuggingFace `sentence-transformers/all-MiniLM-L6-v2` |
| Vector Store | FAISS (local) + pgvector (PostgreSQL) |
| Orchestration | LangChain 0.1 |
| Database | PostgreSQL |
| Cache / Sessions | Redis |
| Auth | JWT (PyJWT) + bcrypt |
| Encryption | AES-256 (cryptography) |

### Frontend
| Component | Technology |
|---|---|
| Framework | React 19 + Vite 7 |
| Routing | React Router 7 |
| Icons | Lucide React |
| Styling | Vanilla CSS |

### Mobile
| Component | Technology |
|---|---|
| Framework | React Native + Expo SDK |
| Navigation | Expo Router |
| Build | EAS Build |
| Bundle ID (iOS) | `app.jurismind.mobile` |
| Bundle ID (Android) | `app.jurismind.mobile` |

### Infrastructure
| Component | Technology |
|---|---|
| Containerization | Docker + Docker Compose |
| Reverse Proxy | Nginx |
| CI/CD | GitHub Actions (optional) |
| Secrets | Environment variables (fail-fast validation on startup) |

---

## 📁 Project Structure

```
LegalGpt-main/
├── app/                        # FastAPI backend
│   ├── main.py                 # App factory, startup, middleware
│   ├── config.py               # Pydantic settings (env-driven)
│   ├── routes/                 # API route handlers
│   │   ├── auth.py             # Login, register, refresh
│   │   ├── chat.py             # Chat completions, history
│   │   ├── documents.py        # Document upload & indexing
│   │   ├── admin.py            # Admin endpoints
│   │   └── health.py           # Liveness & readiness probes
│   ├── core/                   # Business logic
│   │   ├── rag_pipeline.py     # End-to-end RAG orchestration
│   │   ├── embeddings.py       # Embedding model wrapper
│   │   ├── vector_store.py     # FAISS + pgvector abstraction
│   │   ├── llm.py              # LLM client (Groq / OpenAI)
│   │   └── security.py         # JWT, encryption helpers
│   └── models/                 # SQLAlchemy ORM models
├── frontend/                   # React + Vite SPA
│   ├── src/
│   │   ├── App.jsx             # Root component & routing
│   │   ├── components/         # Reusable UI components
│   │   └── pages/              # Page-level components
│   ├── public/                 # Static assets
│   └── package.json
├── mobile_app/                 # Expo React Native app
│   ├── app/                    # Expo Router file-based routes
│   └── app.json                # Expo config (bundle IDs, icons)
├── data/
│   └── faiss_index/            # ⚠️ Gitignored — rebuild locally
├── docs/
│   └── RAG_System_Technical_Document.md
├── scripts/                    # Utility & maintenance scripts
├── nginx/                      # Nginx config for production
├── docker-compose.yml          # Multi-service orchestration
├── Dockerfile                  # Multi-stage backend image
├── run.py                      # Local dev entrypoint
├── requirements.txt
├── .env.example                # ← Copy to .env and fill in
└── .gitignore
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.11+
- Node.js 20+
- Docker & Docker Compose
- [Groq API Key](https://console.groq.com/) (free tier available)
- PostgreSQL (local or managed)
- Redis

### Option 1 — Local Development

```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/LegalGpt.git
cd LegalGpt

# 2. Configure environment
cp .env.example .env
# Edit .env — fill in GROQ_API_KEY, DATABASE_URL, and secrets

# 3. Set up Python environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

# 4. Start the backend
python run.py
# API available at http://localhost:8000
# Health check: http://localhost:8000/health/live

# 5. Start the frontend (new terminal)
cd frontend
npm install
npm run dev
# UI available at http://localhost:5173
```

### Option 2 — Docker Compose (Recommended for Production)

```bash
# 1. Configure environment
cp .env.example .env
# Edit .env — fill in all required values

# 2. Generate strong secrets
python -c "import secrets; print(secrets.token_hex(32))"   # JWT_SECRET_KEY
python -c "import secrets; print(secrets.token_hex(32))"   # ENCRYPTION_KEY
python -c "import secrets; print(secrets.token_hex(32))"   # API_SIGNING_KEY

# 3. Start the full stack
docker compose up -d --build

# 4. Initialize the vector index (first run only)
docker compose exec backend python scripts/build_index.py
```

Services:
- **Frontend:** http://localhost (via Nginx)
- **Backend:** http://localhost:8000 (internal, proxied through Nginx)
- **Health:** http://localhost:8000/health/live

---

## ⚙️ Environment Variables

Copy `.env.example` to `.env` and configure all values before running.

| Variable | Required | Default | Description |
|---|---|---|---|
| `GROQ_API_KEY` | ✅ | — | Groq LLM API key |
| `LLM_PROVIDER` | | `groq` | LLM provider (`groq` / `openai`) |
| `LLM_MODEL` | | `llama-3.1-8b-instant` | Model name |
| `LLM_TEMPERATURE` | | `0.0` | Generation temperature |
| `DATABASE_URL` | ✅ | — | PostgreSQL connection string |
| `REDIS_URL` | | `redis://localhost:6379` | Redis connection URL |
| `JWT_SECRET_KEY` | ✅ prod | — | JWT signing secret (min 32 chars) |
| `JWT_ALGORITHM` | | `HS256` | JWT algorithm |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | | `30` | Access token TTL |
| `REFRESH_TOKEN_EXPIRE_DAYS` | | `7` | Refresh token TTL |
| `ENCRYPTION_KEY` | ✅ prod | — | AES-256 key for sensitive data |
| `API_SIGNING_KEY` | ✅ prod | — | HMAC key for API signatures |
| `FAISS_INDEX_PATH` | | `./data/faiss_index` | FAISS vector index directory |
| `EMBEDDING_MODEL` | | `sentence-transformers/all-MiniLM-L6-v2` | HuggingFace embedding model |
| `APP_ENV` | | `development` | `development` / `production` |
| `ALLOWED_ORIGINS` | | `*` | Comma-separated CORS origins |
| `MAX_UPLOAD_SIZE_MB` | | `10` | Max document upload size |
| `RATE_LIMIT_REQUESTS` | | `100` | Requests per window |
| `RATE_LIMIT_WINDOW_SECONDS` | | `60` | Rate limit window |

> **⚠️ Production:** `JWT_SECRET_KEY`, `ENCRYPTION_KEY`, and `API_SIGNING_KEY` **must** be set — the app will refuse to start without them.

---

## 📡 API Reference

Interactive API docs (Swagger UI) are available at `/docs` in development mode only.

### Core Endpoints

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/auth/register` | — | Create a new user account |
| `POST` | `/api/auth/login` | — | Obtain JWT access + refresh tokens |
| `POST` | `/api/auth/refresh` | Refresh token | Rotate access token |
| `POST` | `/api/chat/completions` | Bearer | Send a legal query, receive cited answer |
| `GET` | `/api/chat/history` | Bearer | Retrieve conversation history |
| `DELETE` | `/api/chat/history/{id}` | Bearer | Delete a conversation |
| `POST` | `/api/documents/upload` | Bearer | Upload a document for indexing |
| `GET` | `/api/admin/users` | Admin | List all users |
| `GET` | `/api/admin/stats` | Admin | System usage statistics |
| `GET` | `/health/live` | — | Liveness probe |
| `GET` | `/health/ready` | — | Readiness probe |

### Example — Legal Query

```bash
curl -X POST https://jurismind.app/api/chat/completions \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"message": "What is the punishment for theft under IPC?"}'
```

```json
{
  "answer": "Under Section 379 of the Indian Penal Code, theft is punishable with imprisonment of either description for a term which may extend to three years, or with fine, or with both.",
  "sources": [
    {"section": "IPC § 379", "title": "Punishment of theft", "relevance": 0.94}
  ],
  "session_id": "abc123"
}
```

---

## 🔄 RAG Pipeline

```
User Query
    │
    ▼
[1] Language Detection & Translation
    │  (regional languages → English)
    ▼
[2] Query Enhancement
    │  (expand legal abbreviations, add context)
    ▼
[3] Embedding Generation
    │  (HuggingFace all-MiniLM-L6-v2, 384-dim)
    ▼
[4] Vector Retrieval
    │  (FAISS cosine similarity, top-k chunks)
    ▼
[5] Context Assembly
    │  (ranked chunks + conversation history)
    ▼
[6] LLM Generation
    │  (Groq llama-3.1-8b-instant, temp=0.0)
    ▼
[7] Response Formatting
       (structured answer + source citations)
```

---

## 📱 Mobile App

```bash
cd mobile_app
npm install

# Start Expo dev server
npx expo start

# Build for production
npx eas build --platform ios
npx eas build --platform android
```

| Platform | Bundle ID |
|---|---|
| iOS | `app.jurismind.mobile` |
| Android | `app.jurismind.mobile` |

---

## 📜 Scripts

| Script | Description |
|---|---|
| `scripts/build_index.py` | Build / rebuild the FAISS vector index from the legal corpus |
| `scripts/ingest_documents.py` | Ingest new documents into the vector store |
| `scripts/create_admin.py` | Create an admin user account |
| `scripts/backup_db.py` | Backup PostgreSQL database |
| `run.py` | Local development server entrypoint |

---

## 🔒 Security

| Control | Implementation |
|---|---|
| Authentication | JWT access + refresh tokens (HS256) |
| Password Hashing | bcrypt with auto-generated salt |
| Account Lockout | Configurable failed-login threshold |
| Rate Limiting | Per-IP request throttling |
| HTTP Headers | OWASP recommended headers (X-Frame-Options, X-XSS-Protection, etc.) |
| Content Security Policy | CSP header restricting resource origins |
| HSTS | HTTP Strict Transport Security in production |
| Data Encryption | AES-256 for sensitive fields at rest |
| Container Hardening | Non-root `jurismind` user, read-only rootfs, tmpfs mounts |
| Secrets Validation | App refuses to start if production secrets are missing |
| API Docs | Swagger UI disabled in `APP_ENV=production` |

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit your changes using [Conventional Commits](https://conventionalcommits.org):
   - `feat:` new feature
   - `fix:` bug fix
   - `docs:` documentation only
   - `chore:` maintenance
4. Push to your branch and open a Pull Request

Please ensure:
- All backend changes include appropriate tests
- API changes update the relevant docstrings
- No secrets are committed (`.env` is gitignored)

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## ⚠️ Legal Disclaimer

> JurisMind provides **legal information**, not **legal advice**. The AI-generated answers are based on statutory text and should not be relied upon as a substitute for consultation with a qualified legal professional. Laws may be amended; always verify with current official sources.

---

<div align="center">
  Made with ❤️ for access to justice in India
</div>


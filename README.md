# Day 25: AI System Architecture Review and Refactoring

## 📌 Overview
Audit, modularization, and refactoring of an end-to-end RAG / FastAPI AI system pipeline. Focuses on code quality, eliminating technical debt, centralized configuration, and regression test harnesses.

## 🏗️ Architecture Diagram
```text
[ Client / User Request ]
           │
           ▼
┌────────────────────────────────────────────────────────┐
│               FastAPI API Gateway                      │
│  - POST /query                                         │
│  - Health Check /docs                                  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│         Input Validation & Guardrails Layer            │
│  - Pydantic schema validation (QueryRequest)           │
│  - Query sanitization, length & empty checks           │
└──────────────────────────┬─────────────────────────────┘
                           │
            ┌──────────────┴──────────────┐
            ▼                             ▼
┌───────────────────────────┐  ┌─────────────────────────┐
│     config.py / .env      │  │   Vector Store (FAISS)  │
│  - Settings (pydantic)    │  │  - text-embedding-3-small│
│  - Top-K, thresholds, keys│  │  - similarity search    │
└───────────────────────────┘  └────────────┬────────────┘
                                            │
                                            ▼ Retrieved Chunks
┌────────────────────────────────────────────────────────┐
│                 Prompt Builder Engine                  │
│  - Context formatting & injection                      │
│  - System instructions & fallback framing              │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ Formatted Prompt
┌────────────────────────────────────────────────────────┐
│              LLM Generation & Response                 │
│  - OpenAI / Vertex API Client                          │
│  - Output parsing & latency instrumentation            │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
                 [ JSON Response Output ]

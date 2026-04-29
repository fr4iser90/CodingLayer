# CodingLayer

Enterprise-grade Code Intelligence & Retrieval System for Local AI Agents

CodingLayer is a standalone service responsible for code understanding, indexing, semantic retrieval, dependency resolution, and context generation for LLM-powered agents running locally with systems like Ollama.

It is designed to work independently from the Agent Layer and acts as the “knowledge engine” for large codebases.

Instead of sending entire repositories to an LLM, CodingLayer provides precise, dependency-aware, version-aware context retrieval.

---

# Goals

* Local-first architecture
* Full Ollama compatibility
* Enterprise scalability
* Multi-repository support
* Incremental indexing
* AST-based parsing
* Semantic code retrieval
* Dependency-aware context building
* Permission-aware access
* Version-aware code snapshots
* High-performance vector search
* Production-ready API integration

---

# Core Philosophy

LLMs should not read entire repositories.

LLMs should receive:

* the right files
* the right functions
* the right dependencies
* the right summaries
* the right commit version

Retrieval quality matters more than model size.

---

# System Architecture

```text
Git Repository
    ↓
Watcher Layer (Git Hooks / FS Watcher / CI)
    ↓
Parser Layer (tree-sitter)
    ↓
Chunking + Symbol Extraction
    ↓
Summary Generator
    ↓
Embedding Pipeline
    ↓
PostgreSQL + pgvector
    ↓
Retrieval Layer
    ↓
Context Builder
    ↓
FastAPI Service
    ↓
Agent Layer
    ↓
Ollama
```

---

# Tech Stack

---

## Core Backend

### FastAPI

Used for:

* Retrieval API
* Indexing API
* Symbol Resolution
* Context Generation
* Repository Management
* Permission Validation

Reason:

* async support
* production-ready
* OpenAPI support
* easy service separation

---

## Database

### PostgreSQL

Used for:

* repositories
* files
* symbols
* summaries
* metadata
* index jobs
* code snapshots
* permissions
* audit trails

### pgvector

Used for:

* semantic embeddings
* nearest-neighbor retrieval
* vector similarity search

Reason:

Vector search should live close to metadata.

---

## Parsing Engine

### tree-sitter

Used for:

* AST parsing
* symbol extraction
* dependency analysis
* import resolution
* class/function detection
* multi-language support

Supported languages:

* Python
* TypeScript
* JavaScript
* Go
* Rust
* Java
* C#
* more later

Reason:

Regex is not reliable for enterprise parsing.

---

## Background Workers

### Celery

Used for:

* indexing jobs
* re-indexing
* embedding refresh
* summary generation
* batch parsing

Alternative:

* Dramatiq
* Temporal

---

## Queue + Cache

### Redis

Used for:

* task queue broker
* retrieval cache
* context cache
* temporary indexing states

Reason:

Required for scalable background processing.

---

## LLM Runtime

### Ollama

Used for:

* summary generation
* code understanding
* retrieval augmentation
* local inference

Recommended models:

* DeepSeek Coder
* Qwen Coder
* Codestral
* StarCoder
* Llama variants

Reason:

Local-first architecture.

---

## Optional Graph Layer

### Neo4j

Used for:

* dependency graphs
* caller/callee tracing
* architecture relationships

Optional for large enterprise systems.

Not required in MVP.

---

## Security Layer

### Vault / Infisical / SOPS

Used for:

* secrets management
* API credentials
* embedding provider keys (if hybrid mode exists)

Never use raw .env files for production.

---

# Project Structure

```text
coding-layer/
│
├── app/
│   ├── api/
│   ├── core/
│   ├── parser/
│   ├── retrieval/
│   ├── embeddings/
│   ├── summaries/
│   ├── context_builder/
│   ├── permissions/
│   ├── workers/
│   ├── repositories/
│   ├── models/
│   ├── database/
│   └── utils/
│
├── migrations/
│
├── docker/
│
├── scripts/
│
├── tests/
│
├── docs/
│
├── docker-compose.yml
│
├── shell.nix
│
├── pyproject.toml
│
└── README.md
```

---

# Separation from Agent Layer

CodingLayer should NOT share the same database as the Agent Layer.

Recommended:

## Agent Layer DB

Handles:

* users
* sessions
* workflows
* tools
* audit logs
* notifications

## CodingLayer DB

Handles:

* repositories
* files
* symbols
* embeddings
* summaries
* dependency graph
* snapshots
* indexing jobs

Reason:

Vector search and indexing workloads should never impact runtime agent operations.

Isolation is required for enterprise reliability.

---

# Enterprise Features

---

## Incremental Indexing

Only changed files should be re-indexed.

Never full re-index unless explicitly requested.

Supports:

* post-commit hooks
* post-merge hooks
* Git webhooks
* CI-based refresh

---

## Dependency-Aware Retrieval

When retrieving a symbol:

also retrieve:

* imports
* parent classes
* interfaces
* related tests
* configuration files
* environment references
* callers
* callees

This is mandatory for high-quality LLM responses.

---

## Version-Aware Snapshots

Every retrieval must know:

* repository
* branch
* commit hash

Never retrieve code without version context.

---

## Permission Layer

Supports:

* repository-level access
* folder-level access
* symbol-level access
* user-level access
* agent-level access

Enterprise systems require strict access boundaries.

---

## Audit Layer

Track:

* who requested context
* what was retrieved
* which repository was accessed
* which commit was used
* which agent triggered retrieval

Required for enterprise compliance.

---

# Development Philosophy

This is not “just another AI helper.”

This is infrastructure.

The system must be:

* predictable
* reproducible
* secure
* observable
* scalable

Reliability matters more than flashy demos.

---

# MVP Build Order

## Phase 1

* PostgreSQL
* pgvector
* tree-sitter
* FastAPI
* basic indexing
* embeddings
* retrieval

## Phase 2

* summaries
* incremental indexing
* context builder

## Phase 3

* permissions
* audit trails
* multi-user support
* snapshots

## Phase 4

* full agent integration
* autonomous workflows
* advanced orchestration

---

# Final Principle

Bad retrieval kills good models.

Good retrieval makes average models feel powerful.

CodingLayer is a retrieval system first.

Everything else comes second.

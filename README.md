# FargoSOCIAL — Social Network Backend API

> A production-style backend service in Go, built to demonstrate clean architecture, relational data modeling, and modern backend engineering practices.

**Author:** [Farhan Alam](https://github.com/FarhanAlam2001)  
**Repository:** [github.com/FarhanAlam2001/social](https://github.com/FarhanAlam2001/social)  
**Language:** Go 1.26.5

---

## At a Glance

| | |
|---|---|
| **What it is** | RESTful backend API for a social network platform |
| **Current scope** | HTTP server, PostgreSQL persistence, versioned migrations, user/post data layer |
| **Architecture** | Layered (API → Store → Database) with interface-driven design |
| **Infra** | Docker Compose (PostgreSQL + Redis), Makefile automation, hot reload via Air |

---

## Why This Project

This project showcases how I approach backend development beyond tutorials — with **structure, scalability, and maintainability** in mind from day one.

Rather than a single-file demo, SOCIAL follows patterns used in real-world services:

- Separation of concerns across `cmd/`, `internal/`, and migration layers
- Interface-based storage layer for testability and future swapping
- Version-controlled database schema with up/down migrations
- Configurable connection pooling and production-minded HTTP server settings
- Standard middleware stack for observability, safety, and request lifecycle control

It is actively evolving toward a full social platform (auth, feeds, caching), with a solid foundation already in place.

---

## Skills Demonstrated

**Languages & Frameworks**  
`Go` · `REST APIs` · `chi router` · `net/http`

**Databases & Data**  
`PostgreSQL` · `SQL` · `Schema Design` · `Foreign Keys` · `citext` · `Connection Pooling` · `database/sql`

**DevOps & Tooling**  
`Docker` · `Docker Compose` · `Make` · `golang-migrate` · `Air` · `direnv` · Environment-based config

**Software Engineering**  
`Clean Architecture` · `Layered Design` · `Interface Segregation` · `Context-aware DB ops` · `Middleware` · `Migration Strategy`

---

## Key Highlights

- **Clean project layout** — Standard Go project structure (`cmd/`, `internal/`) that scales as features grow
- **Production-ready HTTP server** — Configurable read/write/idle timeouts, chi middleware (logging, recovery, request ID, 60s timeout)
- **Database-first design** — Relational schema for users and posts with foreign key integrity
- **Migration workflow** — Sequential SQL migrations with Makefile commands for up, down, and create
- **Store abstraction** — `Storage` struct exposes typed interfaces for Users and Posts, decoupling handlers from SQL
- **Developer experience** — Hot reload with Air, `.envrc` for local config, Docker Compose for one-command DB setup

---

## Architecture

```mermaid
flowchart TB
    Client[HTTP Client] --> API[cmd/api<br/>chi Router + Middleware]
    API --> Handlers[HTTP Handlers<br/>health, future routes]
    Handlers --> Store[internal/store<br/>UsersStore · PostStore]
    Store --> DB[internal/db<br/>Connection Pool]
    DB --> PG[(PostgreSQL 16)]
    
    subgraph DevOps
        Docker[Docker Compose]
        Migrate[golang-migrate]
        Make[Makefile]
    end
    
    Docker --> PG
    Migrate --> PG
    Make --> Migrate
```

### Layer Responsibilities

| Layer | Package | Role |
|-------|---------|------|
| **Entry** | `cmd/api` | Bootstraps config, DB, store, router, and HTTP server |
| **Config** | `internal/env` | Reads env vars with sensible defaults |
| **Database** | `internal/db` | Opens PostgreSQL pool, pings on startup, tunes pool settings |
| **Data Access** | `internal/store` | CRUD operations for Users and Posts via interfaces |
| **Schema** | `cmd/migrate/migrations` | Versioned SQL migrations (up/down) |

---

## Tech Stack

| Category | Tools |
|----------|-------|
| **Backend** | Go 1.26.5, chi/v5 |
| **Database** | PostgreSQL 16, lib/pq |
| **Caching (planned)** | Redis 6.2 |
| **Migrations** | golang-migrate |
| **Dev Tools** | Air, Make, direnv |
| **Containers** | Docker Compose |

---

## Data Model

### Users
- Unique email (`citext`) and username
- Secure password storage (`bytea`)
- Auto-managed timestamps

### Posts
- Title and content
- Linked to users via foreign key (`user_id → users.id`)
- Timestamps for creation tracking

Schema is managed through three sequential migrations with full rollback support.

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/v1/health` | Service health check — returns `OK` |

> Additional endpoints (user registration, post creation, auth) are supported at the store layer and planned for the HTTP API surface.

---

## Project Structure

```
SOCIAL/
├── cmd/
│   ├── api/                 # HTTP server entry point
│   └── migrate/migrations/  # Versioned SQL migrations
├── internal/
│   ├── db/                  # PostgreSQL connection pool
│   ├── env/                 # Environment configuration
│   └── store/               # Data access (users, posts)
├── scripts/                 # DB initialization scripts
├── docker-compose.yml       # PostgreSQL + Redis
├── Makefile                 # Migration automation
├── .air.toml                # Hot reload config
└── .envrc                   # Local environment variables
```

---

## Quick Start

For reviewers and recruiters who want to run the project locally:

```bash
# 1. Clone
git clone https://github.com/FarhanAlam2001/social.git
cd social

# 2. Start database services
docker compose up -d

# 3. Load environment
source .envrc

# 4. Apply migrations
make migrate-up

# 5. Run the API
go run ./cmd/api
# or with hot reload: air
```

**Verify it works:**

```bash
curl http://localhost:8080/v1/health
# Expected: OK
```

### Environment Variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `ADDR` | `:8080` | Server listen address |
| `DB_ADDR` | `postgres://admin:adminpassword@localhost/socialnetwork?sslmode=disable` | PostgreSQL DSN |
| `DB_MAX_OPEN_CONNS` | `30` | Connection pool size |
| `DB_MAX_IDLE_CONNS` | `30` | Idle connections |
| `DB_MAX_IDLE_TIME` | `15m` | Max idle connection lifetime |

---

## Engineering Decisions

| Decision | Rationale |
|----------|-----------|
| **chi over Gin/Echo** | Lightweight, idiomatic `net/http` compatibility, excellent middleware ecosystem |
| **Interface-based store** | Enables mocking in tests and swapping implementations without touching handlers |
| **SQL migrations over ORM** | Full control over schema, explicit indexes/constraints, easy DBA review |
| **citext for email** | Case-insensitive uniqueness without application-level normalization |
| **Connection pool tuning** | Prevents connection exhaustion under load; configurable via env vars |
| **Docker Compose** | Reproducible local environment for anyone reviewing the project |

---

## Roadmap

- [ ] User registration & authentication (JWT)
- [ ] Post CRUD HTTP endpoints
- [ ] Redis caching layer
- [ ] Input validation & structured error responses
- [ ] Unit & integration tests
- [ ] API documentation (OpenAPI/Swagger)

---

## Makefile Reference

| Command | Description |
|---------|-------------|
| `make migration <name>` | Create a new migration file |
| `make migrate-up` | Apply all pending migrations |
| `make migrate-down` | Roll back the last migration |

---

## Contact

**Farhan Alam** — [GitHub](https://github.com/FarhanAlam2001)
                - [Portfolio-Website](https://farhanalam2001.github.io/Farhans_PortFolio_Website/)

If you're reviewing this repo as part of a hiring process, the best place to start is `cmd/api/main.go` (bootstrap flow) and `internal/store/` (data layer design).

---

## License

This project is licensed under the [MIT License](LICENSE.md).


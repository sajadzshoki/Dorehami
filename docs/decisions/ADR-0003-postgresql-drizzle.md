# ADR-0003: PostgreSQL + Drizzle ORM

- **Status:** Proposed (validate versions in Phase 1)

## Context / problem
Relational integrity (memberships, registrations, capacity counters), full-text/trigram search for MVP, JSON flexibility for recurrence rules and notification payloads — in TypeScript.

## Options considered
1. **PostgreSQL + Drizzle** — typed schema-as-code, SQL-transparent, versioned migrations (ADR-0004).
2. PostgreSQL + Prisma — mature, but heavier runtime/codegen and less SQL transparency for locking patterns.
3. MySQL/MongoDB — weaker fit (relational invariants; PG search/jsonb) with no compensating benefit.

## Decision
Option 1. Use `timestamptz` UTC everywhere.

**Phase 0.1 evidence (npm registry + docker-library GitHub, 2026-10-14):** `drizzle-orm` 0.45.4 declares peer `pg >=8`; `pg` latest observed 8.23.1; `drizzle-kit` 0.31.11. The official `docker-library/postgres` tree currently maintains major directories 14–19; **recommendation: PostgreSQL 17 or 18 line for MVP (mature, not the newest major), exact minor + image digest pinned at Phase 1 bootstrap from the official image page and recorded here.** Choosing the newest major (19) for a first release is not justified without a Phase 1 restore/upgrade test. Phase 1 must also pick the driver (`pg` vs `postgres-js`) once and record it — peer ranges allow both; do not mix drivers.

## Rationale / consequences
Drizzle keeps the capacity-locking SQL explicit and testable, and its schema doubles as TypeScript types. Trade-offs: smaller plugin ecosystem than Prisma; team must be comfortable writing near-SQL. Constraint rules (partial unique indexes etc.) are defined in the schema, not only in app code. **Validation:** Drizzle version ↔ PG version ↔ Node/Nuxt runtime matrix recorded in Phase 1.

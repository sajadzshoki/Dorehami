# ADR-0003: PostgreSQL + Drizzle ORM

- **Status:** Proposed (validate versions in Phase 1)

## Context / problem
Relational integrity (memberships, registrations, capacity counters), full-text/trigram search for MVP, JSON flexibility for recurrence rules and notification payloads — in TypeScript.

## Options considered
1. **PostgreSQL + Drizzle** — typed schema-as-code, SQL-transparent, versioned migrations (ADR-0004).
2. PostgreSQL + Prisma — mature, but heavier runtime/codegen and less SQL transparency for locking patterns.
3. MySQL/MongoDB — weaker fit (relational invariants; PG search/jsonb) with no compensating benefit.

## Decision
Option 1. Pin exact PostgreSQL and Drizzle versions in Phase 1 after compatibility check; use `timestamptz` UTC everywhere.

## Rationale / consequences
Drizzle keeps the capacity-locking SQL explicit and testable, and its schema doubles as TypeScript types. Trade-offs: smaller plugin ecosystem than Prisma; team must be comfortable writing near-SQL. Constraint rules (partial unique indexes etc.) are defined in the schema, not only in app code. **Validation:** Drizzle version ↔ PG version ↔ Node/Nuxt runtime matrix recorded in Phase 1.

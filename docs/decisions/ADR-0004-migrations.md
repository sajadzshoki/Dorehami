# ADR-0004: Database migration & schema-versioning strategy

- **Status:** Proposed

## Context / problem
Schema must evolve safely on a production VPS with real registrations; rollbacks of data migrations are risky.

## Options considered
1. **Drizzle Kit generated, versioned, committed migrations; forward-only in production**, applied at deploy time as an explicit step; destructive changes via expand→migrate→contract across two releases.
2. Ad-hoc `push` to production — fast, unreviewable, forbidden.
3. Hand-written SQL only — full control, loses schema/type sync benefits.

## Decision
Option 1. Migrations are reviewed in PRs like code; each phase's implementation plan lists expected migrations; production deploys run migrations before app rollout with a pre-migration backup (DEPLOYMENT-AND-BACKUP-PLAN). Decide in Phase 1 whether `system_settings` survives (ERD note) and how migration execution ships under the no-custom-Dockerfile constraint (ADR-0018 open point).

## Failure & recovery behavior (added Phase 0.1)

- Migrations run as a **separate one-shot step/job before the new app version serves traffic** (ADR-0018 workflow), using a dedicated migration connection where feasible. The app must not start serving against a half-migrated schema: deploy order is backup → migrate → app rollout → smoke tests.
- Each Drizzle migration is applied in the order committed; PostgreSQL transactional DDL makes most migrations atomic, but any migration using non-transactional operations (e.g. `CREATE INDEX CONCURRENTLY`, certain enum changes) must be split, marked in review, and given an explicit manual recovery note in the migration PR.
- **On migration failure:** the job exits non-zero, deployment stops, the previous app version keeps serving (schema changes must be backward-compatible with it via expand/contract), and the operator either forward-fixes with a new migration or restores the pre-migration backup. Never edit an already-applied migration file; never mark migrations as applied by hand outside the documented Drizzle mechanism.
- **Recovery proof:** Phase 1 must demonstrate, on a throwaway database: apply-from-empty, apply-over-previous-version, induced failure leaves the previous version usable, and restore-from-backup. This is a Phase 1 acceptance item, not a launch-time discovery.

## Consequences
Reproducible environments, auditable schema history; discipline cost: no quick production schema edits, ever.

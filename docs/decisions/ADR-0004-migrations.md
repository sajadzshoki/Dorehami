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

## Consequences
Reproducible environments, auditable schema history; discipline cost: no quick production schema edits, ever.

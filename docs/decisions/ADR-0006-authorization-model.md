# ADR-0006: Capability + resource-scoped authorization (not a rigid global-role model)

- **Status:** Proposed

## Context / problem
One account must act as user, community member/manager, organizer, and (rarely) platform admin. A single global "role" cannot express "manager of community X but plain member of Y" (brief §7).

## Options considered
1. **Layered model:** (a) platform scope — `is_platform_admin` flag (ERD note) for platform-wide powers; (b) **organizer capability** — an approved/active capability record enabling event/community creation + organizer page; (c) **resource-scoped roles** — membership role per community, ownership per event. Every check happens server-side against the loaded resource.
2. Global RBAC roles only — cannot scope community management; forces over-privileged accounts.
3. Full ACL/per-object permission tables — flexible but overengineered for MVP.

## Decision
Option 1. **Granting:** organizer capability = self-service activation (creates organizer page) with verification/trust level gating *publication review*, not creation (DOMAIN-MODEL §2; thresholds Open Q-05). Admin may suspend capability with audit. **Admin protection:** platform admin granted only via CLI or by an existing admin through an audited, confirmed action; admin sessions stricter (SECURITY-BASELINE §3); every admin mutation writes an AuditLog entry (ADR-0016).

## Consequences
Authorization helpers per module (`canManageCommunity(user, community)` etc.) become mandatory, unit-tested building blocks; BOLA tests (T-04/T-05) verify them per endpoint. Trade-off: more checks to write than a role middleware — accepted, since the frontend never authorizes.

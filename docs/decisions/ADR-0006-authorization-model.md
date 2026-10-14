# ADR-0006: Capability + resource-scoped authorization (not a rigid global-role model)

- **Status:** Proposed

## Context / problem
One account must act as user, community member/manager, organizer, and (rarely) platform admin. A single global "role" cannot express "manager of community X but plain member of Y" (brief §7).

## Options considered
1. **Layered model:** (a) platform scope — `is_platform_admin` flag (ERD note) for platform-wide powers; (b) **organizer capability** — an approved/active capability record enabling event/community creation + organizer page; (c) **resource-scoped roles** — membership role per community, ownership per event. Every check happens server-side against the loaded resource.
2. Global RBAC roles only — cannot scope community management; forces over-privileged accounts.
3. Full ACL/per-object permission tables — flexible but overengineered for MVP.

## Decision
Option 1. **Granting:** organizer capability = self-service activation (creates organizer page) with verification/trust level gating *publication review*, not creation (DOMAIN-MODEL §2; thresholds Open D-04). Admin may suspend capability with audit. **Admin protection:** platform admin granted only via CLI or by an existing admin through an audited, confirmed action; admin sessions stricter (SECURITY-BASELINE §3); every admin mutation writes an AuditLog entry (ADR-0016).

## Permission matrix (normative summary, Phase 0.1 — full matrix in DOMAIN-MODEL §11)

- **Event:** only its organizer (owner) may edit/cancel/manage registrations; a linked community's managers get **no** implicit rights over the event unless one of them is the organizer. Admins may unpublish/cancel for moderation, audited.
- **Community:** managers may edit settings, approve/remove members, and moderate that community's posts; plain members may post/comment/react; non-members may only view public content; the founder is a manager, not a separate super-role.
- **Registration/attendee data:** the registrant manages their own registration; the organizer sees only the attendee view allowed by each attendee's privacy setting and never phone numbers; platform admins do not get bulk attendee export in MVP.
- **Direct messages:** only the two participants; blocking (recommended MVP, founder approval D-05) prevents new conversation initiation and further messages from the blocked party.
- **Platform admin:** user suspension, verification decisions, taxonomy, report resolution, audit viewing — each audited; admins cannot read DMs (D-06 recommendation) and cannot grant admin without an audited, confirmed action.

## Consequences
Authorization helpers per module (`canManageCommunity(user, community)` etc.) become mandatory, unit-tested building blocks; BOLA tests (T-04/T-05) verify them per endpoint. Trade-off: more checks to write than a role middleware — accepted, since the frontend never authorizes.

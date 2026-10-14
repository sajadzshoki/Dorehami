# ADR-0016: Append-only audit logging & privacy-minimal operational logging

- **Status:** Proposed (append-only + minimal data: brief-aligned)

## Context / problem
Admin power needs accountability (T-18) without building a surveillance store; logs themselves leak secrets when careless (T-17).

## Options considered
1. **`audit_logs` table, append-only at application level** (no update/delete code paths; DB role restriction as Phase 8 hardening), recording actor/action/target/minimal metadata; operational logs structured + redacted, separate from audit.
2. Audit via generic app logs only — mutable, unqueryable in admin UI; rejected.
3. Full event-sourcing of all actions — overengineering; rejected.

## Decision
Option 1. Audited: admin actions, capability grant/suspend, verification & moderation decisions, system-setting changes, account-deletion execution. Never logged anywhere: OTP codes, passwords, session tokens, message bodies, secrets; phone numbers masked. Retention periods are a founder/legal decision (PROJECT-STATUS D-06) and **no period in this document is a compliance claim**. Interim engineering defaults until that decision: audit logs are append-only and not deleted by the application; personal operational logs are kept short (Provisional 30 days). Phase 8 cannot sign off until D-06 is answered.

## Consequences
Admin UI gets a trustworthy audit view (MVP scope); developers must use the audit service, enforced by review + tests that each admin mutation writes exactly one entry.

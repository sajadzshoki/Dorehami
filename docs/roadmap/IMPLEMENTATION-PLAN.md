# Implementation Plan — Dorehami (Phases 0–9)

Rules for every phase: independently reviewable; only the allowed scope; server-side authorization and tests are part of "done", not follow-ups; update the listed docs in the same change; commit only when acceptance criteria pass locally and CI is green (once CI exists); push only to the session branch unless the team directs otherwise. No phase may pull post-MVP items (MVP-SCOPE §2) without a scope-change note.

## Phase 0 — Product definition & architecture documentation ✅ (this phase)
Objective: the document set in this repository. Acceptance: PROJECT-STATUS checklist §Phase-0-criteria. No code, no infra.

## Phase 1 — Application foundation, database, configuration, Compose
- **Prereqs:** Phase 0 accepted; ADR-0001/0003/0018 validations scheduled first.
- **Allowed scope:** scaffold Nuxt 4 app (no product features), Tailwind/Nuxt UI per compatibility check, Drizzle + PostgreSQL wiring, migration tooling (ADR-0004), env schema + fail-fast config validation, brand-config module skeleton (tokens, no final design), Compose stack with pinned official images (resolve ADR-0018 app-image mechanism + proxy choice), CI (lint, typecheck, unit tests, secret scan), test infrastructure (ephemeral Postgres), health endpoint, structured redacted logging.
- **Deliverables:** running empty app via Compose; one trivial migration applied/rolled forward; resolved versions recorded in ADR-0001/0003/0018.
- **Risks:** no-Dockerfile packaging friction; Nuxt-UI/Tailwind version conflicts.
- **Tests:** boot test, config-validation failures (incl. prod+mock SMS rejection, T-22), migration apply on empty DB.
- **Acceptance:** fresh machine → Compose up → app healthy → tests green; no feature endpoints exist.
- **Docs to update:** ADR validations, ENVIRONMENT-AND-SECRETS (real variable list), ARCHITECTURE §7 outcome.

## Phase 2 — Design system, i18n, application shells
- **Prereqs:** Phase 1.
- **Scope:** token system (light/dark, contrast-verified; resolve Q-09 font + dark tokens), brand config wired to UI/manifest, fa/en routing + language switcher, RTL/LTR layouts, Jalali/Gregorian date utilities (ADR-0009 validation), public shell (header/footer/discovery chrome), authenticated shell, empty/loading/error state patterns, accessibility foundations (focus, landmarks, reduced motion).
- **Tests:** i18n routing + `dir` correctness, calendar conversion unit tests (incl. leap years), axe-style a11y checks on shells, visual regression optional.
- **Acceptance:** both locales render shells correctly RTL/LTR; hardcoded-brand lint rule active (ADR-0019).
- **Docs:** BRAND (font/dark tokens final), I18N-AND-DATE outcomes, Q-09/Q-13 closure.

## Phase 3 — Authentication, profiles, onboarding
- **Prereqs:** Phase 2 shells; Kavenegar account/API access (external dependency — if unavailable, mock-only dev continues but launch gate stays closed).
- **Scope:** OTP request/verify + sessions + logout + session list, optional password, profiles, interests/city onboarding, privacy settings; `SmsProvider` adapter (Kavenegar + mock); rate limits per SECURITY-BASELINE.
- **Tests:** full THREAT-MODEL T-01/02/03/05/13/17 auth suite; onboarding e2e in both locales.
- **Acceptance:** ACCEPTANCE-CRITERIA AC-AUTH lines pass; production-mode mock rejection demonstrated.
- **Docs:** ADR-0005 parameter record (Kavenegar behavior), SECURITY-BASELINE deltas.

## Phase 4 — Event discovery, recurrence, registration
- **Prereqs:** Phase 3; catalog admin basics (categories/cities seed + minimal admin CRUD may land here ahead of Phase 7's full panel — record the split).
- **Scope:** organizer capability activation + organizer pages, event CRUD, series/occurrence materialization (spec + tests for regeneration semantics, ADR-0007), discovery/search/filters (ADR-0010), registration transaction (ADR-0008), attendee + event cancellation, ICS export, media upload for covers (ADR-0011), publication review hook (queue UI may be Phase 7; the *state machine* lands here).
- **Tests:** concurrency capacity test, duplicate/race/retry suite, recurrence fuzz + timezone cases, BOLA per endpoint, search normalization cases.
- **Acceptance:** AC-EVENT lines pass; J1/J4 e2e green.
- **Docs:** DOMAIN-MODEL/ERD as-built deltas, ADR-0007/0008 validation notes, Q-04/Q-06 closure.

## Phase 5 — Communities & social interactions
- **Prereqs:** Phase 4 (event-community linking reuses event module).
- **Scope:** communities CRUD + review states, membership join/leave/approve, posts/comments/reactions, reporting (user side), 1-to-1 messaging with initiation rules (Q-10 closure required first), community-linked events surfaced.
- **Tests:** membership/role matrix, DM participant authorization (T-07), report flow, XSS corpus on all content fields (T-09).
- **Acceptance:** AC-COMM/AC-DM lines pass; J3/J5 e2e green.
- **Docs:** DOMAIN-MODEL §6–7 as-built, Q-03/Q-10 closure.

## Phase 6 — Notifications, PWA, calendar polish
- **Prereqs:** Phases 4–5 domain events exist.
- **Scope:** in-app notifications for all defined triggers, push subscriptions + dispatch + preferences (VAPID), PWA installability + service worker per ADR-0014, ICS across calendar apps (Q-14 policy), notification link-safety.
- **Tests:** preference matrix (no push when disabled/unsubscribed), offline shell behavior, ICS import checks, T-21 link-injection test.
- **Acceptance:** AC-NOTIF/PWA lines pass.
- **Docs:** ADR-0014 validation, ENVIRONMENT (VAPID entries) final.

## Phase 7 — Organizer workspace & administration panel
- **Prereqs:** Phases 4–6 (all moderated entities exist).
- **Scope:** organizer workspace (my events/communities/registrations, occurrence management), admin panel (users, events, communities, categories, cities, reports queue, publication review queue, organizer verification, audit-log viewer), `system_settings` if retained.
- **Tests:** admin authorization matrix (T-05/T-18), audit-entry-per-action tests, review-queue e2e (J6).
- **Acceptance:** AC-ADMIN lines pass; no admin action possible without audit row.
- **Docs:** ADR-0006/0016 as-built, admin runbook section in operations docs.

## Phase 8 — Security hardening, integration tests, MVP acceptance
- **Prereqs:** all features; Q-12 retention/legal answers.
- **Scope:** full THREAT-MODEL verification pass, dependency & secret audits, rate-limit/load testing, performance budget validation (SEO-AND-PERFORMANCE), accessibility audit (WCAG 2.2 AA) incl. native fa review, backup/restore drill on staging-like stack, penetration-fix cycle.
- **Tests:** the complete ACCEPTANCE-CRITERIA suite, both locales.
- **Acceptance:** every MVP acceptance line green or explicitly waived in PROJECT-STATUS with rationale; security checklist (Baseline §9) signed off.
- **Docs:** test/performance reports linked from PROJECT-STATUS; THREAT-MODEL residual-risk sign-off.

## Phase 9 — Deployment, backup, restore, release documentation
- **Prereqs:** Phase 8 sign-off; production VPS + SMS provider + domain/TLS ready.
- **Scope:** production Compose deployment, HTTPS, backup automation + restore drill from real backup, release/rollback runbook, monitoring/alerts minimal set (uptime, OTP spend, error rate), go-live checklist, user-facing help copy (cancellation policy etc.) finalized in both languages.
- **Tests:** restore-from-backup rehearsal, failover of config (missing-secret boot refusal), smoke suite against production.
- **Acceptance:** DEPLOYMENT-AND-BACKUP-PLAN executed end-to-end once; rollback rehearsed; Phase 0 doc set updated to as-built.
- **Docs:** operations docs become as-built runbooks; PROJECT-STATUS → "MVP released" with known limitations.

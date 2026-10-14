# Implementation Plan — Dorehami (Phases 0–9)

Rules for every phase: independently reviewable; only the allowed scope; server-side authorization and tests are part of "done", not follow-ups; update the listed docs in the same change; commit only when acceptance criteria pass locally and CI is green (once CI exists); push only to the session branch unless the team directs otherwise. No phase may pull post-MVP items (MVP-SCOPE §2) without a scope-change note.

## Phase 0 — Product definition & architecture documentation ✅
Objective: the document set in this repository. Acceptance: PROJECT-STATUS checklist §Phase-0-criteria. No code, no infra.

## Phase 0.1 — Documentation audit & decision resolution ✅ (this phase)
Audited Phase 0 for contradictions, resolved Phase 1 blockers from npm-registry/GitHub evidence (ADR-0001/0003), specified the Dockerfile-less image workflow (ADR-0018 Option A), migration failure behavior (ADR-0004), the normative permission matrix and event edge cases (DOMAIN-MODEL §10–12), and ERD corrections. Remaining founder approvals are tracked as D-01… in PROJECT-STATUS. No code, no infra.

## Phase 0.2 — Founder decisions recorded & Phase 1 gated ✅ (this phase)
D-01 approved as Option C (ADR-0018), Phase 0.1 audit approved as documentation (open questions remain open and scheduled), Phase 1 entry gate, scope, AC-P1 acceptance criteria, deliverables, and exit gate defined below and in PROJECT-STATUS. No code, no infra.

## Phase 1 — Technical foundation & infrastructure scaffolding (no product features)
- **Entry gate (all required; defined in PROJECT-STATUS §5):** G1 D-01 recorded consistently (ADR-0018, ADR index, PROJECT-STATUS, this plan, ARCHITECTURE, operations docs) · G2 version candidates selected from ADR-0001/0003 evidence (final pins = experiment E-2 at bootstrap) · G3 image strategy: Option C approved as default, Dockerfile-less retained only under the same criteria (final confirmation = experiment E-1) · G4 dev/test/build/migrate/deploy workflows documented (ADR-0018/0004, DEPLOYMENT-AND-BACKUP-PLAN) · G5 external resources/credentials inventoried by variable name & purpose only (ENVIRONMENT-AND-SECRETS) · G6 scope & exclusions (§ below) explicit · G7 at Phase 1 start: Git branch/tree checked and existing work preserved before any scaffolding commit.
- **Allowed scope (only):** project scaffolding with pinned versions + committed lockfile (pnpm via Corepack, TypeScript 5.x line, per ADR-0001/0003 pending E-2); strict TypeScript + framework config; environment-variable validation with safe production failure; PostgreSQL connectivity + Drizzle setup; a **minimal technical migration workflow** (framework/migration bookkeeping only — a trivial non-domain migration, e.g. an empty/framework table, solely to prove apply/fail/recover); **no product entities or product-domain migrations** — any exception needs a written justification recorded here before it lands; dev/test/build commands; selected application-image workflow (E-1); Docker Compose infrastructure (app/PostgreSQL/MinIO/reverse proxy) with health checks + persistent volumes; health/readiness endpoints + basic structured (redacted) logging; minimal automated checks (config validation, DB connectivity, migration apply, production build); basic CI (lint, typecheck, tests, secret + image scan); brand-config module *skeleton* (config plumbing only — no design system, no screens); essential setup/operations docs.
- **Explicit exclusions (Phase 1 must NOT contain):** authentication/OTP/sessions, user profiles/onboarding, events/series/occurrences/registrations, communities/memberships/posts/comments/reactions, direct messaging, notifications, organizer/admin features, product UI screens (including discovery/home), product-domain database entities, SEO pages, and anything from MVP-SCOPE §2. A bare framework placeholder route to prove SSR/health is allowed and is not a product screen.
- **Bounded experiments (run inside Phase 1; each blocks only what it gates):**
  - **E-1 Image workflow** — implement the standard Dockerfile (Option C) for the scaffold; success = all ADR-0018 validation criteria (digest-pinned bases, frozen-lockfile build, secret-free layers, non-root start + health, same-image migrations, rollback-by-digest). Failure of any criterion → record it, then evaluate Option A against the identical criteria; B only if both fail. *Blocks:* the image/deploy deliverables, not scaffolding or DB work.
  - **E-2 Version pinning** — at bootstrap, re-check registry evidence (it drifts) and pin Node/Nuxt/TypeScript/pnpm/@nuxt/ui/Tailwind/@nuxtjs/i18n/Drizzle + driver; success = frozen install + strict typecheck + production build all pass on the pinned set, recorded in ADR-0001/0003. *Blocks:* declaring the scaffold done; does not block E-1's start with candidate versions.
  - **E-3 PostgreSQL line & driver** — choose PG 18 vs 17 and the single Drizzle driver (D-02); success = connect, migrate, dump/restore on a scratch instance. *Blocks:* migration deliverable sign-off only.
  - **E-4 Migration failure drill** — success = AC-P1 8 demonstrated end-to-end. *Blocks:* Phase 1 exit.
- **Expected deliverables (files/config areas):** `package.json` + committed lockfile + `packageManager` pin; Nuxt + TypeScript(strict) + lint configuration; `.env.example` (names/dummy values only) + startup env-validation module; Drizzle configuration + migrations directory containing only the minimal technical migration; selected image artifact (`Dockerfile` if Option C, or the documented assembly configuration if Option A) + `compose.yaml` (dev/prod profiles as designed); health/readiness route + structured logging setup; test setup (unit + ephemeral-PostgreSQL integration harness) with the minimal-check suite; CI configuration; README/setup docs and operations updates; ADR/PROJECT-STATUS write-backs of E-1…E-4 results.
- **Tests (minimal, measurable):** the AC-P1 suite — fresh-checkout run, frozen install, typecheck+lint zero errors, production build, readiness honesty with the DB down/up, migration apply-from-empty + induced-failure drill, image build ×2 + start, secret scans (repo + image). (Prod-mode mock-SMS rejection, T-22, belongs to the config-validation checks insofar as provider config exists in the Phase 1 skeleton; full OTP behavior remains Phase 3.)
- **Acceptance:** every AC-P1 item is *Verified* (see exit gate) and a diff review confirms AC-P1 12 (no product features).
- **Docs to update:** ADR-0001/0003 (pinned versions), ADR-0018 (E-1 selected workflow + evidence), ADR-0004 (as-built migration mechanics), ENVIRONMENT-AND-SECRETS (as-built variable list), ARCHITECTURE §7, DEPLOYMENT-AND-BACKUP-PLAN (tested path), PROJECT-STATUS (exit report).
- **Exit gate (before Phase 2 may begin):** every AC-P1 criterion is classified exactly one of **Verified & passing** · **Implemented but not verified** (exit blocked until verified or explicitly waived by the founder in PROJECT-STATUS) · **Blocked by external resources** (named resource + owner; exit blocked if it affects AC-P1 1–11) · **Deferred with explicit reason** (founder-visible, with the phase that will close it). Phase 2 begins only when no AC-P1 item is silently in the middle two states and the Phase 1 commit(s) are pushed with CI green.
- **Risks:** version drift between the Phase 0.1 evidence and bootstrap day (E-2, lockfile on day one); Dockerfile layer hygiene (E-1 secret scan); treating “minimal migration workflow” as license for product schema (AC-P1 12 diff review).

## Phase 2 — Design system, i18n, application shells
- **Prereqs:** Phase 1.
- **Scope:** token system (light/dark, contrast-verified; resolve D-12 font + dark tokens), brand config wired to UI/manifest, fa/en routing + language switcher, RTL/LTR layouts, Jalali/Gregorian date utilities (ADR-0009 validation), public shell (header/footer/discovery chrome), authenticated shell, empty/loading/error state patterns, accessibility foundations (focus, landmarks, reduced motion).
- **Tests:** i18n routing + `dir` correctness, calendar conversion unit tests (incl. leap years), axe-style a11y checks on shells, visual regression optional.
- **Acceptance:** both locales render shells correctly RTL/LTR; hardcoded-brand lint rule active (ADR-0019).
- **Docs:** BRAND (font/dark tokens final), I18N-AND-DATE outcomes, D-12/D-08 closure.

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
- **Docs:** DOMAIN-MODEL/ERD as-built deltas, ADR-0007/0008 validation notes, D-10 + A-4 closure.

## Phase 5 — Communities & social interactions
- **Prereqs:** Phase 4 (event-community linking reuses event module).
- **Scope:** communities CRUD + review states, membership join/leave/approve, posts/comments/reactions, reporting (user side), 1-to-1 messaging with initiation/blocking rules (D-05/D-07 closure required first), community-linked events surfaced.
- **Tests:** membership/role matrix, DM participant authorization (T-07), report flow, XSS corpus on all content fields (T-09).
- **Acceptance:** AC-COMM/AC-DM lines pass; J3/J5 e2e green.
- **Docs:** DOMAIN-MODEL §6–7 as-built, D-13/D-05/D-07 closure.

## Phase 6 — Notifications, PWA, calendar polish
- **Prereqs:** Phases 4–5 domain events exist.
- **Scope:** in-app notifications for all defined triggers, push subscriptions + dispatch + preferences (VAPID), PWA installability + service worker per ADR-0014, ICS across calendar apps (D-14 policy), notification link-safety.
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
- **Prereqs:** all features; D-06 retention/legal answers.
- **Scope:** full THREAT-MODEL verification pass, dependency & secret audits, rate-limit/load testing, performance budget validation (SEO-AND-PERFORMANCE), accessibility audit (WCAG 2.2 AA) incl. native fa review, backup/restore drill on staging-like stack, penetration-fix cycle.
- **Tests:** the complete ACCEPTANCE-CRITERIA suite, both locales.
- **Acceptance:** every MVP acceptance line green or explicitly waived in PROJECT-STATUS with rationale; security checklist (Baseline §10) signed off.
- **Docs:** test/performance reports linked from PROJECT-STATUS; THREAT-MODEL residual-risk sign-off.

## Phase 9 — Deployment, backup, restore, release documentation
- **Prereqs:** Phase 8 sign-off; production VPS + SMS provider + domain/TLS ready.
- **Scope:** production Compose deployment, HTTPS, backup automation + restore drill from real backup, release/rollback runbook, monitoring/alerts minimal set (uptime, OTP spend, error rate), go-live checklist, user-facing help copy (cancellation policy etc.) finalized in both languages.
- **Tests:** restore-from-backup rehearsal, failover of config (missing-secret boot refusal), smoke suite against production.
- **Acceptance:** DEPLOYMENT-AND-BACKUP-PLAN executed end-to-end once; rollback rehearsed; Phase 0 doc set updated to as-built.
- **Docs:** operations docs become as-built runbooks; PROJECT-STATUS → "MVP released" with known limitations.

# Project Status — Dorehami

## Current status
**Phase 0 (product definition & architecture documentation) — complete in this change, pending human review.** The repository previously contained only a one-line README. No application code, database, Docker/Compose files, or infrastructure have been created. Recommended next step: human review of open questions Q-01…Q-15 priorities, then Phase 1 per the prompt in §6 below.

## Decision summary
Confirmed by brief: bilingual event+community product; OTP-first auth; modular monolith (Nuxt 4/Nitro); PostgreSQL+Drizzle; MinIO; PostgreSQL MVP search; Kavenegar via adapter; Compose without custom Dockerfiles; configurable brand. Proposed pending validation: exact versions (Phase 1), auth parameters, recurrence subset/regeneration semantics, trust-level thresholds, DM initiation rule, app-image delivery mechanism (ADR-0018). Full record: [decisions](decisions/README.md).

## Assumptions (validate; never treat as facts)
A-1 MVP events are free (no payments) · A-2 fa-first, Iran-focused launch · A-3 public communities only · A-4 ≤1 community per event — see MVP-SCOPE §4.

## Open questions (owner: product/team; target phase to close)
| ID | Question | Needed by |
|---|---|---|
| Q-01 | Minimum age / age policy and any guardian rules? | Phase 3 |
| Q-02 | Onboarding interests: min/max selections; interest vs category mapping? | Phase 2–3 |
| Q-03 | Sole community manager leaving: successor-required vs community archival — confirm rule. | Phase 5 |
| Q-04 | Online-event join-link reveal policy; organizer-set registration close time; attendee cancellation deadline (proposed: until start). | Phase 4 |
| Q-05 | Organizer trust levels: exact promotion criteria/thresholds; what requires review (events, communities, posts?). | Phase 4 |
| Q-06 | Event↔community linking permission (managers only?); confirm ≤1 community per event (A-4). | Phase 4 |
| Q-07 | Persian UI term for "community" (انجمن vs اجتماع vs …) — native copy decision. | Phase 2 |
| Q-08 | Launch market/cities/categories; default locale confirm (`fa`); phone country scope (Iran-only numbers at launch?). | Phase 1–2 |
| Q-09 | Persian/Latin typeface + licensing; dark-theme token derivation. | Phase 2 |
| Q-10 | DM initiation contexts; is **block** in MVP (recommended yes alongside report)? | Phase 5 |
| Q-11 | Which minimal, privacy-conscious product metrics (PRD §10) and where stored? | Phase 1/8 |
| Q-12 | Legal retention periods (reports, audit, deleted accounts/content); admin DM-access stance confirm (proposed: no access). | Phase 8 (design in Phase 5/7) |
| Q-13 | Slug strategy per locale vs one shared slug; per-locale titles for admin taxonomy. | Phase 2 |
| Q-14 | ICS access: authenticated download vs signed link (calendar-app compatibility)? | Phase 6 |
| Q-15 | CAPTCHA provider/privacy stance if SMS-abuse escalation needs it. | Phase 3/8 |

## Major risks
R-1 Recurrence regeneration semantics understated → off-by-one/DST bugs (mitigated: ADR-0007 tests budgeted in Phase 4). R-2 SMS cost/abuse (T-13; budget alerts + limits). R-3 No-custom-Dockerfile app packaging friction (ADR-0018 open point; resolve first in Phase 1). R-4 Jalali date-picker accessibility is genuinely hard (budgeted Phases 2/4). R-5 Low-trust review could bottleneck supply if thresholds are wrong (Q-05; tune with Phase 7 metrics). R-6 Single-VPS availability (accepted for MVP; backup/restore drills, RPO/RTO in operations plan). R-7 Persian copy quality if written by translation (brand rule: native copy + fa review in Phase 2/8).

## Phase 0 acceptance checklist (§14 of the brief)
1 Brand consistent ✔ · 2 Configurable identity documented ✔ (BRAND §5, ADR-0019) · 3 MVP vs post-MVP explicit ✔ · 4 User journeys ✔ (USER-FLOWS J1–J6) · 5 Architecture/modules ✔ · 6 ERD incl. recurrence ✔ · 7 Capacity/concurrency ✔ (ADR-0008) · 8 Threat model prioritized + testable ✔ (22 threats) · 9 ADRs ✔ (19) · 10 API/error/authz conventions ✔ · 11 I18n/calendar/SEO/a11y/perf ✔ · 12 Testing + measurable acceptance ✔ · 13 Phased dependency-aware roadmap ✔ · 14 Provider failure behavior ✔ (ADR-0015, Baseline §2) · 15 No implementation created ✔ · 16 Open questions explicit ✔ (above) · 17 Terminology consistent ✔ (GLOSSARY) · 18 Links/Mermaid checked — validated in the Phase 0 change (see commit notes).

## Recommended next step — Phase 1 prompt

> **Phase 1 — Application foundation.** Prereads: docs/PROJECT-STATUS.md, docs/decisions/ADR-0001/0003/0004/0018, docs/operations/ENVIRONMENT-AND-SECRETS.md, docs/roadmap/IMPLEMENTATION-PLAN.md (Phase 1 section). First resolve and record in ADRs: pinned Nuxt/PostgreSQL/Drizzle versions + Tailwind/Nuxt-UI compatibility, and the no-custom-Dockerfile app-image delivery mechanism (ADR-0018). Then scaffold the Nuxt 4 + TypeScript app with Drizzle/PostgreSQL, migration tooling, env validation (production must reject mock SMS), brand-config skeleton, the Compose stack (pinned official images), CI (lint/typecheck/unit/secret-scan), and ephemeral-Postgres test infrastructure. No product features, no auth, no event/community endpoints. Verify: fresh Compose boot is healthy, one trivial migration applies, config-failure tests pass. Update ADR-0001/0003/0018 with resolved versions and close Phase 1 acceptance in docs/PROJECT-STATUS.md. Commit as `feat: bootstrap application foundation` only when CI is green.

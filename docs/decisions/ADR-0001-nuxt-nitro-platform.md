# ADR-0001: Nuxt 4 + Nitro as the application platform

- **Status:** Proposed (validate in Phase 1)

## Context / problem
One small team must ship SSR SEO pages, an interactive app, and a JSON API in fa/en, on a single VPS, in TypeScript.

## Options considered
1. **Nuxt 4 (Vue 3, Nitro server routes)** — one codebase for SSR pages + API; strong i18n/SEO story.
2. Separate Vue SPA + Node API — simpler mental split, but duplicates routing/auth, weakens SSR/SEO, more ops.
3. Next.js/React — capable, but team/product direction and brief specify Vue/Nuxt; switching adds no product value.

## Decision
Adopt option 1: Nuxt 4 + Vue 3 + TypeScript (strict) + Nitro routes; Tailwind CSS and Nuxt UI **subject to Phase 1 compatibility verification** with the exact Nuxt 4 version pinned then.

## Rationale / consequences
Single deployable, SSR where it matters, file-based structure maps to the module boundaries in ARCHITECTURE §3. Trade-offs: Nitro/Vue ecosystem churn — mitigated by pinning versions, avoiding edge APIs, and keeping domain logic framework-light so modules are testable without Nuxt.

## Phase 0.1 compatibility evidence (npm registry, checked 2026-10-14 — re-verify at Phase 1 bootstrap; these are ceilings, not a lockfile)

| Package | Latest observed | Constraint evidence |
|---|---|---|
| `nuxt` | 4.6.1 | engines: Node `^22.22.3 \|\| ^24.15.0 \|\| >=26.0.0`; bundles Vue `^3.5.43` |
| `@nuxt/ui` | 4.11.3 | depends on Nuxt kit `^4.5.2`, Tailwind CSS `^4.3.3` (`@tailwindcss/vite`), TypeScript peer `^5.6.3 \|\| ^6 \|\| ^7` |
| `tailwindcss` | 4.3.3 | Tailwind v4 line required by Nuxt UI 4 — Tailwind v3 configs/plugins are not interchangeable |
| `@nuxtjs/i18n` | 10.6.0 | Nuxt kit `^4.5.1`, Node `>=20.11.1` |
| `@vite-pwa/nuxt` | 1.1.1 | present; maturity still validated in Phase 6 per ADR-0014 |
| `typescript` | 7.0.2 latest; **5.9.3 latest 5.x** | Recommendation: pin the latest 5.x at bootstrap (conservative tooling compatibility), not the newest major, until Phase 1 typecheck proves otherwise |

**Runtime decision from evidence:** Node 24 LTS line at or above the floor Nuxt declares (`^24.15.0`); Phase 1 pins the exact official Node image by digest (ADR-0018) and records it here. Node 26 is allowed by engines but is not the LTS choice for a first production release.
**Package manager:** pnpm via Corepack, exact version pinned in `packageManager` at bootstrap and in CI; npm is the fallback only if a Phase 1 blocker is documented. No version is claimed here beyond what the registry showed on the check date.

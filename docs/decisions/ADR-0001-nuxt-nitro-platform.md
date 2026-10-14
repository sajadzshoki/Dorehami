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
Single deployable, SSR where it matters, file-based structure maps to the module boundaries in ARCHITECTURE §3. Trade-offs: Nitro/Vue ecosystem churn — mitigated by pinning versions, avoiding edge APIs, and keeping domain logic framework-light so modules are testable without Nuxt. **Validation (Phase 1):** verify Nuxt 4 stable version, Nuxt UI + Tailwind compatibility, i18n module support, and PWA module maturity; record resolved versions in this ADR before scaffolding.

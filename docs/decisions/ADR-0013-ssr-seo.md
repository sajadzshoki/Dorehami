# ADR-0013: SSR for public pages, hydrated app for authenticated surfaces

- **Status:** Proposed

## Context / problem
Public event/community/organizer pages must be indexable and fast on slow connections; authenticated surfaces (workspace, admin, messaging) don't need SSR but share components.

## Options considered
1. **Nuxt SSR for public routes; same app serves authenticated routes** (SSR or CSR per route, decided by data sensitivity/perf — never SSR private data into shared caches).
2. Fully static generation for public pages — content changes (capacity, cancellation) make staleness risky; rejected as primary (targeted static landing pages allowed).
3. SPA-only — fails SEO requirement; rejected.

## Decision
Option 1, with the SEO rules in SEO-AND-PERFORMANCE: published-only SSR queries, per-locale canonical/hreflang/OG, sitemap, structured data only when valid. Public HTML must never contain permission-restricted data (test-enforced, T-06).

## Consequences
Server load is the scaling axis; mitigations are HTTP caching of static assets (and any page caching must be Phase-approved with invalidation rules for cancellation/capacity changes).

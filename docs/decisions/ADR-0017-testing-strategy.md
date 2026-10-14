# ADR-0017: Testing strategy

- **Status:** Proposed

## Context / problem
Critical journeys (OTP login, registration under concurrency, authorization boundaries, publication review) must be provably correct; MVP demands automated tests for critical journeys (brief).

## Options considered
1. **Layered:** unit (domain rules, recurrence, Jalali conversion, authz helpers) · integration (API + real PostgreSQL/MinIO in test containers, mocked providers) · e2e (critical journeys in a browser, both locales) · specialized suites (concurrency for capacity, security/BOLA corpus, i18n/RTL smoke, SEO meta assertions). Tooling (Vitest/Playwright etc.) pinned in Phase 1 by compatibility.
2. E2E only — slow, brittle, misses invariants; rejected.
3. Manual QA only — violates brief; rejected.

## Decision
Option 1. Merge gates: unit+integration green; BOLA suite mandatory for every new endpoint; the THREAT-MODEL "verification test" column is the security test backlog. No test may depend on real SMS (mock adapter) or real user data.

## Consequences
Test infrastructure (ephemeral Postgres) is a Phase 1 deliverable, not an afterthought; flaky tests are treated as defects.

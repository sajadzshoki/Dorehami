# Architecture Decision Records — index

Status key: **Proposed** = recommended, needs the validation stated in the ADR; **Accepted** = confirmed by the Phase 0 brief. No ADR is silently final: each lists its validation/revisit trigger.

| ADR | Decision | Status |
|---|---|---|
| [0001](ADR-0001-nuxt-nitro-platform.md) | Nuxt 4 + Nitro application platform | Proposed — registry evidence added Phase 0.1 |
| [0002](ADR-0002-modular-monolith.md) | Modular monolith over microservices | Accepted (brief) |
| [0003](ADR-0003-postgresql-drizzle.md) | PostgreSQL + Drizzle ORM | Proposed — evidence added Phase 0.1; PG 17/18 recommended, digest pinned in Phase 1 |
| [0004](ADR-0004-migrations.md) | Versioned migrations via Drizzle Kit, forward-only in prod | Proposed — failure/recovery behavior specified Phase 0.1 |
| [0005](ADR-0005-otp-auth-sessions.md) | Phone OTP auth + server-side sessions; optional password | Proposed (policy), Accepted (OTP-first per brief) |
| [0006](ADR-0006-authorization-model.md) | Capability + resource-scoped authorization | Proposed |
| [0007](ADR-0007-recurring-events.md) | Series + materialized occurrences | Proposed |
| [0008](ADR-0008-capacity-transactions.md) | Transactional capacity with row locking + partial unique index | Proposed |
| [0009](ADR-0009-time-calendars.md) | UTC storage, event timezone, Jalali/Gregorian display | Proposed |
| [0010](ADR-0010-postgresql-search.md) | PostgreSQL search for MVP | Accepted (brief), revisit triggers defined |
| [0011](ADR-0011-minio-media.md) | MinIO private buckets + signed URLs | Proposed |
| [0012](ADR-0012-i18n-localized-urls.md) | fa/en with locale-prefixed URLs | Proposed |
| [0013](ADR-0013-ssr-seo.md) | SSR for public pages; CSR-light app shell | Proposed |
| [0014](ADR-0014-pwa.md) | Installable PWA, conservative caching | Proposed |
| [0015](ADR-0015-provider-adapters.md) | Provider adapter ports (SMS first: Kavenegar) | Accepted (brief pattern), Proposed (interfaces) |
| [0016](ADR-0016-audit-logging-privacy.md) | Append-only audit log, minimal-data logging | Proposed |
| [0017](ADR-0017-testing-strategy.md) | Layered testing: unit/integration/e2e/concurrency/security | Proposed |
| [0018](ADR-0018-docker-compose-no-custom-dockerfiles.md) | Docker Compose deployment and application-image strategy | **Accepted — founder D-01 (Phase 0.2): Option C (standard Dockerfile) approved; no-Dockerfile constraint lifted; final workflow confirmed by Phase 1 experiment E-1** |
| [0019](ADR-0019-brand-configuration.md) | Centralized brand config & design tokens | Accepted (brief requirement) |

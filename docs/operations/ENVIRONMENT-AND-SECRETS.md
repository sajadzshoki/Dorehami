# Environment & Secrets — Dorehami

Names and behavior only — **no real values anywhere in this repository**. A `.env.example` with dummy placeholders is created in Phase 1. Startup validation: production boot fails fast, naming missing/invalid required variables (never printing values); mock providers are rejected when `NODE_ENV=production`.

| Variable | Purpose | Required | Sensitivity | If missing/invalid |
|---|---|---|---|---|
| `NODE_ENV` | `development`/`test`/`production` mode switch | yes | low | default `development` locally; Compose sets explicitly |
| `APP_BASE_URL` | Canonical public origin (links, ICS, canonical URLs) | prod: yes | low | prod boot fails (links/SEO would be wrong) |
| `DATABASE_URL` | PostgreSQL connection for the app | yes | **secret** | boot fails |
| `DB_MIGRATION_URL` | Privileged connection for migrations, if separated (Phase 1 decision) | optional | **secret** | migrations unavailable; app still boots |
| `SESSION_SECRET` | HMAC/pepper for OTP-code + token hashing | yes | **secret** | boot fails |
| `SMS_PROVIDER` | `kavenegar` \| `mock` | yes | low | prod boot fails if unset or `mock` |
| `KAVENEGAR_API_KEY` | Kavenegar credential | when provider=kavenegar | **secret** | prod boot fails; OTP send (dev) returns provider error |
| `KAVENEGAR_SENDER` | Sender line/number per provider account | provider-dependent | low | provider default or send failure — Phase 3 confirms |
| `OTP_TTL_SECONDS`, `OTP_MAX_ATTEMPTS`, `OTP_RESEND_COOLDOWN_SECONDS` | OTP policy knobs (defaults in SECURITY-BASELINE §2) | no | low | safe defaults used |
| `MINIO_ENDPOINT`, `MINIO_ACCESS_KEY`, `MINIO_SECRET_KEY`, `MINIO_BUCKET_MEDIA`, `MINIO_BUCKET_PUBLIC` | Object storage (ADR-0011) | yes (media features) | keys **secret** | boot fails in prod; uploads disabled with honest error in dev |
| `VAPID_PUBLIC_KEY`, `VAPID_PRIVATE_KEY`, `VAPID_SUBJECT` | Web Push (Phase 6) | push feature only | private key **secret** | push disabled (feature-flagged off), rest of app unaffected |
| `RATE_LIMIT_*` (per-route overrides) | Abuse controls (Baseline §7) | no | low | documented defaults |
| `AUDIT/LOG_LEVEL` (`LOG_LEVEL`) | Log verbosity | no | low | default `info`; `debug` forbidden in production |
| `DEFAULT_LOCALE` | Entry-locale default (D-09) | no | low | `fa` default |
| `DEFAULT_TIMEZONE` | Fallback display timezone | no | low | `Asia/Tehran` default |
| `APP_IMAGE_DIGEST` | Digest of the production app image built by the ADR-0018 selected workflow (standard Dockerfile by default, Option C); deploys pin by digest, never a floating tag | prod: yes | low | deploy tooling refuses to proceed (T-24) |
| `REGISTRY` / registry credentials | Where CI pushes and the VPS pulls the app image (e.g. GHCR) | prod CI/deploy: yes | credentials **secret** | build/deploy fails explicitly; no fallback to local unpinned builds |

Rules: secrets via env or Docker secret files outside Git; never in Compose files committed with real values, never in images, never logged; rotation = update env + restart + (for `SESSION_SECRET`) forced session invalidation (all users re-login — accepted, documented consequence); CI scans for accidental secrets (T-19).

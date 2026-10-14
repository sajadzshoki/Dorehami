# Deployment & Backup Plan — Dorehami (intended; nothing here is implemented in Phase 0)

## 1. Topology (per ADR-0018/0002)
Single VPS, Docker Compose, official base images pinned by digest, per ADR-0018 as amended by founder decision D-01 (Phase 0.2): the production app image is built with a **conventional multi-stage Dockerfile (default, Option C)** — or, only if Phase 1 experiment E-1 shows it meeting every ADR-0018 criterion with equal-or-lower complexity, the retained Dockerfile-less OCI assembly (Option A). Either way, CI pushes by git SHA and production Compose pulls **by digest**. Fallback Option B (checksum-verified release tarball, bind-mounted read-only) applies only if both C and A fail E-1, with the failures recorded in ADR-0018. Reverse proxy (official Caddy recommended) terminates HTTPS → app → PostgreSQL + MinIO on a private Compose network with named volumes. DB/MinIO ports never published publicly; MinIO console disabled or bound to localhost/VPN only. Bind mounts are a dev-only mechanism and are forbidden for the production app artifact (except as the explicitly recorded Option B fallback).

## 2. Environments & configuration
Local dev (Compose, mock SMS allowed) · optional staging (same topology, fake data, real provider in sandbox/test mode if available) · production (real provider mandatory). All config via env (ENVIRONMENT-AND-SECRETS); startup validation gates deploys; health check endpoint (`/health`, no config values) wired to Compose healthchecks for app (after DB reachable), Postgres (`pg_isready`), MinIO readiness.

## 3. HTTPS & proxy guidance
Proxy obtains/renews TLS (e.g. Caddy automatic HTTPS or certbot-managed certs with the chosen proxy), redirects HTTP→HTTPS, sets/forwards security headers per SECURITY-BASELINE §1, caps request body size (aligned with upload limits), and forwards client IP via trusted headers consumed by rate limiting (spoofing considered — only the proxy may set them).

## 4. Release procedure (Phase 9 runbook skeleton)
Pin image versions → run CI (tests + secret scan) → backup (§5) → run migrations forward (ADR-0004) → roll app → smoke tests (public page, OTP request with test number where provider permits, registration on a test event) → monitor error/OTP-spend alerts. Rollback = previous app artifact + (schema is forward-only, so rollback relies on expand/contract compatibility, ADR-0004).

## 5. Backup & restore
- **PostgreSQL:** scheduled `pg_dump` (custom format) from a sidecar/cron on the host, Provisional daily full + WAL/binlog strategy decided in Phase 9 by data volume; retention Provisional 7 daily + 4 weekly, encrypted at rest, one copy off-VPS.
- **MinIO:** bucket replication or `mc mirror` of both buckets on the same schedule (media metadata in PG ties to object keys — backups taken consistently, PG first).
- **Config:** Compose/env files (secrets stored in the team's secret store, not Git) inventoried in the runbook.
- **Restore drill:** mandatory before launch and quarterly (Provisional): restore into a scratch stack, run smoke suite, record duration in PROJECT-STATUS. Untested backups are assumed broken.
- RPO/RTO: Provisional RPO 24h / RTO 4h for MVP; tighten only with measured need.

## 6. Updates & maintenance
Monthly pinned-image patch review (security advisories for Node/PostgreSQL/MinIO/proxy), staged on staging first; OS unattended security updates on the VPS host; dependency audit in CI (ADR-0017 gates). No secrets in Git at any point (T-19 checks in CI and pre-deploy).

## 7. Monitoring (minimal, privacy-conscious)
Uptime + health, error rate, OTP request/spend vs budget alert (T-13), disk/volume growth, backup success alerts. No third-party analytics on authenticated pages (Baseline §9).

# Security Baseline — Dorehami

Minimum requirements; implementation phases must not ship below this bar. Owner: whoever implements the phase; verified in Phase 8 hardening.

## 1. General

Server-side authorization on every protected operation (frontend hiding is never a control) · deny by default · least privilege for DB/MinIO/provider credentials · security headers (CSP, `X-Content-Type-Options`, `Referrer-Policy`, `frame-ancestors`) on all responses · HTTPS only in production with HSTS · dependencies pinned, audited in CI, updated monthly (Provisional cadence).

## 2. Authentication — OTP & password policy

- OTP: ≥6 digits from a CSPRNG; stored **hashed** (HMAC/hash with server secret), 5-minute expiry (Provisional), ≤5 verification attempts, single-use, one active challenge per phone+purpose, resend cooldown 60s, request rate limits per phone/IP (T-01/02/13).
- SMS text contains only the code + product name (from brand config), expiry notice, and "ignore if not requested" line. Codes never appear in logs, URLs, or analytics.
- Optional password: Argon2id (or bcrypt cost ≥12 — Phase 3 picks per library support) hashing; min length 10 (Provisional), breach-list check optional; password login rate-limited like OTP verify; changing password or phone re-authenticates with OTP and revokes other sessions.
- Production fail-safe: app **refuses to start** or refuses auth requests if the real SMS provider is unconfigured or the mock provider is selected in production (T-22). A failed SMS send surfaces as an honest error (`PROVIDER_UNAVAILABLE`), never success.

## 3. Sessions & cookies

Server-side sessions; token = ≥256-bit random, stored hashed; cookie `HttpOnly; Secure; SameSite=Lax` (evaluate Strict in Phase 3), path-scoped; rotation on login and capability/role change; idle timeout Provisional 30 days sliding with 90-day absolute for users, shorter for admins (12h idle, Provisional); logout revokes server-side; settings page lists sessions with revoke (Provisional MVP inclusion — recommended).

## 4. Authorization

Capability/resource model per ADR-0006: platform admin (platform scope) · organizer capability (own resources) · community manager (that community) · member (membership-scoped) · participant (conversation/registration-scoped). Every handler re-checks against the loaded resource; two-user BOLA tests per endpoint are merge-blocking.

## 5. Input, output, files

Schema validation on all inputs; parameterized queries only; user content plain-text with escaping (no unsanitized HTML in MVP); URL fields scheme-whitelisted and never server-fetched (T-11); uploads: images only, content-sniffed, ≤5MB (Provisional — validate for event covers Phase 2), random object keys, private MinIO buckets, signed URLs ≤15 min for private media; SVG disallowed in MVP.

## 6. Rate limiting & abuse

Global + per-route limits (auth, OTP, search, upload, messaging, reports); `429` + `Retry-After`; limits configurable via env, documented in ENVIRONMENT-AND-SECRETS. CAPTCHA only as an escalation after thresholds, privacy-reviewed (Q-15).

## 7. Secrets & configuration

Secrets only via environment/secret files outside Git; `.env.example` documents names with dummy values; CI secret scanning; startup validation fails fast listing missing required variables (names only); secrets never printed in logs or health endpoints; rotation procedure documented before first production deploy.

## 8. Logging, audit, privacy

Structured logs with redaction (no OTPs, tokens, passwords, message bodies, full phone numbers — mask to last 2 digits where operationally needed); AuditLog append-only at application level for: admin actions, capability grants/revocations, verification decisions, moderation actions, system-setting changes — capturing actor, action, target, timestamp, minimal metadata. Analytics: minimal, self-hosted or none in MVP; no third-party trackers on authenticated pages (Confirmed principle: privacy-conscious).

## 9. Production configuration checklist (gate for Phase 9)

Real SMS provider active & mock blocked · debug off · HTTPS + HSTS · MinIO/DB not publicly exposed · backups tested (restore drill) · secret scan clean · default/dummy credentials absent · error pages leak no stack traces · health endpoint exposes no config values.

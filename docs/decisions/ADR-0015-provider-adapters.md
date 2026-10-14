# ADR-0015: External-provider adapters (SMS, push, email, maps)

- **Status:** Accepted pattern (brief); interface details Proposed

## Context / problem
SMS OTP (Kavenegar initially), web push, and later email/maps must be replaceable; production must never fake delivery; domain logic must not depend on a vendor.

## Options considered
1. **Port/adapter interfaces in `platform`:** `SmsProvider.sendOtp(phone, code, locale)`, `PushProvider`, optional `EmailProvider`/`MapProvider`; config selects the implementation; mock implementations compiled/allowed only outside production.
2. Direct Kavenegar SDK calls in auth code — fast, couples auth to vendor, untestable without network; rejected.
3. Generic notification microservice — premature (ADR-0002).

## Decision
Option 1. Failure semantics are contractual: adapters throw/return explicit failure → API returns `PROVIDER_UNAVAILABLE`; startup validation fails in production if a required provider is missing or set to mock (T-22). Provider payloads/responses are never logged with secrets or codes.

## Consequences
Swapping SMS vendors is a config + adapter change; tests use the mock adapter deterministically. Kavenegar API specifics (sender line, templates, error codes) are validated in Phase 3 and recorded here.

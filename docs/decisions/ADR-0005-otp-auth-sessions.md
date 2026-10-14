# ADR-0005: Phone OTP authentication, optional password, server-side sessions

- **Status:** Proposed (OTP-first and optional password: Accepted per brief; parameters Provisional)

## Context / problem
Target users are phone-first (Iran, SMS via Kavenegar). OTP must resist brute force, replay, and SMS-cost abuse (THREAT-MODEL T-01/02/13/22); sessions must resist fixation/theft (T-03).

## Options considered
1. **Phone OTP primary + optional password + server-side sessions** (hashed opaque tokens, HttpOnly cookies).
2. Password-first with phone verification — worse fit for the audience; recovery flows enlarge attack surface.
3. Stateless JWT sessions — revocation and logout are weak; rejected for a moderation-heavy platform.
4. Third-party auth (OAuth/social) — provider availability/privacy fit for the initial market is poor; may be added later via adapters.

## Decision
Option 1, with parameters in SECURITY-BASELINE §2–3 (OTP length/expiry/attempts/cooldowns; Argon2id-or-bcrypt password hashing; cookie flags; rotation; revocation). SMS goes only through the `SmsProvider` adapter (ADR-0015); mock SMS is dev/test-only and production fails fast without real provider config.

## Consequences
Strong revocation/audit story; SMS cost is an operational risk controlled by rate limits and budget alerts. Known residual: SMS interception/SIM-swap — passkeys are a post-MVP candidate, explicitly not promised.

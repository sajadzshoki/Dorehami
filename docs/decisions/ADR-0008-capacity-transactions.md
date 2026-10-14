# ADR-0008: Transactional capacity & registration management

- **Status:** Proposed (invariant Accepted per brief; mechanism to be proven by Phase 4 tests)

## Context / problem
Concurrent registrations must never exceed capacity, and a user must never register twice for one occurrence (T-15) — including under retries and parallel requests.

## Options considered
1. **Single DB transaction:** lock occurrence row `FOR UPDATE` → check status/duplicate/`registered_count < capacity` → insert + increment. Plus DB backstops.
2. Atomic conditional `UPDATE occurrences SET registered_count = registered_count+1 WHERE id=? AND registered_count < capacity` then insert — fewer locks, trickier rollback pairing.
3. Application-level locks/Redis — extra infra, weaker guarantees; rejected.

## Decision
Option 1 as primary (option 2 acceptable if Phase 4 benchmarks demand it, with the same proofs). DB backstops regardless: partial unique index `unique(occurrence_id, user_id) where status='confirmed'`, and a `CHECK (registered_count <= capacity)`-style guard where expressible. Capacity decrease below confirmed count is rejected (`CAPACITY_BELOW_REGISTRATIONS`).

## Consequences
Correctness rests on one well-tested transaction; mandatory test: 50 parallel registrations against capacity 10 yield exactly 10 confirmed and consistent counts. Counter drift detection: reconciliation query in tests/admin diagnostics.

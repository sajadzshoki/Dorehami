# ADR-0007: Recurring events — series + materialized occurrences

- **Status:** Proposed (modeling decision; rule subset Provisional)

## Context / problem
Recurring events are MVP scope. Cancelling/editing one instance must never affect others unintentionally; registrations and capacity are per-instance; queries ("events this week") need concrete times.

## Options considered
1. **Series row (rule) + materialized occurrence rows**; registrations attach to occurrences; single events = event with exactly one occurrence (no series row or a trivial one).
2. Rule only, expand at query time — edits/exceptions per instance become painful; capacity checks need virtual rows.
3. Copy-the-event clones with no series link — no series-level management; cancellation semantics break.

## Decision
Option 1. Rule = validated RFC-5545-inspired subset stored as JSONB on `event_series` (frequency/interval/weekdays/end), anchored to the event timezone; occurrences materialized up to a rolling window (Provisional 6 months) and refreshed by a scheduled job/command; bounds: min interval, ≤366 occurrences/series (T-16). Once materialized, an occurrence is independently editable/cancellable; series edits regenerate only unmaterialized/future-unmodified occurrences (Phase 4 must spec the exact regeneration semantics and test them — flagged as the phase's highest-risk deliverable).

## Consequences
Uniform registration logic; storage bounded; DST/timezone correctness depends on Phase 4 tests using the event timezone, never server-local time.

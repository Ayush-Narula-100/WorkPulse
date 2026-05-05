# WorkPulse v2.0 — Project State

## Project Reference

See: .planning/PROJECT.md (updated 2026-05-05)

**Core value:** Enable managers to see real-time workload pressure across their team using explainable AI predictions
**Current focus:** Phase 1 — Backend Health & Local Dev Boot

## Milestone

**v1.0** — Get it fully running and demoable

## Decisions

- YOLO mode: auto-approve, no interactive gates
- Standard granularity: 5-8 phases, balanced scope
- Model profile: inherit (uses current session model)
- No Postgres/Redis required: in-memory fallback works
- No JWT backend: demo auth via localStorage

## Blockers

(none)

## Notes

- Project is brownfield — most code already exists
- Primary work: integration, wiring, and polishing
- Backend starts in-memory mode when Postgres/Redis unavailable
- 4 demo accounts: sarah (manager), alex (member), maya (team_lead), david (admin)

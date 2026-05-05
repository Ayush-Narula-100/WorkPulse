# WorkPulse v2.0 — Project Context

## What This Is

**WorkPulse** is an AI-powered team workload intelligence system. It combines a Mamdani fuzzy inference engine, a Sugeno engine, a 4-layer neural network, and Kernel SHAP to predict individual workload levels, surface burnout risk, and generate 7-day LSTM+ARIMA forecasts. Managers get a rich dashboard; members get a personal workload view; the collaboration tab shows knowledge silo detection and Brooks' Law analysis.

**Stack:**
- **Backend:** Node.js 20 + Express + TypeScript, port 4000. In-memory fallback when Postgres/Redis are unavailable.
- **Frontend:** Next.js 14 App Router + Zustand + pure CSS (no Tailwind), port 3000. Proxies `/api/*` → backend.
- **DB layer:** PostgreSQL 16 (primary), Redis 7 (rate-limit cache, optional), in-memory fallbacks for both.
- **Auth:** Demo-mode client-side auth (localStorage), RBAC roles: member, team_lead, manager, hr, executive, admin.

## Core Value

Enable engineering managers and team leads to see real-time workload pressure across their team — surfacing burnout risk, knowledge silos, and sprint overcommits before they become crises — using explainable AI predictions (SHAP feature contributions).

## Requirements

### Validated (already exists in codebase)

- ✓ Mamdani fuzzy engine (55 rules, MIN aggregation, centroid defuzz) — `backend/src/core/fuzzy/mamdani`
- ✓ Sugeno engine (6 Gaussian-antecedent rules, weighted average) — `backend/src/core/fuzzy/sugeno`
- ✓ Hybrid fusion (4 strategies: weighted_average, contextual_switch, ensemble, adaptive) — `backend/src/core/fuzzy/hybrid`
- ✓ Neural network (4-layer DNN, ReLU→tanh→sigmoid) — `backend/src/core/ml/`
- ✓ Kernel SHAP (17 features, deviation-weighted, generates recommendations) — `backend/src/core/ml/`
- ✓ LSTM+ARIMA 7-day forecast ensemble — `backend/src/core/forecast/`
- ✓ Brooks' Law coordination overhead + synergy detection — `backend/src/core/collab/`
- ✓ 10 escalation rules (burnout, bus factor, silos, sprint overcommit, trend alerts) — `backend/src/core/alerts/`
- ✓ 6 RBAC roles with permission inheritance — `backend/src/core/rbac/`
- ✓ Checkpoint system (50 manual / 10 auto rotation, Postgres snapshots) — `backend/src/db/`
- ✓ SOC2-style audit log (immutable append-only) — `backend/src/db/`
- ✓ Redis rate limiting with in-memory fallback — `backend/src/middleware/rateLimit.ts`
- ✓ PostgreSQL + Redis with in-memory fallback for both — `backend/src/db/`
- ✓ Manager Dashboard page — `frontend/src/app/dashboard/page.tsx` (27 KB)
- ✓ Member Dashboard page — `frontend/src/app/member/page.tsx` (20 KB)
- ✓ Collab page — `frontend/src/app/collab/page.tsx` (24 KB)
- ✓ Login page with demo accounts — `frontend/src/app/login/page.tsx`
- ✓ Signup page (2-step flow) — `frontend/src/app/signup/page.tsx`
- ✓ Gauge + Sparkline common components — `frontend/src/components/common/`
- ✓ Next.js proxy `/api/*` → `localhost:4000` — `frontend/next.config.js`
- ✓ Docker Compose (postgres + redis + backend + frontend) — `docker-compose.yml`

### Active (needs work)

- [ ] Backend starts cleanly without Postgres/Redis (in-memory mode proven)
- [ ] Frontend builds and runs without TypeScript errors
- [ ] All pages render correctly with live backend data
- [ ] Dashboard component (`frontend/src/components/dashboard/Dashboard.tsx`) wired into dashboard page
- [ ] Broken directory names cleaned up (stray `{backend`, `{login,signup,...}` dirs in repo)
- [ ] End-to-end smoke test: login → dashboard → member view → collab → logout
- [ ] Auth store (Zustand) wired to frontend pages
- [ ] Gauge component integrated into member + dashboard pages

### Out of Scope

- Real JWT authentication backend — using demo client-side auth (localStorage) for now
- Production deployment — Docker Compose provided but not a current priority
- Mobile responsive layout — desktop-first
- Automated test suite — manual UAT only for this milestone

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| No Tailwind | Pure CSS design system, light theme, full control | Already implemented |
| In-memory fallback | Works without Postgres/Redis in local dev | Already implemented |
| Demo auth (localStorage) | Avoid full auth backend, focus on AI features | Already implemented |
| Next.js proxy | Avoids CORS issues in dev | Already implemented in next.config.js |
| YOLO mode | Fast execution, no approval gates | User selected |
| Standard granularity | 5-8 phases, balanced scope | Selected |

## Context

This is a **brownfield project** — the codebase is substantially built. The work is:
1. Fix any remaining integration/compilation issues
2. Wire up components that exist but may not be connected
3. Verify the full user journey works end-to-end
4. Polish and ensure the UI is production-quality

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition:**
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions

**After each milestone:**
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-05-05 after initialization*

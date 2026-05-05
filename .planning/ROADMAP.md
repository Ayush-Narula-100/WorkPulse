# Roadmap: WorkPulse v2.0

## Overview

WorkPulse is substantially built — the fuzzy logic engines, ML pipeline, SHAP explainability, LSTM forecasting, RBAC, checkpoints, and audit log all exist in the backend. The frontend has all major pages (dashboard, member, collab, login, signup). This milestone gets everything running end-to-end: fix integration issues, wire live data to all pages, ensure the auth flow works correctly, and polish the UI to production quality.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

- [ ] **Phase 1: Backend Health** - Verify backend compiles and all API routes respond correctly in in-memory mode
- [ ] **Phase 2: Frontend Build** - Fix TypeScript errors and ensure all pages compile cleanly
- [ ] **Phase 3: Auth Flow** - Login/signup/logout and role-based routing work correctly
- [ ] **Phase 4: Manager Dashboard** - Dashboard page loads live data from backend
- [ ] **Phase 5: Member Dashboard** - Member page shows personal workload, SHAP, and forecast
- [ ] **Phase 6: Collaboration Page** - Collab page shows team network, silos, and Brooks' Law
- [ ] **Phase 7: Navigation & Polish** - Role-aware nav, loading states, consistent UI
- [ ] **Phase 8: End-to-End Smoke Test** - Full user journey works, app is ready to demo

## Phase Details

### Phase 1: Backend Health
**Goal**: Backend starts reliably in in-memory mode (no Postgres/Redis required), TypeScript compiles cleanly, and all API routes respond correctly
**Depends on**: Nothing (first phase)
**Requirements**: Backend boot, API routes
**Success Criteria** (what must be TRUE):
  1. `npm run dev` in `/backend` starts without errors
  2. `GET /api/health` returns `{ status: "operational" }` with rules count > 0
  3. `POST /api/predict/all` returns predictions for all team members
  4. `GET /api/alerts` returns array (not error)
  5. No TypeScript compilation errors
**Plans**: 3 plans

Plans:
- [ ] 01-01: Verify backend TypeScript compiles (`tsc --noEmit`) and fix any type errors
- [ ] 01-02: Start backend in dev mode, confirm all core module imports resolve
- [ ] 01-03: Smoke-test all API endpoints via curl/fetch

### Phase 2: Frontend Build
**Goal**: Frontend builds without TypeScript errors and all pages are importable
**Depends on**: Phase 1
**Requirements**: Frontend compilation, clean imports
**Success Criteria** (what must be TRUE):
  1. `npm run dev` in `/frontend` starts without compilation errors
  2. No TypeScript errors in dashboard/member/collab/login/signup pages
  3. All component imports resolve (Gauge, Sparkline, auth hooks)
  4. Browser console shows no critical import errors on page load
**Plans**: 3 plans

Plans:
- [ ] 02-01: Run `tsc --noEmit` in `/frontend` and fix all type errors
- [ ] 02-02: Audit all import paths in pages — fix missing imports and broken directory artifacts
- [ ] 02-03: Verify `next dev` starts and home page loads without errors

### Phase 3: Auth Flow
**Goal**: Login, signup, and logout work correctly. Route guards redirect unauthenticated users. Demo accounts land on correct dashboards.
**Depends on**: Phase 2
**Requirements**: Auth flow, role-based routing
**Success Criteria** (what must be TRUE):
  1. `sarah@workpulse.io` logs in → redirects to `/dashboard`
  2. `alex@workpulse.io` logs in → redirects to `/member`
  3. Signup completes and redirects to appropriate page
  4. Logout clears session and redirects to `/login`
  5. Unauthenticated users hitting `/dashboard` redirect to `/login`
**Plans**: 3 plans

Plans:
- [ ] 03-01: Audit AuthProvider wrapping in layout.tsx — ensure it covers all pages
- [ ] 03-02: Verify login demo card selection + submit → correct redirect by role
- [ ] 03-03: Implement route protection for dashboard/member/collab pages

### Phase 4: Manager Dashboard
**Goal**: Dashboard page loads real data from the backend, all panels render correctly with no placeholder/empty states
**Depends on**: Phase 3
**Requirements**: Live data in dashboard, Gauge component, alerts panel
**Success Criteria** (what must be TRUE):
  1. Dashboard shows all team members with their workload scores from backend
  2. Alert badges display real active alerts
  3. Team metric summary cards show correct aggregated numbers (avg score, burnout count)
  4. Gauge component renders with correct score values (0-100)
  5. No "undefined" or NaN values visible in the UI
**Plans**: 4 plans

Plans:
- [ ] 04-01: Wire `api.getTeam()` and `api.predictAll()` into dashboard — replace static data
- [ ] 04-02: Wire `api.getAlerts()` and `api.getTeamMetrics()` into summary panels
- [ ] 04-03: Wire `api.getAllForecasts()` and verify Gauge/Sparkline render correctly
- [ ] 04-04: Wire `api.getFusionConfig()` + `api.updateFusionConfig()` into fusion config panel

### Phase 5: Member Dashboard
**Goal**: Member page shows the logged-in user's personal workload data, SHAP recommendations, and 7-day forecast
**Depends on**: Phase 4
**Requirements**: Personal workload view, SHAP recommendations, forecast sparkline
**Success Criteria** (what must be TRUE):
  1. Logged in as `alex@workpulse.io` → member page shows Alex's specific data
  2. SHAP recommendations panel shows top contributing factors with explanations
  3. Forecast sparkline shows 7 days of projected workload
  4. Changing input sliders triggers a new prediction and updates the score display
  5. Workload level badge (Optimal / Overloaded / Burnout Risk) updates correctly
**Plans**: 3 plans

Plans:
- [ ] 05-01: Wire member page to identify current user's memberId via useAuth() and fetch personal data
- [ ] 05-02: Render SHAP feature contributions as ranked recommendations list
- [ ] 05-03: Wire input sliders to `api.updateInputs()` with live prediction refresh

### Phase 6: Collaboration Page
**Goal**: Collab page shows team collaboration network, knowledge silo detection, and Brooks' Law analysis
**Depends on**: Phase 5
**Requirements**: Collaboration network, silo detection, Brooks' Law panel
**Success Criteria** (what must be TRUE):
  1. Collab page loads and shows collaboration network/pairs data
  2. Knowledge silo risk displayed for each team member
  3. Selecting a member shows their collaboration impact score
  4. Brooks' Law panel displays coordination overhead metrics
  5. Page accessible to managers/team_leads, not members
**Plans**: 3 plans

Plans:
- [ ] 06-01: Wire `api.getCollaborations()` and `api.getKnowledgeSilos()` to render collaboration data
- [ ] 06-02: Wire `api.getCollabImpact(memberId)` for selected member impact
- [ ] 06-03: Verify role-based access and Brooks' Law panel renders

### Phase 7: Navigation & Polish
**Goal**: Navigation shell works across all pages, UI is polished and consistent, no rough edges
**Depends on**: Phase 6
**Requirements**: Role-aware nav, loading states, consistent design system
**Success Criteria** (what must be TRUE):
  1. Nav links are role-aware (member can't see manager dashboard link)
  2. All pages have consistent visual language (font, spacing, card styles)
  3. Loading states appear while data fetches in-flight (spinner or skeleton)
  4. Error states show gracefully when backend unavailable
  5. Light theme looks premium and professional throughout
**Plans**: 3 plans

Plans:
- [ ] 07-01: Audit layout/nav component — implement role-aware link visibility
- [ ] 07-02: Add loading skeletons and error boundaries to all async data fetches
- [ ] 07-03: CSS audit — ensure design system tokens consistent across all pages

### Phase 8: End-to-End Smoke Test
**Goal**: Complete user journey works without errors, app is ready to demo
**Depends on**: Phase 7
**Requirements**: All flows working, in-memory fallback, checkpoint/audit verified
**Success Criteria** (what must be TRUE):
  1. All 4 demo accounts log in and see appropriate dashboards with live data
  2. Backend starts in in-memory mode without external services
  3. Checkpoint save + restore works without errors
  4. Audit log populates on user actions
  5. App can be demoed end-to-end without errors or empty states
**Plans**: 3 plans

Plans:
- [ ] 08-01: Full E2E run — login as each demo account and verify correct page with data
- [ ] 08-02: Verify in-memory mode — confirm graceful fallback when no Postgres/Redis
- [ ] 08-03: Test checkpoint create/restore and audit log population

## Progress

**Execution Order:**
Phases execute in order: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Backend Health | 0/3 | Not started | - |
| 2. Frontend Build | 0/3 | Not started | - |
| 3. Auth Flow | 0/3 | Not started | - |
| 4. Manager Dashboard | 0/4 | Not started | - |
| 5. Member Dashboard | 0/3 | Not started | - |
| 6. Collaboration Page | 0/3 | Not started | - |
| 7. Navigation & Polish | 0/3 | Not started | - |
| 8. End-to-End Smoke Test | 0/3 | Not started | - |

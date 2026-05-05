# WorkPulse v2.0 — Milestone v1.0 Roadmap

## Milestone Goal

Get WorkPulse fully running locally: backend starts cleanly, frontend builds without errors, all pages load with live data, and the complete user journey works end-to-end. Polish any UI gaps and verify the application feels production-quality.

---

## Phase 1 — Backend Health & Local Dev Boot

**Goal:** Backend starts reliably in in-memory mode (no Postgres/Redis required), TypeScript compiles cleanly, and all API routes respond correctly.

**Plans:**
1. Verify backend TypeScript compiles (`tsc --noEmit`) and fix any type errors
2. Start backend in dev mode, confirm all core module imports resolve (fuzzy, ML, forecast, collab, alerts, rbac)
3. Smoke-test all API endpoints: `/api/health`, `/api/team`, `/api/predict/all`, `/api/forecasts`, `/api/alerts`, `/api/rbac/roles`, `/api/checkpoints`, `/api/audit`

**UAT Criteria:**
- `npm run dev` in `/backend` starts without errors
- `GET /api/health` returns `{ status: "operational" }` with rules count
- `POST /api/predict/all` returns predictions for all 4 team members
- `GET /api/alerts` returns array of active alert objects
- No TypeScript compilation errors

**UI hint:** no

---

## Phase 2 — Frontend Build & TypeScript Fixes

**Goal:** Frontend builds without TypeScript errors and all pages are importable.

**Plans:**
1. Run `tsc --noEmit` in `/frontend` and fix all type errors
2. Audit all `import` paths in pages (dashboard, member, collab, login, signup) — fix missing imports
3. Fix broken directory artifacts: remove/rename stray `{backend` and `{login,signup,...}` dirs that may cause build issues
4. Verify `next build` or `next dev` starts cleanly

**UAT Criteria:**
- `npm run dev` in `/frontend` starts without compilation errors
- No red TypeScript errors in dashboard/member/collab/login/signup pages
- All component imports resolve (Gauge, Sparkline, Dashboard component, auth hooks)
- Browser console shows no critical import errors on page load

**UI hint:** no

---

## Phase 3 — Auth Flow & Page Routing

**Goal:** Login, signup, and logout work correctly. Route guards redirect unauthenticated users. Demo accounts land on correct dashboards.

**Plans:**
1. Audit `AuthProvider` wrapping in `layout.tsx` — ensure it wraps all pages
2. Verify login flow: select demo account → submit → redirect to `/dashboard` (manager/admin) or `/member` (member/team_lead)
3. Verify signup flow: 2-step form → create account → redirect to appropriate dashboard
4. Verify logout clears localStorage and redirects to `/login`
5. Add route protection: unauthenticated users hitting `/dashboard` or `/member` redirect to `/login`

**UAT Criteria:**
- Clicking a demo account card + "Sign in" redirects correctly based on role
- Signup completes and redirects to correct page
- Logout works and prevents re-accessing protected pages without login
- `sarah@workpulse.io` → `/dashboard`, `alex@workpulse.io` → `/member`

**UI hint:** yes

---

## Phase 4 — Manager Dashboard — Live Data Integration

**Goal:** Dashboard page loads real data from the backend, all panels render correctly with no placeholder/empty states.

**Plans:**
1. Wire `api.getTeam()` and `api.predictAll()` into dashboard page — replace any static/hardcoded data
2. Wire `api.getAlerts()` into alerts panel
3. Wire `api.getTeamMetrics()` into summary metric cards (avg score, burnout count, overloaded count)
4. Wire `api.getAllForecasts()` into forecast panel
5. Wire `api.getFusionConfig()` + `api.updateFusionConfig()` into fusion config panel (if present)
6. Verify Gauge and Sparkline components render correctly with real prediction scores

**UAT Criteria:**
- Dashboard loads all 4 team members with their workload scores
- Alert badges show real active alerts from backend
- Team metric summary cards show correct aggregated numbers
- Gauge component renders with correct score values (0-100)
- No "undefined" or NaN values visible in the UI
- Fusion config controls update predictions in real-time

**UI hint:** yes

---

## Phase 5 — Member Dashboard — Personal View Integration

**Goal:** Member page shows the logged-in user's personal workload data, SHAP recommendations, and 7-day forecast.

**Plans:**
1. Wire member page to use `useAuth()` to identify the current user's `memberId`
2. Fetch `api.getMember(memberId)` and `api.predictMember(memberId)` for personal data
3. Render SHAP feature contributions (from prediction result) as a ranked list of recommendations
4. Render 7-day workload forecast from `api.getForecast(memberId)` using Sparkline
5. Wire input sliders (hours/week, task complexity, meetings, etc.) to `api.updateInputs()` with live prediction refresh

**UAT Criteria:**
- Logged-in as `alex@workpulse.io` → member page shows Alex's specific data
- SHAP recommendations panel shows top contributing factors
- Forecast sparkline shows 7 days of projected workload
- Changing input sliders triggers a new prediction and updates the score display
- Workload level badge (Optimal / Overloaded / Burnout Risk) updates correctly

**UI hint:** yes

---

## Phase 6 — Collaboration Page Integration

**Goal:** Collab page shows team collaboration network, knowledge silo detection, and Brooks' Law analysis.

**Plans:**
1. Wire `api.getCollaborations()` to render collaboration pairs/network
2. Wire `api.getKnowledgeSilos()` to render silo risk indicators per member
3. Wire `api.getCollabImpact(memberId)` for selected member's collaboration impact score
4. Ensure Brooks' Law coordination overhead display renders correctly
5. Verify collab page is accessible from navigation for manager/team_lead roles

**UAT Criteria:**
- Collab page loads and shows collaboration network data
- Knowledge silo risk is displayed for each team member
- Selecting a member shows their collaboration impact score
- Brooks' Law panel displays coordination overhead metrics
- Page is accessible to managers and team leads, not members

**UI hint:** yes

---

## Phase 7 — Navigation, Layout & UI Polish

**Goal:** Navigation shell works across all pages. UI is polished, consistent, and visually premium. No rough edges.

**Plans:**
1. Audit layout component — ensure nav links work for all roles (manager sees dashboard + collab + team; member sees only member view)
2. Verify CSS design system is consistent across all pages (colors, typography, spacing from `globals.css`)
3. Polish any visual gaps: empty states, loading skeletons, error boundaries
4. Add loading states to all async data fetches (spinner or skeleton cards)
5. Verify mobile/tablet layout is acceptable (no horizontal overflow, nav collapses if needed)

**UAT Criteria:**
- Nav links are role-aware (member can't navigate to manager dashboard)
- All pages have consistent visual language (same font, spacing, card styles)
- Loading states appear while data fetches in-flight
- Error states show gracefully when backend is unavailable
- No visual regressions — the light theme looks premium and professional

**UI hint:** yes

---

## Phase 8 — End-to-End Smoke Test & Final Fixes

**Goal:** Complete user journey works without errors. App is ready to demo.

**Plans:**
1. Full E2E run: start backend → start frontend → login as each of 4 demo accounts → verify correct page renders with data
2. Fix any remaining bugs or visual issues discovered in testing
3. Verify in-memory mode: kill Postgres/Redis if running, restart backend, confirm graceful fallback
4. Test checkpoint creation and restore flow from the dashboard
5. Verify audit log populates on user actions

**UAT Criteria:**
- All 4 demo accounts can log in and see appropriate dashboards with live data
- Backend starts cleanly in in-memory mode (no external services required)
- Checkpoint save + restore works without errors
- Audit log shows recent actions
- App can be demoed end-to-end without hitting any errors or empty states

**UI hint:** no

---

*Milestone v1.0 | 8 phases | Created 2026-05-05*

# hermes-saas Feature Audit

**Date:** 2025-05-18
**Files examined:** `app/main.py`, `frontend/index.html`, `start.sh`, `entrypoint.py`
**Branding verified:** Cortex + brain SVG + `#3b82f6` blue ✅

---

## ✅ What Looks Good

- **Branding:** Correct — Cortex name, brain SVG, blue `#3b82f6` accent, no purple
- **Frontend path in backend:** `os.path.join(os.path.dirname(__file__), "..", "app", "frontend", "index.html")` → resolves to `/app/app/frontend/index.html` inside container ✅ (matches known-correct hot-fix path)
- **Container structure:** `/app/app/frontend/` is the served path; `/app/frontend/` is stale
- **Kanban board UI:** Present in frontend (5-column board, dark theme, projects/usage/settings sections)
- **Entrypoint:** `entrypoint.py` starts Hermes dashboard on :9119 + FastAPI on :8000

---

## 🔴 Missing / Broken

### Auth (HIGH priority)

- **No login/signup logic** — forms are UI only, no API calls to backend
- **No session token handling** — frontend has no auth headers on API requests
- **No tenant isolation in API** — all requests assumed authenticated
- **What exists:** `start.sh` extracts session token from Hermes dashboard, but frontend doesn't use it

**Fix needed:**
- Add `POST /api/auth/login` and `POST /api/auth/logout` to `main.py`
- Frontend stores token (localStorage or in-memory), sends `Authorization: Bearer <token>` on all API requests
- Backend middleware checks tenant ID from token

---

### Frontend-Backend API Integration (HIGH priority)

- **Kanban board is pure JS/CSS** — no `fetch`/`WebSocket` calls to actually load tasks
- **Projects section** — UI only, no `GET /api/projects` call
- **Usage section** — static placeholder text
- **Settings section** — UI only

**Fix needed:**
- Wire kanban board to `GET /api/kanban/tasks` and `WebSocket /ws/kanban`
- Add `POST /api/projects` for project creation
- Add usage tracking API

---

### Tenant Isolation (MEDIUM priority)

- No `tenant_id` in requests
- No middleware to extract tenant from auth token
- All data served to all clients (multi-tenancy broken by design)

---

### Notifier / WebSocket (MEDIUM priority)

- No WebSocket endpoint in `main.py`
- Kanban live updates won't work without WS
- Telegram notifier configured but not tested in this audit run

---

## 📋 Prioritized Bug/Feature List

| # | Priority | Item | Type |
|---|----------|------|------|
| 1 | 🔴 HIGH | Add auth endpoints (login/logout) + JWT middleware | Feature |
| 2 | 🔴 HIGH | Wire frontend kanban to backend API | Bug |
| 3 | 🔴 HIGH | Add `tenant_id` to all API requests | Bug |
| 4 | 🟡 MED | Add WebSocket endpoint for live kanban updates | Feature |
| 5 | 🟡 MED | Add `GET /api/projects` + project CRUD | Feature |
| 6 | 🟡 MED | Connect usage section to real data | Bug |
| 7 | 🟢 LOW | Settings UI — actual save/load | Feature |

---

## Container Hot-Fix Reminder

- Correct path: `/app/app/frontend/index.html`
- Wrong path: `/app/frontend/` (stale, not served)
- See: `/home/cerebro/.hermes/scripts/hermes-saas-deploy.sh` (deploy script with correct path)

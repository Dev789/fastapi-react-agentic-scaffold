---
description: Profile and optimize backend and frontend performance with actionable findings
---

# Performance Audit Workflow

## When to Use
- Before a major release with new features
- When users report slow loading or interactions
- When monitoring shows increased latency or error rates
- As a regular quarterly health check

## Steps

### 1. Define Baseline Metrics
Before optimizing, measure the current state:

#### Backend Metrics
| Metric | How to Measure | Target |
|---|---|---|
| API response time (p50, p95) | Application logs or APM tool | p95 < 500ms |
| Slowest endpoints | Sort by avg response time | Identify top 5 |
| Database query time | `EXPLAIN ANALYZE` on slow queries | < 100ms per query |
| N+1 query count | Enable SQLAlchemy echo mode or query logging | 0 |

#### Frontend Metrics
| Metric | How to Measure | Target |
|---|---|---|
| Largest Contentful Paint (LCP) | Chrome Lighthouse / DevTools | < 2.5s |
| First Input Delay (FID) | Chrome Lighthouse | < 100ms |
| Cumulative Layout Shift (CLS) | Chrome Lighthouse | < 0.1 |
| Bundle size (JS) | `npx vite-bundle-visualizer` | < 250KB gzipped |
| Time to Interactive (TTI) | Chrome Lighthouse | < 3.5s |

### 2. Backend — Database Profiling
```bash
# Enable query logging in development
# In backend/app/db/session.py, temporarily:
engine = create_async_engine(DATABASE_URL, echo=True)
```

Run the application and trigger common user flows. Look for:
- [ ] **N+1 queries:** Multiple identical SELECT statements → fix with `selectin` eager loading.
- [ ] **Missing indices:** `EXPLAIN ANALYZE` shows sequential scans on large tables → add B-Tree index.
- [ ] **Slow joins:** Consider denormalization or materialized views for read-heavy queries.
- [ ] **Unbounded queries:** Missing `LIMIT` on list endpoints → enforce pagination.

### 3. Backend — Endpoint Profiling
```bash
# Add timing middleware (if not already present)
# Or use: pip install py-spy
py-spy record -o profile.svg -- python -m uvicorn app.main:app
```
- Identify endpoints with p95 > 500ms.
- Check if slow endpoints can benefit from **caching** (Redis).
- Check if heavy computations can be **offloaded** to background tasks (Celery).

### 4. Frontend — Lighthouse Audit
```bash
# Run Lighthouse from CLI
npx lighthouse http://localhost:5173 --output=html --output-path=./lighthouse-report.html
```
Review the report and focus on:
- [ ] **Large JS bundles:** Use `React.lazy()` for route-level code splitting.
- [ ] **Unoptimized images:** Convert to WebP, add `width`/`height`, lazy-load below the fold.
- [ ] **Unused CSS:** Tailwind purges unused classes in production — ensure `content` config is correct.
- [ ] **Render-blocking resources:** Defer non-critical scripts and CSS.

### 5. Frontend — Bundle Analysis
```bash
cd frontend
npx vite-bundle-visualizer
```
Look for:
- [ ] **Large dependencies:** Can any be replaced with lighter alternatives?
- [ ] **Duplicated modules:** Multiple versions of the same library?
- [ ] **Unused imports:** Tree-shaking not working for a library? Check for side-effect imports.

### 6. Frontend — React Profiler
- Open React DevTools → Profiler tab.
- Record a common user flow.
- Look for:
  - [ ] Components re-rendering unnecessarily → add `React.memo`.
  - [ ] Expensive renders → use `useMemo`/`useCallback`.
  - [ ] Prop drilling causing cascading re-renders → use Redux selectors or context.

### 7. Document Findings
Create an audit report with:
```markdown
## Performance Audit — <Date>

### Summary
- Backend p95: XXXms (target: <500ms) — ✅/❌
- Frontend LCP: XXXs (target: <2.5s) — ✅/❌
- Bundle size: XXXkb (target: <250kb) — ✅/❌

### Findings
| # | Area | Issue | Impact | Fix | Effort |
|---|---|---|---|---|---|
| 1 | Backend | N+1 on /orders endpoint | High | Add selectin loading | 2h |
| 2 | Frontend | Large moment.js bundle | Medium | Replace with dayjs | 1h |

### Action Items
- [ ] Create tickets for each finding
- [ ] Prioritize by impact/effort ratio
```

### 8. Create Optimization Tickets
For each finding, create a ticket following PM rules (`.agents/rules/pm.md`):
- Title, priority, story points, description, acceptance criteria.
- Link back to the audit report.

---
name: performance-profiler
description: Profile backend endpoints, database queries, frontend bundles, and rendering performance. Use when latency rises, pages feel slow, Lighthouse scores regress, or you need a repeatable performance audit and follow-up optimization plan.
---

# Performance Profiler

## Steps

### 1. Define Baseline Metrics
Before optimizing, measure the current state.

#### Backend Metrics
| Metric | How to Measure | Target |
|---|---|---|
| API response time (p50, p95) | Application logs or APM tool | p95 < 500ms |
| Slowest endpoints | Sort by average response time | Identify top 5 |
| Database query time | `EXPLAIN ANALYZE` on slow queries | < 100ms per query |
| N+1 query count | Enable SQLAlchemy echo mode or query logging | 0 |

#### Frontend Metrics
| Metric | How to Measure | Target |
|---|---|---|
| Largest Contentful Paint (LCP) | Chrome Lighthouse or DevTools | < 2.5s |
| First Input Delay (FID) | Chrome Lighthouse | < 100ms |
| Cumulative Layout Shift (CLS) | Chrome Lighthouse | < 0.1 |
| Bundle size (JS) | `npx vite-bundle-visualizer` | < 250KB gzipped |
| Time to Interactive (TTI) | Chrome Lighthouse | < 3.5s |

### 2. Backend - Database Profiling
```bash
# Enable query logging in development
# In backend/app/db/session.py, temporarily:
engine = create_async_engine(DATABASE_URL, echo=True)
```

Run the application and trigger common user flows. Look for:
- [ ] N+1 queries and fix them with eager loading where appropriate.
- [ ] Missing indexes on large scans surfaced by `EXPLAIN ANALYZE`.
- [ ] Slow joins that may need query changes, denormalization, or materialized views.
- [ ] Unbounded queries on list endpoints.

### 3. Backend - Endpoint Profiling
```bash
# Add timing middleware if not already present
# Or use:
py-spy record -o profile.svg -- python -m uvicorn app.main:app
```

- Identify endpoints with p95 > 500ms.
- Check whether slow endpoints would benefit from caching.
- Check whether heavy computations should move to background jobs.

### 4. Frontend - Lighthouse Audit
```bash
npx lighthouse http://localhost:5173 --output=html --output-path=./lighthouse-report.html
```

Review the report and focus on:
- [ ] Large JS bundles that need code splitting.
- [ ] Unoptimized images that need better formats or dimensions.
- [ ] Unused CSS caused by incorrect Tailwind `content` configuration.
- [ ] Render-blocking resources that can be deferred.

### 5. Frontend - Bundle Analysis
```bash
cd frontend
npx vite-bundle-visualizer
```

Look for:
- [ ] Large dependencies that could be replaced or lazy-loaded.
- [ ] Duplicated modules.
- [ ] Tree-shaking failures from unused or side-effect imports.

### 6. Frontend - React Profiler
- Open React DevTools and use the Profiler tab.
- Record a representative user flow.
- Look for:
  - [ ] Unnecessary re-renders.
  - [ ] Expensive renders that need restructuring or memoization.
  - [ ] Prop drilling that causes cascading re-renders.

### 7. Document Findings
Create an audit report:

```markdown
## Performance Audit - <Date>

### Summary
- Backend p95: XXXms (target: <500ms) - pass/fail
- Frontend LCP: XXXs (target: <2.5s) - pass/fail
- Bundle size: XXXKB (target: <250KB) - pass/fail

### Findings
| # | Area | Issue | Impact | Fix | Effort |
|---|---|---|---|---|---|
| 1 | Backend | N+1 on /orders endpoint | High | Add selectin loading | 2h |
| 2 | Frontend | Large date library bundle | Medium | Replace with a smaller alternative | 1h |

### Action Items
- [ ] Create tickets for each finding
- [ ] Prioritize by impact and effort
```

### 8. Create Optimization Tickets
For each finding, create a ticket following `.agents/rules/pm.md`:
- Include title, priority, story points, description, and acceptance criteria.
- Link back to the audit report.

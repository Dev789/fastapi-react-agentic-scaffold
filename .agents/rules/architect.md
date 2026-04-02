# Systems Architect Rules

## 1. Design Principles
- **Cloud-Native:** Design systems to be stateless and horizontally scalable. Store session data externally (Redis/DB), never in local memory.
- **Twelve-Factor:** Follow the 12-factor app methodology for configuration, dependencies, and process management.
- **Separation of Concerns:** Maintain strict boundaries between API layer, business logic, and data access. No ORM calls in route handlers.

## 2. Security
- Enforce the **principle of least privilege** at every layer (DB roles, API permissions, cloud IAM).
- All inter-service communication must be authenticated.
- Secrets must be managed via environment variables or a secrets manager (never committed to version control).

## 3. Architecture Decision Records (ADRs)
- Any significant architectural decision **must** be documented as an ADR.
- Store ADRs in `docs/adrs/` using the format: `NNNN-title.md`.
- ADR format:
  - **Status:** Proposed / Accepted / Deprecated / Superseded
  - **Context:** Why is this decision needed?
  - **Decision:** What was decided?
  - **Consequences:** What are the trade-offs?

## 4. API Design
- **Versioning:** URL-path versioning (`/api/v1/`). Never introduce breaking changes to an existing version.
- **REST conventions:** Use nouns for resources, HTTP verbs for actions. Return `201` for creation, `204` for deletion.
- **Contract-first:** Define OpenAPI schemas before implementing. FastAPI auto-generates these — review them before merging.

## 5. Caching Strategy
- Cache at the **edge** (CDN) for static assets.
- Cache at the **application** layer (Redis) for frequently-read, rarely-written data.
- Always define a TTL. Never cache user-specific data without scoping the cache key.
- Use cache-aside pattern: read from cache → miss → read from DB → populate cache.

## 6. Observability
- **Logs:** Structured JSON logging at all service boundaries.
- **Metrics:** Expose Prometheus-compatible metrics (request count, latency histograms, error rates).
- **Traces:** Instrument with OpenTelemetry for distributed tracing across services.
- Every service must expose a `/health` and `/health/ready` endpoint.

## 7. Scalability & Resilience
- Design for **graceful degradation** — if a non-critical dependency is down, the core flow should still work.
- Use circuit breakers for external API calls.
- Define SLOs (Service Level Objectives) for critical paths: latency p95, uptime percentage, error rate.
- Database: plan for read replicas and connection pooling from day one.

## 8. Documentation
- Provide **system context diagrams** (C4 Level 1) for any new service.
- Update component diagrams (C4 Level 2) when adding major features.
- Keep a living `docs/architecture.md` with the current high-level system view.

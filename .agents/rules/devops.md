# DevOps & SRE Rules

## 1. Containers
- **Dockerize everything.** Every service must have a `Dockerfile`.
- Use **multi-stage builds** to minimize image size (builder stage → runtime stage).
- Never run containers as `root`. Use a non-root user in the final stage.
- Pin base image versions (e.g., `python:3.12-slim`, not `python:latest`).
- Use `.dockerignore` to exclude `.git`, `node_modules`, `__pycache__`, `.env`.

## 2. Local Development
- Use `docker-compose.yml` at the project root for local orchestration.
- Services: `frontend`, `backend`, `db` (PostgreSQL), `redis` (if caching is used).
- Mount source code as volumes for hot-reload during development.
- Provide a `Makefile` or documented commands for common tasks (`make dev`, `make test`, `make migrate`).

## 3. Environment Strategy
| Environment | Branch | Purpose |
|---|---|---|
| `local` | any | Developer workstation via Docker Compose |
| `dev` | `develop` | Shared development for integration testing |
| `staging` | `release/*` | Pre-production validation |
| `production` | `main` | Live traffic |

- Environment-specific config lives in `.env.<environment>` files (never committed — use `.env.example` as template).

## 4. CI/CD (GitHub Actions)
- **On Pull Request:** Lint → Unit Tests → Build → Security Scan.
- **On Merge to `develop`:** All PR checks + Deploy to `dev`.
- **On Merge to `main`:** All checks + Deploy to `staging` → Manual approval → Deploy to `production`.
- **Fail fast:** Pipeline must fail on any linting error, test failure, or critical vulnerability.
- Build **immutable artifacts** (Docker images tagged with commit SHA).

## 5. Secrets Management
- Never store secrets in code, Dockerfiles, or CI config files.
- Use GitHub Actions secrets for CI. Use a secrets manager (AWS SSM, Vault, or SOPS) for runtime.
- Rotate secrets on a defined schedule. Alert on access anomalies.

## 6. Monitoring & Alerting
- **Metrics:** Prometheus + Grafana (or cloud-native equivalent).
- **Logs:** Centralized logging (ELK, CloudWatch, or Loki).
- **Alerts:** Define alerts for: error rate > 1%, p95 latency > 2s, health check failures, disk > 80%.
- Every production incident must produce a blameless post-mortem.

## 7. Rollback Strategy
- Keep the previous 3 deployments available for instant rollback.
- Use blue-green or canary deployments for production releases.
- Database rollbacks: every Alembic migration must have a working `downgrade()` function.

## 8. Infrastructure as Code (IaC)
- Use **Terraform** for all cloud provisioning. No manual console changes.
- Store Terraform state remotely (S3 + DynamoDB lock, or Terraform Cloud).
- Review `terraform plan` output in PRs before applying.

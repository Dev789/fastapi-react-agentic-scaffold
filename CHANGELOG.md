# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Instruction hierarchy in `agents.md` (§0) to resolve governance conflicts
- Mandatory co-consultation matrix in `agents.md` (§1.1) for cross-domain tasks
- Supported version matrix in `agents.md` (§1.2) pinning 12 core dependencies
- Disclaimer in `README.md` clarifying its illustrative-only status
- Skill registry in `.agents/skills/registry.yaml` for agent-agnostic discovery
- Compatibility guidance standardized on `agents.md`
- `push-code-repo` skill for safely publishing committed branches to the remote repository

### Fixed
- Contradictory commit rules between `agents.md` and `create-feature.md` workflow
- Context-blocking in `.agentignore` for required Alembic migration files
- Floating version language ("latest stable", "v6+") in persona rules with pinned dependencies
- Skill frontmatter descriptions so auto-discovery does not depend on body-only usage notes
- Stale prompt and workflow references after migrating reusable instructions into `.agents/skills/`
- Repo documentation so skills, workflows, and compatibility entrypoints are internally consistent
- Multi-agent persona architecture with 9 specialized AI personas (`react-rules`, `fastapi-rules`, `architect`, `security`, `devops`, `dba`, `qa`, `ba`, `pm`)
- 6 reusable skills for scaffolding, review, performance, and security tasks
- 12 development workflows covering feature creation, bug fixes, code review, refactoring, deployment, hotfix, performance audit, security audit, onboarding, third-party API integration, and ADR authoring
- Global AI directives with persona routing table, canonical folder structure, and git conventions
- Documentation standards with Python docstrings (Google style) and TypeScript JSDoc requirements
- Granular atomic commit rules and changelog maintenance policy
- Pull request description template with checklist
- API documentation standards (OpenAPI enrichment) for FastAPI endpoints
- `.agentignore` file to optimize AI assistant context windows
- `.gitignore` covering Python, Node.js, IDE, Docker, Terraform, and OS artifacts
- `run-tests` workflow for running the full test suite with coverage reporting
- `CHANGELOG.md` following Keep a Changelog format

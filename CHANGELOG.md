# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Multi-agent persona architecture with 9 specialized AI personas (`react-rules`, `fastapi-rules`, `architect`, `security`, `devops`, `dba`, `qa`, `ba`, `pm`)
- 3 reusable prompt templates (`create-endpoint`, `create-component`, `create-migration`)
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

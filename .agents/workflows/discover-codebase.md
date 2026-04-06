---
description: Deeply map and understand a new codebase from scratch. Identify tech stack, core architecture flows, key entry points, and deployment processes.
---

# Codebase Discovery Workflow

## Purpose
Build a "zero-to-one" mental model of any repository to get productive quickly.

## Steps

### 1. Initial Triage
- Read `README.md` for the project overview.
- Read `agents.md` for the AI persona routing and coding conventions.
- List the root directory to understand the project structure.

### 2. Run Codebase Scout Skill
Use the skill: `.agents/skills/codebase-scout/SKILL.md`
- Identify the Technology Stack (Pinned versions, build tools).
- Map core entry points (API Router, Frontend entry).
- Trace a core data flow (Frontend -> Redux -> API -> CRUD -> DB).
- Understand the Local Development and Deployment processes.

### 3. Verify the Environment
Follow the `setup-local.md` workflow:
- [ ] Docker infrastructure running.
- [ ] Backend dev server responding.
- [ ] Frontend dev server responding.
- [ ] Tests passing locally.

### 4. Continuous Discovery
As you work on specific features:
- Consult the `discovery-checklist` in the `codebase-scout` skill.
- Check for existing ADRs in `documents/adrs/`.
- Review previous PR summaries using the `pr-reviewer` skill.

## Checklist
- [ ] Technology stack identified.
- [ ] Core data flow traced.
- [ ] Main entry points mapped.
- [ ] Local dev environment verified.
- [ ] Deployment process understood.

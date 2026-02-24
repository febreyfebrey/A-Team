---
name: Agent Orchestration
description: Defines all 18 agents, their purposes, usage triggers, and the 6-phase workflow reference
---

# Agent Orchestration

## Complete Agent Registry

### Core Development Agents

| Agent | Location | Purpose | When to Use |
|-------|----------|---------|-------------|
| `tech-lead` | `agents/` | Orchestrate the 6-phase workflow, assign tasks, enforce quality gates | Every project -- the coordinator for all work |
| `product-manager` | `agents/planning/` | Gather requirements, write feature specs, maintain user manual | Phase 1 (Definition) and Phase 6 (Release) |
| `architect` | `agents/planning/` | System design, architecture decisions, technical feasibility review | Phase 1 (review) and Phase 2 (Design) |
| `planner` | `agents/planning/` | Create step-by-step implementation plans for complex features | Phase 3 before any coding begins |
| `frontend-engineer` | `agents/development/` | Build React components, pages, layouts with Next.js and Tailwind CSS | Phase 3 for all UI implementation work |
| `backend-engineer` | `agents/development/` | Build API routes, business logic, Prisma schemas, middleware | Phase 3 for all server-side implementation work |
| `devops` | `agents/operations/` | Dockerfile, CI/CD pipelines, Prisma migrations, deployment config | Phase 4 (Infrastructure) |

### Code Quality Agents

| Agent | Location | Purpose | When to Use |
|-------|----------|---------|-------------|
| `code-reviewer` | `agents/quality/` | General code review for quality, maintainability, readability | After every code change in Phase 3 |
| `typescript-reviewer` | `agents/quality/` | TypeScript type safety, patterns, generics, performance | After code-reviewer approves in Phase 3 |
| `security-reviewer` | `agents/quality/` | Auth, input validation, API security, secrets, XSS/CSRF | After code-reviewer approves in Phase 3 |
| `database-reviewer` | `agents/quality/` | Prisma schema, query optimization, indexes, migrations | When Prisma schema or queries change in Phase 3 |

### Specialized Agents

| Agent | Location | Purpose | When to Use |
|-------|----------|---------|-------------|
| `tdd-guide` | `agents/testing/` | Write failing tests first, enforce TDD cycle | Phase 3 before engineers write implementation code |
| `qa-engineer` | `agents/testing/` | Run full test suite, verify API contracts, coverage metrics | Phase 5 (QA) |
| `e2e-runner` | `agents/testing/` | Playwright E2E tests for critical user flows | Phase 5 after qa-engineer passes |
| `build-error-resolver` | `agents/development/` | Fix TypeScript and Next.js build failures | Phase 3 when `npm run build` or `npx tsc --noEmit` fails |
| `refactor-cleaner` | `agents/development/` | Remove dead code, consolidate duplicates, simplify structure | After Phase 3 reviews identify duplication or dead code |
| `doc-updater` | `agents/operations/` | Update codemaps and README from code changes | Phase 6 (Release) |
| `process-reviewer` | `agents/review/` | Retrospective on team collaboration quality | After Phase 6 completes |

## Proactive Agent Usage Rules

You must invoke agents automatically in these situations:

1. **code-reviewer** -- Invoke after every code change, with no exceptions. Do not merge without code review approval.
2. **security-reviewer** -- Invoke when any of these change: authentication logic, API route handlers, user input processing, environment variable usage, database queries with user-supplied values.
3. **typescript-reviewer** -- Invoke when any of these change: shared type definitions, generic utility types, complex type inference patterns, module declaration files.
4. **database-reviewer** -- Invoke when any of these change: `schema.prisma`, migration files, Prisma query patterns, database seeding scripts.
5. **build-error-resolver** -- Invoke immediately when `npm run build` or `npx tsc --noEmit` produces errors.
6. **tdd-guide** -- Invoke before frontend-engineer or backend-engineer begins implementation of any new feature.
7. **process-reviewer** -- Invoke after every Phase 6 completion. Do not skip retrospectives.

## Parallel Task Execution

You must run agents in parallel when their tasks have no dependencies:

- **frontend-engineer + backend-engineer** -- Run in parallel when the frontend task does not depend on a backend API that has not been built yet.
- **code-reviewer + typescript-reviewer + security-reviewer + database-reviewer** -- Run applicable reviewers in parallel after implementation completes (each reviewer reads the same diff independently).
- **qa-engineer + e2e-runner** -- Run in parallel during Phase 5 when unit/integration tests and E2E tests cover different aspects.
- **doc-updater + product-manager (user manual)** -- Run in parallel during Phase 6.

You must run agents sequentially when dependencies exist:

- **planner** must complete before **tdd-guide** starts.
- **tdd-guide** must complete before **frontend-engineer** or **backend-engineer** starts.
- All Phase 3 reviewers must approve before proceeding to Phase 4.

## 6-Phase Workflow Reference

```
Phase 1: Definition
  product-manager (write spec) -> architect (review spec)

Phase 2: Design
  architect (technical design) -> tech-lead (validate coverage)

Phase 3: Implementation
  planner -> tdd-guide -> [frontend-engineer | backend-engineer] ->
  [code-reviewer | typescript-reviewer | security-reviewer | database-reviewer] ->
  build-error-resolver (on build failure) -> refactor-cleaner (on duplication findings)

Phase 4: Infrastructure
  devops (deployment config, migrations, CI/CD)

Phase 5: QA
  [qa-engineer | e2e-runner] -> route failures back to Phase 3

Phase 6: Release
  [doc-updater | product-manager (user manual)] -> process-reviewer
```

## Related Rules by Agent Category

| Agent Category | Applicable Rules |
|----------------|-----------------|
| All agents | `agents.md`, `coding-style.md` |
| Core Development | `coding-style.md`, `patterns.md`, `api-response-format.md`, `testing.md`, `git-workflow.md` |
| Code Quality | `coding-style.md`, `security.md`, `performance.md`, `api-response-format.md` |
| Specialized | `testing.md`, `git-workflow.md`, `performance.md` |

## Violation Scenarios

- Merging code without invoking `code-reviewer` first -- Violation
- Skipping `security-reviewer` when authentication logic changes -- Violation
- Running `frontend-engineer` before `tdd-guide` writes failing tests -- Violation
- Skipping `process-reviewer` after Phase 6 -- Violation
- Running `backend-engineer` and `frontend-engineer` sequentially when their tasks are independent -- Violation (wastes time by not parallelizing)

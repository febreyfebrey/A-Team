---
name: Git Workflow
description: Enforces conventional commit format, branch naming, PR requirements, and feature implementation flow
---

# Git Workflow

## Commit Message Format

You must use this format for every commit message:

```
<type>: <description>
```

The `<type>` must be one of:

| Type | When to Use |
|------|-------------|
| `feat` | A new feature or user-facing capability |
| `fix` | A bug fix |
| `refactor` | Code restructuring that does not change behavior |
| `docs` | Documentation changes only |
| `test` | Adding or updating tests only |
| `chore` | Build config, dependencies, tooling changes |
| `perf` | Performance improvement with no behavior change |
| `ci` | CI/CD pipeline changes |

The `<description>` must:
- Start with a lowercase verb in imperative mood ("add", "fix", "remove", not "added", "fixes", "removing")
- Be under 72 characters
- Describe the change, not the file

Examples:

```
feat: add user registration API endpoint
fix: prevent duplicate email registration
refactor: extract validation logic into shared module
test: add integration tests for order creation
```

## Branch Naming

You must use this format for branch names:

```
<type>/<short-description>
```

| Type | When to Use |
|------|-------------|
| `feature/` | New feature development |
| `fix/` | Bug fix |
| `refactor/` | Code restructuring |

Examples:

```
feature/user-registration
fix/duplicate-email-check
refactor/auth-middleware
```

You must use kebab-case for the description portion. You must not include ticket numbers in branch names unless the Tech Lead specifies a ticketing system.

## Pull Request Requirements

Every PR must include:

1. **Title** -- Under 70 characters, follows the commit type format (`feat: ...`, `fix: ...`)
2. **Summary** -- One to three bullet points describing what changed and why
3. **Test Plan** -- A checklist of verification steps

```markdown
## Summary
- Add user registration endpoint with email validation
- Integrate Zod schema for request body parsing

## Test plan
- [ ] Unit tests pass for registration service
- [ ] Integration test verifies 201 response on valid input
- [ ] Integration test verifies 400 response on invalid email
- [ ] Manual test confirms duplicate email returns 409
```

You must not create a PR without a test plan section. You must analyze the full commit history of the branch (not only the latest commit) when writing the PR summary.

## Feature Implementation Flow

You must follow this sequence for every feature:

1. **Plan** -- Read the spec and architecture docs. Create a step-by-step implementation plan with file dependencies.
2. **TDD** -- Write failing tests that describe expected behavior before writing implementation code.
3. **Implement** -- Write the minimum code to make tests pass.
4. **Code Review** -- Submit changes for review. Address all CRITICAL and HIGH findings before proceeding.
5. **Commit** -- Create a conventional commit with a descriptive message.

You must not commit code that has not passed code review. You must not skip the TDD step for new features.

## Commit Hygiene

- You must make atomic commits: one logical change per commit.
- You must not combine unrelated changes in a single commit.
- You must not commit generated files (`node_modules/`, `.next/`, `prisma/client/`).
- You must not commit environment files (`.env`, `.env.local`).

## Violation Scenarios

- Commit message does not follow `<type>: <description>` format -- Violation
- Commit type is not in the accepted list (feat, fix, refactor, docs, test, chore, perf, ci) -- Violation
- PR created without a test plan section -- Violation
- Code committed without passing code review -- Violation
- Multiple unrelated changes combined in a single commit -- Violation
- Branch name does not follow `<type>/<short-description>` format -- Violation

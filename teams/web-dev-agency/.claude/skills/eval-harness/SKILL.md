---
name: Eval Harness
description: Define and execute evaluation-driven development with capability and regression evals
---

# Eval Harness

## Purpose

Use this framework to define expected behavior before implementation, measure quality systematically, and prevent regressions. Evals are the unit tests of AI-assisted development -- define them first, code second.

## Philosophy

Traditional development: Write code, then verify it works.
Eval-driven development (EDD): Define what "works" means first, then implement until evals pass.

Every feature must have evals defined before implementation begins. An implementation is complete only when all associated evals pass.

## Eval Types

### Capability Evals

Verify the system can perform a specific function correctly. Define input, expected output, and grader.

### Regression Evals

Verify previously working behavior has not broken. Run after every code change. All previously passing evals must continue to pass.

## Grader Types

| Grader       | When to Use                                      | Reliability |
|--------------|--------------------------------------------------|-------------|
| Code-based   | Output is deterministic and machine-verifiable    | Highest     |
| Model-based  | Output requires judgment (quality, tone, clarity) | Medium      |
| Human        | Security reviews, UX assessments, edge cases      | Highest     |

Prefer code-based graders. Reserve human graders for security-critical evaluations.

## Metrics

- **pass@k**: Run eval `k` times, pass if at least one trial succeeds. Use for non-deterministic outputs. Default `k=3`.
- **pass^k**: Run eval `k` times, pass only if all trials succeed. Use for deterministic functions. Default `k=3`.

## Workflow

### Step 1: Define

Create `.claude/evals/{feature-name}.md` before writing implementation code:

```markdown
# Eval: {Feature Name}
## Capability Evals
### EVAL-001: {Short description}
- Input: {exact input or scenario}
- Expected: {exact expected output or behavior}
- Grader: {code | model | human}
- Metric: {pass@k | pass^k}, k={value}
- Priority: {P0 | P1 | P2}
## Regression Evals
### REG-001: {Short description}
- Precondition: {what must already work}
- Input: {exact input}
- Expected: {exact expected output}
- Grader: {code | model | human}
```

### Step 2: Implement

Write the feature code. Run evals continuously during development.

### Step 3: Evaluate

Execute all evals. Record results in `.claude/evals/{feature-name}.log`.

### Step 4: Report

Generate a summary: total evals, passed, failed, specific failure reasons, and recommendations.

## Integration Commands

| Command          | Action                                             |
|------------------|----------------------------------------------------|
| `/eval define`   | Create a new eval definition for a feature         |
| `/eval check`    | Run all evals for the current feature              |
| `/eval report`   | Generate a summary report of the latest eval run   |
| `/eval list`     | List all eval definitions and their current status |

## Best Practices

1. Define before coding. Write eval definitions as the first step of every feature.
2. Run frequently. Execute evals after every meaningful change.
3. Prefer code graders. Deterministic checks are reproducible and fast.
4. Keep evals independent. Each eval must run in isolation.
5. Use human review for security. Model-based graders must not be the sole check for auth logic.
6. Track history. Append every eval run to the log file. Never overwrite previous results.
7. Fail fast. If a P0 eval fails, stop development and fix it before continuing.

## Example: Authentication Feature Evals

### Eval Definition (`.claude/evals/user-auth.md`)

```markdown
# Eval: User Authentication
## Capability Evals
### EVAL-001: Successful registration
- Input: POST /api/auth/register with { name: "Alice", email: "alice@test.com", password: "SecureP@ss1" }
- Expected: HTTP 201, body contains { success: true, data: { id, name, email } }, no password in response
- Grader: code
- Metric: pass^3, k=3
- Priority: P0

### EVAL-002: Duplicate email rejection
- Input: POST /api/auth/register with an email that already exists
- Expected: HTTP 409, body contains { success: false, error: { code: "USER_EMAIL_TAKEN" } }
- Grader: code
- Metric: pass^3, k=3
- Priority: P0

### EVAL-003: Login returns valid JWT
- Input: POST /api/auth/login with valid credentials
- Expected: HTTP 200, sets httpOnly cookie with JWT expiring in under 15 minutes
- Grader: code
- Metric: pass^3, k=3
- Priority: P0

## Regression Evals
### REG-001: Registration still works after login feature added
- Precondition: EVAL-001 has previously passed
- Input: Same as EVAL-001
- Expected: Same as EVAL-001
- Grader: code
```

### Eval Log (`.claude/evals/user-auth.log`)

```
[2025-01-15T10:00:00Z] RUN #1 - Branch: feature/auth-registration
EVAL-001: PASS (3/3)
EVAL-002: PASS (3/3)

[2025-01-15T14:00:00Z] RUN #2 - Branch: feature/auth-login
EVAL-001: PASS (3/3)
EVAL-002: PASS (3/3)
EVAL-003: FAIL (2/3) - Trial 2: Cookie missing httpOnly flag
REG-001: PASS (3/3)

[2025-01-15T15:30:00Z] RUN #3 - Branch: feature/auth-login (fix)
EVAL-003: PASS (3/3)
REG-001: PASS (3/3)
```

---
name: Testing Requirements
description: Enforces 80% coverage, TDD workflow, test types, and coverage scope for all code changes
---

# Testing Requirements

## Coverage Threshold

You must maintain a minimum of 80% code coverage across the project. You must not merge code that drops coverage below this threshold.

Run coverage checks with:

```bash
npx vitest --coverage
```

## TDD Workflow

You must follow the RED-GREEN-REFACTOR cycle for all new features and bug fixes:

### RED Phase

Write a failing test that describes the expected behavior before writing any implementation code:

```typescript
describe("createUser", () => {
  it("returns the created user with id, email, and name", async () => {
    const input = { email: "test@example.com", name: "Test User" };
    const result = await createUser(input);

    expect(result).toMatchObject({
      id: expect.any(String),
      email: "test@example.com",
      name: "Test User",
    });
  });

  it("throws DUPLICATE_EMAIL when email already exists", async () => {
    const input = { email: "existing@example.com", name: "Duplicate" };

    await expect(createUser(input)).rejects.toThrow("DUPLICATE_EMAIL");
  });
});
```

### GREEN Phase

Write the minimum implementation code to make the failing tests pass. You must not add functionality beyond what the tests require.

### REFACTOR Phase

Improve the code structure without changing behavior. Run the test suite after every refactor to confirm no regressions.

## Test Types

### Unit Tests (Jest/Vitest)

You must write unit tests for every exported function, utility, and service module:

- Test file location: co-located with the source file (`user.service.test.ts` next to `user.service.ts`)
- Test isolation: mock external dependencies (database, HTTP clients, file system)
- Naming: `describe("{FunctionName}")` with `it("does specific thing")` format

### Integration Tests (API Route Testing)

You must write integration tests for every API route handler:

```typescript
import { POST } from "@/app/api/users/route";
import { NextRequest } from "next/server";

describe("POST /api/users", () => {
  it("returns 201 with created user on valid input", async () => {
    const request = new NextRequest("http://localhost/api/users", {
      method: "POST",
      body: JSON.stringify({ email: "new@example.com", name: "New User" }),
    });

    const response = await POST(request);
    const body = await response.json();

    expect(response.status).toBe(201);
    expect(body.success).toBe(true);
    expect(body.data.email).toBe("new@example.com");
  });

  it("returns 400 on invalid email", async () => {
    const request = new NextRequest("http://localhost/api/users", {
      method: "POST",
      body: JSON.stringify({ email: "not-an-email", name: "Bad" }),
    });

    const response = await POST(request);
    const body = await response.json();

    expect(response.status).toBe(400);
    expect(body.success).toBe(false);
    expect(body.error.code).toBe("VALIDATION_ERROR");
  });
});
```

### E2E Tests (Playwright)

You must write Playwright E2E tests for every critical user flow:

- User registration and login
- Core feature workflows (CRUD operations visible in the UI)
- Error state handling (invalid form submissions, network failures)

```typescript
import { test, expect } from "@playwright/test";

test("user can register and see the dashboard", async ({ page }) => {
  await page.goto("/register");
  await page.fill('[name="email"]', "newuser@example.com");
  await page.fill('[name="password"]', "SecurePass123!");
  await page.click('button[type="submit"]');

  await expect(page).toHaveURL("/dashboard");
  await expect(page.locator("h1")).toContainText("Dashboard");
});
```

## Coverage Scope

Every test file must cover these three categories:

1. **Happy path** -- The expected successful behavior with valid input
2. **Edge cases** -- Boundary values, empty input, maximum length input, special characters
3. **Error handling** -- Invalid input, missing required fields, unauthorized access, resource not found

## Agent Responsibilities

| Agent | Testing Role |
|-------|-------------|
| `tdd-guide` | Write failing tests before implementation (RED phase). Enforce TDD cycle. |
| `qa-engineer` | Run the full test suite. Verify API contract compliance. Report coverage metrics. |
| `e2e-runner` | Write and execute Playwright tests for critical user flows. Report failures with screenshots. |

## Violation Scenarios

- Pushing code with no corresponding test files -- Violation
- Code coverage drops below 80% after merge -- Violation
- Writing implementation code before writing a failing test (skipping RED phase) -- Violation
- Test file covers only the happy path without edge cases or error handling -- Violation
- API route has no integration test -- Violation
- Critical user flow has no E2E test -- Violation

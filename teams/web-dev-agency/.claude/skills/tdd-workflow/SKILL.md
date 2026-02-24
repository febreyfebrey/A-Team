---
name: TDD Workflow
description: Execute the Red-Green-Refactor test-driven development cycle for TypeScript and Next.js
---

# TDD Workflow

## Purpose

Follow this workflow for every feature implementation. Write tests before writing production code. Never skip the cycle.

## Core Cycle

1. **RED** -- Write a test that describes the desired behavior. Run it. Confirm it fails for the expected reason.
2. **GREEN** -- Write the smallest amount of production code that makes the test pass. Do not add extra logic.
3. **REFACTOR** -- Clean up both test and production code. Run tests after every change to confirm they remain green.

Repeat until the feature is complete.

## Coverage Requirements

| Scope                    | Minimum Coverage |
|--------------------------|------------------|
| Overall project          | 80%              |
| Critical business logic  | 100%             |
| Utility functions        | 90%              |
| API route handlers       | 85%              |
| React components         | 75%              |

## Unit Test Patterns

Use Jest or Vitest with `describe`/`it` blocks. Each `it` block must test exactly one behavior.

Use table-driven tests for functions with many input/output pairs:

```typescript
describe("validateEmail", () => {
  it.each([
    ["user@example.com", true],
    ["invalid-email", false],
    ["user@.com", false],
    ["user@domain.co", true],
  ])("validates %s as %s", (email, expected) => {
    expect(validateEmail(email)).toBe(expected);
  });
});
```

## Integration Test Patterns

```typescript
import { POST } from "@/app/api/users/route";
import { prismaMock } from "@/test/mocks/prisma";

describe("POST /api/users", () => {
  it("creates a user and returns 201", async () => {
    prismaMock.user.create.mockResolvedValue(mockUser);
    const request = new Request("http://localhost/api/users", {
      method: "POST",
      body: JSON.stringify({ name: "Alice", email: "alice@test.com" }),
    });
    const response = await POST(request);
    expect(response.status).toBe(201);
  });
});
```

### Prisma Mock Setup

Create a singleton mock in `test/mocks/prisma.ts`:

```typescript
import { PrismaClient } from "@prisma/client";
import { mockDeep, DeepMockProxy } from "jest-mock-extended";

export const prismaMock: DeepMockProxy<PrismaClient> = mockDeep<PrismaClient>();
jest.mock("@/lib/prisma", () => ({ prisma: prismaMock }));
```

## E2E Test Patterns

Use Playwright for critical user flows. Limit E2E tests to high-value paths: signup, login, checkout, core feature. Use `data-testid` attributes for element selection.

## Mocking Strategy

| Dependency          | Mock Approach                                |
|---------------------|----------------------------------------------|
| Prisma Client       | `jest-mock-extended` deep mock               |
| External fetch      | `msw` (Mock Service Worker) or `jest.fn()`   |
| NextRequest         | Construct `new Request()` directly           |
| NextResponse        | Assert on returned `Response` object         |
| Environment vars    | Set in `beforeEach`, restore in `afterEach`  |

## Test Quality Checklist

- [ ] Each test has a single, clear assertion focus
- [ ] Tests do not depend on execution order
- [ ] No shared mutable state between tests
- [ ] Tests verify behavior, not implementation details
- [ ] Test names read as behavior specifications
- [ ] All tests complete in under 10 seconds (unit), 60 seconds (integration)

## Common Mistakes

| Mistake                          | Fix                                              |
|----------------------------------|--------------------------------------------------|
| Testing private implementation   | Test only the public API surface                 |
| Brittle CSS selectors in E2E     | Use `data-testid` attributes                    |
| Shared state between tests       | Use `beforeEach` to reset all state              |
| Mocking too much                 | Mock only external boundaries (DB, network)      |

## Example: TDD Cycle for User Registration

### Step 1: RED -- Write the failing test

```typescript
import { registerUser } from "@/features/auth/services/auth-service";
import { prismaMock } from "@/test/mocks/prisma";

describe("registerUser", () => {
  it("creates a user with hashed password and returns public profile", async () => {
    prismaMock.user.findUnique.mockResolvedValue(null);
    prismaMock.user.create.mockResolvedValue({
      id: "1", name: "Alice", email: "alice@test.com", password: "hashed", createdAt: new Date(),
    });
    const result = await registerUser({ name: "Alice", email: "alice@test.com", password: "P@ssword1" });
    expect(result).toEqual({ id: "1", name: "Alice", email: "alice@test.com" });
    expect(result).not.toHaveProperty("password");
  });

  it("throws ConflictError when email is already taken", async () => {
    prismaMock.user.findUnique.mockResolvedValue({ id: "1" } as any);
    await expect(
      registerUser({ name: "Alice", email: "taken@test.com", password: "P@ssword1" })
    ).rejects.toThrow("EMAIL_TAKEN");
  });
});
```

### Step 2: GREEN -- Write minimal implementation

```typescript
import { prisma } from "@/lib/prisma";
import { hashPassword } from "@/shared/utils/crypto";
import type { RegisterInput, UserPublic } from "../types";

export async function registerUser(input: RegisterInput): Promise<UserPublic> {
  const existing = await prisma.user.findUnique({ where: { email: input.email } });
  if (existing) throw new Error("EMAIL_TAKEN");

  const user = await prisma.user.create({
    data: { name: input.name, email: input.email, password: await hashPassword(input.password) },
  });
  return { id: user.id, name: user.name, email: user.email };
}
```

### Step 3: REFACTOR -- Improve while green

- Extract `toPublicUser()` mapper function.
- Replace generic `Error` with a custom `ConflictError` class.
- Add Zod validation for `RegisterInput`.
- Run tests after each change. All must remain green.

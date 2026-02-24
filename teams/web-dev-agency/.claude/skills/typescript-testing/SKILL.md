---
name: TypeScript Testing
description: Provides testing patterns with Jest/Vitest for TypeScript, React components, and Next.js API routes
---

# TypeScript Testing

## Test File Organization

Place test files co-located with source (`Button.test.tsx` next to `Button.tsx`) or in `__tests__/` folders. Choose one convention per project. Use `.tsx` extension when tests render JSX.

## Describe / It / Expect

```tsx
describe("formatCurrency", () => {
  it("formats positive amounts with two decimal places", () => {
    expect(formatCurrency(1234.5)).toBe("$1,234.50");
  });
  it("formats zero as $0.00", () => {
    expect(formatCurrency(0)).toBe("$0.00");
  });
});
```

## Table-Driven Tests

Use `it.each` to test multiple inputs without duplicating test bodies:

```tsx
it.each([
  ["Hello World", "hello-world"],
  ["  Extra   Spaces  ", "extra-spaces"],
  ["Special @#$ Chars!", "special-chars"],
])("converts '%s' to '%s'", (input, expected) => {
  expect(slugify(input)).toBe(expected);
});
```

## Mocking

**Jest:**

```tsx
jest.mock("@/lib/prisma", () => ({
  prisma: { user: { findUnique: jest.fn() } },
}));
const mockFind = prisma.user.findUnique as jest.MockedFunction<typeof prisma.user.findUnique>;
mockFind.mockResolvedValue({ id: "1", name: "Alice", email: "a@b.com" } as any);
```

**Vitest:**

```tsx
import { vi } from "vitest";
vi.mock("@/lib/prisma", () => ({
  prisma: { user: { findUnique: vi.fn() } },
}));
```

## React Testing Library

Query priority (most accessible first): `getByRole` > `getByLabelText` > `getByText` > `getByTestId`.

Always use `userEvent` over `fireEvent`:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

const user = userEvent.setup();
await user.click(screen.getByRole("button", { name: /submit/i }));
await user.type(screen.getByRole("textbox", { name: /email/i }), "user@test.com");
```

Use `waitFor` or `findBy*` for async assertions:

```tsx
expect(await screen.findByText("Success")).toBeInTheDocument();
```

## API Route Testing

```tsx
import { NextRequest } from "next/server";
import { GET } from "@/app/api/users/route";

function createMockRequest(method: string, opts?: {
  body?: Record<string, unknown>; searchParams?: Record<string, string>;
}): NextRequest {
  const url = new URL("http://localhost:3000/api/users");
  if (opts?.searchParams) {
    Object.entries(opts.searchParams).forEach(([k, v]) => url.searchParams.set(k, v));
  }
  return new NextRequest(url, {
    method,
    body: opts?.body ? JSON.stringify(opts.body) : undefined,
    headers: opts?.body ? { "Content-Type": "application/json" } : undefined,
  });
}

it("returns paginated users", async () => {
  const req = createMockRequest("GET", { searchParams: { page: "1" } });
  const res = await GET(req);
  expect(res.status).toBe(200);
});
```

## Async and Timer Patterns

```tsx
// Rejected promises
await expect(createUser({ email: "" })).rejects.toThrow("Email is required");

// Fake timers
beforeEach(() => { jest.useFakeTimers(); });
afterEach(() => { jest.useRealTimers(); });
it("debounces", () => {
  const cb = jest.fn();
  debounce(cb, 300)();
  jest.advanceTimersByTime(300);
  expect(cb).toHaveBeenCalledTimes(1);
});
```

## Coverage

Run `npx jest --coverage` or `npx vitest run --coverage`. Enforce minimum thresholds: statements 80%, branches 75%, functions 80%, lines 80%.

## Example: Testing a React Component

**Input**: Test a LoginForm that accepts `onSubmit`, shows errors, and disables during loading.

**Output:**

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { LoginForm } from "@/components/login-form";

describe("LoginForm", () => {
  const mockSubmit = jest.fn();
  const user = userEvent.setup();
  beforeEach(() => { mockSubmit.mockReset(); });

  it("submits email and password", async () => {
    mockSubmit.mockResolvedValue(undefined);
    render(<LoginForm onSubmit={mockSubmit} />);
    await user.type(screen.getByLabelText(/email/i), "user@test.com");
    await user.type(screen.getByLabelText(/password/i), "secret123");
    await user.click(screen.getByRole("button", { name: /sign in/i }));
    expect(mockSubmit).toHaveBeenCalledWith("user@test.com", "secret123");
  });

  it("displays error on failed submission", async () => {
    mockSubmit.mockRejectedValue(new Error("Invalid credentials"));
    render(<LoginForm onSubmit={mockSubmit} />);
    await user.type(screen.getByLabelText(/email/i), "user@test.com");
    await user.type(screen.getByLabelText(/password/i), "wrong");
    await user.click(screen.getByRole("button", { name: /sign in/i }));
    expect(await screen.findByRole("alert")).toHaveTextContent("Invalid credentials");
  });

  it("disables button while loading", async () => {
    mockSubmit.mockImplementation(() => new Promise(() => {}));
    render(<LoginForm onSubmit={mockSubmit} />);
    await user.type(screen.getByLabelText(/email/i), "user@test.com");
    await user.type(screen.getByLabelText(/password/i), "secret123");
    await user.click(screen.getByRole("button", { name: /sign in/i }));
    expect(screen.getByRole("button")).toBeDisabled();
    expect(screen.getByRole("button")).toHaveTextContent("Signing in...");
  });
});
```

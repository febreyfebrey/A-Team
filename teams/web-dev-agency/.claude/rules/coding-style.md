---
name: Coding Style
description: Enforces TypeScript and Next.js coding conventions for immutability, file organization, and naming
---

# Coding Style

## Immutability

You must create new objects instead of mutating existing ones. Use spread syntax, `map`, `filter`, and `reduce` for transformations.

```typescript
// Correct: create a new object
const updatedUser = { ...user, name: "New Name" };

// Violation: mutate the existing object
user.name = "New Name";
```

```typescript
// Correct: create a new array
const activeUsers = users.filter((u) => u.isActive);

// Violation: mutate the existing array
users.splice(inactiveIndex, 1);
```

You must not use `Array.prototype.push` on shared state. You must not use `delete` on object properties. You must not reassign function parameters.

## File Organization

You must keep files focused on a single responsibility. Target 200-400 lines per file. You must not exceed 800 lines in any single file.

When a file approaches 800 lines, split it:
- Extract helper functions into a `utils.ts` file in the same directory.
- Extract type definitions into a `.types.ts` file.
- Extract sub-components into their own files.

## Error Handling

You must wrap every async operation in a try-catch block with a user-friendly error message:

```typescript
export async function getUser(id: string): Promise<User> {
  try {
    const user = await prisma.user.findUniqueOrThrow({ where: { id } });
    return user;
  } catch (error) {
    if (error instanceof Prisma.PrismaClientKnownRequestError) {
      if (error.code === "P2025") {
        throw new AppError("USER_NOT_FOUND", `User ${id} does not exist`, 404);
      }
    }
    throw new AppError("INTERNAL_ERROR", "Failed to retrieve user", 500);
  }
}
```

You must use typed error responses. You must not expose raw database errors or stack traces to the client.

## Input Validation

You must validate all external input (request bodies, query parameters, path parameters, form data) using Zod schemas before processing:

```typescript
const QuerySchema = z.object({
  page: z.coerce.number().int().min(1).default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
  search: z.string().max(200).optional(),
});
```

You must not access `request.body` or `request.query` without passing through a Zod schema first.

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Variables and functions | camelCase | `getUserById`, `isActive` |
| React components | PascalCase | `UserProfile`, `DashboardLayout` |
| TypeScript types and interfaces | PascalCase | `UserResponse`, `CreateOrderInput` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_BASE_URL` |
| File names (components) | PascalCase or kebab-case | `UserProfile.tsx` or `user-profile.tsx` |
| File names (utilities) | kebab-case | `format-date.ts`, `use-auth.ts` |
| Database models (Prisma) | PascalCase singular | `User`, `OrderItem` |
| API route paths | kebab-case | `/api/user-profile`, `/api/order-items` |
| CSS class names | Tailwind utilities only | `className="flex items-center gap-2"` |

## Import Ordering

You must organize imports in this order, separated by blank lines:

1. External packages (`react`, `next`, `zod`, `prisma`)
2. Internal aliases (`@/lib/`, `@/components/`, `@/services/`)
3. Relative imports (`./`, `../`)
4. Type-only imports (`import type { ... }`)

```typescript
import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";

import { prisma } from "@/lib/prisma";
import { successResponse, errorResponse } from "@/lib/response";

import { validatePermission } from "../auth";

import type { UserResponse } from "@/types/api";
```

## Code Quality Checklist

You must verify every code change against this checklist before submission:

1. **Readability** -- Every function and variable has a descriptive, intention-revealing name.
2. **Function size** -- No function exceeds 50 lines (excluding type definitions and imports).
3. **Nesting depth** -- No code block is nested more than 4 levels deep. Extract helper functions to reduce nesting.
4. **No console.log in production** -- Remove all `console.log` statements. Use a structured logger for server-side logging.
5. **No commented-out code** -- Delete dead code. Use version control to retrieve old code.
6. **No `any` type** -- Use `unknown` with type narrowing when the type is genuinely unknown.
7. **No default exports** -- Use named exports, except for Next.js pages and layouts which require default exports.

## Violation Scenarios

- Mutable operations (`push`, `splice`, `delete`, direct property assignment) on shared state -- Violation
- File exceeds 800 lines -- Violation
- Async function without try-catch error handling -- Violation
- External input accessed without Zod validation -- Violation
- `console.log` left in production code -- Violation
- Function exceeds 50 lines -- Violation
- Code nesting exceeds 4 levels -- Violation
- Using `any` type -- Violation

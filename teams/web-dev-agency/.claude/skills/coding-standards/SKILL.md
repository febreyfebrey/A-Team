---
name: Coding Standards
description: Enforce universal TypeScript coding standards for readability, consistency, and maintainability
---

# Coding Standards

## Purpose

Apply these standards to every TypeScript file in the project. These rules are non-negotiable and must be followed without exception.

## Code Quality Principles

1. **Readability first** -- Write code that a new team member can understand in under 60 seconds per function.
2. **KISS (Keep It Simple, Stupid)** -- Choose the simplest solution that satisfies the requirement. Do not introduce abstractions until duplication occurs three or more times.
3. **DRY (Don't Repeat Yourself)** -- Extract shared logic into utility functions or hooks once the same pattern appears in three or more locations.
4. **YAGNI (You Aren't Gonna Need It)** -- Do not implement features or abstractions that have no current requirement. Delete dead code immediately.

## TypeScript Standards

- Enable `strict: true` in `tsconfig.json`. Never disable strict checks per file.
- Never use `any`. Use `unknown` when the type is genuinely uncertain, then narrow with type guards.
- Handle `null` and `undefined` explicitly. Use optional chaining (`?.`) and nullish coalescing (`??`) instead of truthy checks.
- Prefer union types over enums for simple value sets:
  ```typescript
  // Correct
  type Status = "active" | "inactive" | "suspended";

  // Avoid unless you need reverse mapping
  enum Status {
    Active = "active",
  }
  ```
- Use utility types to derive types from existing ones:
  ```typescript
  type UserUpdate = Partial<Pick<User, "name" | "email" | "avatar">>;
  type UserSummary = Omit<User, "password" | "createdAt">;
  type RolePermissions = Record<Role, Permission[]>;
  ```

## Naming Conventions

| Target                  | Convention        | Example                     |
|-------------------------|-------------------|-----------------------------|
| Variables, functions    | camelCase         | `getUserById`, `isActive`   |
| Components, types, interfaces | PascalCase | `UserProfile`, `ApiResponse`|
| Constants               | UPPER_SNAKE_CASE  | `MAX_RETRY_COUNT`, `API_URL`|
| Files (components)      | PascalCase        | `UserProfile.tsx`           |
| Files (utilities)       | kebab-case        | `date-utils.ts`             |
| Directories             | kebab-case        | `user-profile/`             |

## Function Rules

- Limit every function to 50 lines maximum. Extract helper functions when exceeding this limit.
- Each function must have a single responsibility. If a function name contains "and", split it.
- Use early return for error cases and guard clauses:
  ```typescript
  function getUser(id: string): User {
    if (!id) throw new Error("User ID is required");
    if (!isValidUuid(id)) throw new Error("Invalid user ID format");

    // Main logic here (not nested inside conditions)
    return userRepository.findById(id);
  }
  ```

## File Organization

- Place one component per file. The file name must match the exported component name.
- Use feature-based folder structure:
  ```
  src/
  ├── features/
  │   ├── auth/
  │   │   ├── components/
  │   │   ├── hooks/
  │   │   ├── services/
  │   │   └── types.ts
  │   └── dashboard/
  ├── shared/
  │   ├── components/
  │   ├── hooks/
  │   └── utils/
  └── lib/
  ```
- Use barrel exports (`index.ts`) at the feature level to control public API surface.

## Import Order

Organize imports in this exact order, separated by blank lines:

```typescript
// 1. External libraries
import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { z } from "zod";

// 2. Internal modules (absolute paths)
import { prisma } from "@/lib/prisma";
import { AuthService } from "@/features/auth/services";

// 3. Relative imports
import { UserCard } from "./UserCard";
import { useUserData } from "../hooks/useUserData";

// 4. Type imports
import type { User, UserRole } from "@/features/auth/types";
```

## Comments

- Write comments only when the logic is non-obvious. Do not restate what the code already expresses.
- Use JSDoc for all public API functions and exported utilities:
  ```typescript
  /**
   * Calculate the pro-rated subscription cost for a mid-cycle plan change.
   * Uses daily granularity: remaining days * (new plan daily rate - old plan daily rate).
   */
  export function calculateProratedCost(
    oldPlan: Plan,
    newPlan: Plan,
    remainingDays: number
  ): number {
    // ...
  }
  ```
- Never commit commented-out code. Use version control to retrieve old code.

## Example

### Well-Structured Module

```typescript
// src/features/users/services/user-service.ts
import { prisma } from "@/lib/prisma";
import { hashPassword } from "@/shared/utils/crypto";

import { UserCreateSchema } from "../schemas";

import type { UserCreate, UserPublic } from "../types";

const SALT_ROUNDS = 12;

/**
 * Create a new user account with hashed password.
 * Throws if email is already registered.
 */
export async function createUser(input: UserCreate): Promise<UserPublic> {
  const validated = UserCreateSchema.parse(input);

  const existing = await prisma.user.findUnique({
    where: { email: validated.email },
  });
  if (existing) {
    throw new ConflictError("EMAIL_TAKEN", "This email is already registered");
  }

  const hashedPassword = await hashPassword(validated.password, SALT_ROUNDS);

  const user = await prisma.user.create({
    data: {
      name: validated.name,
      email: validated.email,
      password: hashedPassword,
    },
  });

  return toPublicUser(user);
}
```

### Poorly-Structured Module (Do Not Imitate)

```typescript
// Bad: no types, uses any, mixed responsibilities, no validation
export async function doUserStuff(data: any) {
  // TODO: fix this later
  console.log(data);
  let user = await prisma.user.findFirst({ where: { email: data.email } });
  if (user == null) {
    var password = data.password; // Bad: var, no hashing
    user = await prisma.user.create({ data: { ...data, password } });
    // also send welcome email here
    await fetch("https://api.email.com/send", {
      method: "POST",
      body: JSON.stringify({ to: data.email, subject: "Welcome" }),
    });
  }
  return user; // Bad: returns internal model with password
}
```

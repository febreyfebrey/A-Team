---
name: Backend Patterns
description: Provides backend architecture patterns for Next.js API routes including service layer, error handling, and caching
---

# Backend Patterns

## API Route Handler Pattern

Every route handler must follow: **Validate** (Zod) -> **Authenticate** -> **Execute** (service) -> **Return** typed response. Never mix validation, auth, or data access in the handler itself.

## Service Layer

Services contain business logic, call repositories, and enforce rules. Services never import `NextRequest` or `NextResponse`.

```tsx
// services/user-service.ts
import { userRepository } from "@/repositories/user-repository";
import { ApiError } from "@/lib/errors";

export const userService = {
  async createUser(input: CreateUserInput) {
    const existing = await userRepository.findByEmail(input.email);
    if (existing) throw new ApiError(409, "EMAIL_EXISTS", "Email already exists");
    return userRepository.create(input);
  },
};
```

## Repository Pattern

Abstract Prisma calls behind a repository. This makes services testable without a database.

```tsx
// repositories/user-repository.ts
import { prisma } from "@/lib/prisma";
export const userRepository = {
  async findById(id: bigint) { return prisma.user.findUnique({ where: { id } }); },
  async findByEmail(email: string) { return prisma.user.findUnique({ where: { email } }); },
  async create(data: CreateUserInput) { return prisma.user.create({ data }); },
};
```

## Error Handling

### ApiError Class

```tsx
export class ApiError extends Error {
  constructor(public statusCode: number, public code: string, message: string) {
    super(message);
    this.name = "ApiError";
  }
}
```

### Centralized Error Handler

```tsx
import { NextRequest, NextResponse } from "next/server";
import { ZodError } from "zod";

type HandlerFn = (req: NextRequest, ctx?: any) => Promise<NextResponse>;

export function withErrorHandler(handler: HandlerFn): HandlerFn {
  return async (req, ctx) => {
    try { return await handler(req, ctx); }
    catch (error) {
      if (error instanceof ApiError)
        return NextResponse.json({ error: { code: error.code, message: error.message } }, { status: error.statusCode });
      if (error instanceof ZodError)
        return NextResponse.json({ error: { code: "VALIDATION_ERROR", details: error.flatten().fieldErrors } }, { status: 400 });
      return NextResponse.json({ error: { code: "INTERNAL_ERROR", message: "Unexpected error" } }, { status: 500 });
    }
  };
}
```

### Error Codes

`VALIDATION_ERROR` (400), `UNAUTHORIZED` (401), `FORBIDDEN` (403), `NOT_FOUND` (404), `CONFLICT` (409), `RATE_LIMITED` (429), `INTERNAL_ERROR` (500).

## Authentication and Authorization

```tsx
export async function authenticate(request: NextRequest): Promise<AuthUser> {
  const token = request.headers.get("Authorization")?.replace("Bearer ", "");
  if (!token) throw new ApiError(401, "UNAUTHORIZED", "Missing token");
  const user = await verifyToken(token);
  if (!user) throw new ApiError(401, "UNAUTHORIZED", "Invalid token");
  return user;
}
export function authorize(user: AuthUser, roles: string[]) {
  if (!roles.includes(user.role)) throw new ApiError(403, "FORBIDDEN", "Insufficient permissions");
}
```

## Rate Limiting

```tsx
const counts = new Map<string, { count: number; resetAt: number }>();
export function rateLimit(key: string, max: number, windowMs: number): void {
  const now = Date.now();
  const rec = counts.get(key);
  if (!rec || now > rec.resetAt) { counts.set(key, { count: 1, resetAt: now + windowMs }); return; }
  rec.count++;
  if (rec.count > max) throw new ApiError(429, "RATE_LIMITED", "Too many requests");
}
```

## Caching

```tsx
type Entry<T> = { data: T; expiresAt: number };
export class MemoryCache {
  private store = new Map<string, Entry<unknown>>();
  get<T>(key: string): T | null {
    const e = this.store.get(key) as Entry<T> | undefined;
    if (!e || Date.now() > e.expiresAt) { this.store.delete(key); return null; }
    return e.data;
  }
  set<T>(key: string, data: T, ttlMs: number) { this.store.set(key, { data, expiresAt: Date.now() + ttlMs }); }
  invalidate(key: string) { this.store.delete(key); }
}
```

Invalidate on every write. Use key prefixes (`users:`, `posts:`) for group invalidation. Set TTL as a safety net.

## Structured Logging

Include `requestId`, `userId`, `method`, `path` in every log entry:

```tsx
type LogLevel = "info" | "warn" | "error";
function log(level: LogLevel, data: Record<string, unknown>) {
  const entry = { level, timestamp: new Date().toISOString(), ...data };
  level === "error" ? console.error(JSON.stringify(entry)) : console.log(JSON.stringify(entry));
}
export const logger = {
  info: (d: Record<string, unknown>) => log("info", d),
  warn: (d: Record<string, unknown>) => log("warn", d),
  error: (d: Record<string, unknown>) => log("error", d),
};
```

## Example

**Input**: Create a POST /api/users endpoint with Zod validation, auth, service layer, and typed response.

**Output -- Schema:**

```tsx
import { z } from "zod";
export const createUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email().max(255),
  role: z.enum(["user", "admin"]).default("user"),
});
export type CreateUserInput = z.infer<typeof createUserSchema>;
```

**Output -- Route handler:**

```tsx
// app/api/users/route.ts
export const POST = withErrorHandler(async (request: NextRequest) => {
  const body = await request.json();
  const input = createUserSchema.parse(body);
  const authUser = await authenticate(request);
  authorize(authUser, ["admin"]);
  const user = await userService.createUser(input);
  logger.info({ action: "user_created", userId: String(user.id) });
  return NextResponse.json({ user }, { status: 201 });
});
```

**Output -- Success (201):**

```json
{ "user": { "id": 42, "name": "Alice", "email": "alice@example.com", "role": "user" } }
```

**Output -- Validation error (400):**

```json
{ "error": { "code": "VALIDATION_ERROR", "details": { "email": ["Enter a valid email"] } } }
```

**Output -- Conflict (409):**

```json
{ "error": { "code": "EMAIL_EXISTS", "message": "Email already exists" } }
```

---
name: Security Guidelines
description: Enforces pre-commit security checks, secret management, and Next.js-specific security practices
---

# Security Guidelines

## Pre-Commit Security Checklist

You must verify every item on this checklist before committing code:

1. **No hardcoded secrets** -- No API keys, tokens, passwords, or connection strings in source files. Use environment variables exclusively.
2. **Input validated** -- Every external input (request bodies, query params, path params, form data) is validated with a Zod schema before processing.
3. **SQL injection prevented** -- All database queries use Prisma's parameterized queries. No raw SQL with string concatenation.
4. **XSS prevented** -- No `dangerouslySetInnerHTML` unless the content is sanitized with a trusted library (e.g., DOMPurify). All user-generated content is escaped by React's default rendering.
5. **CSRF protection** -- State-mutating API endpoints (POST, PUT, DELETE) verify the request origin or use CSRF tokens.
6. **Auth and authz verified** -- Every protected endpoint checks authentication (valid token) and authorization (user has permission for the action).
7. **Rate limiting** -- Public-facing endpoints (login, registration, password reset) have rate limiting configured.
8. **Error messages safe** -- Error responses do not expose internal implementation details, stack traces, database schema, or file paths.

## Secret Management

You must store all secrets in environment variables. You must not commit secrets to version control under any circumstance.

Required `.gitignore` entries:

```
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

Environment variable naming:
- Prefix client-accessible variables with `NEXT_PUBLIC_` (non-sensitive values only)
- Store sensitive values without the `NEXT_PUBLIC_` prefix (server-only access)

```typescript
// Correct: server-only secret
const dbUrl = process.env.DATABASE_URL;

// Correct: non-sensitive client value
const apiBase = process.env.NEXT_PUBLIC_API_BASE_URL;

// Violation: sensitive value with NEXT_PUBLIC_ prefix
const secret = process.env.NEXT_PUBLIC_JWT_SECRET;
```

## Next.js-Specific Security

### Server vs Client Components

You must not include sensitive data in Client Components (`"use client"`). Sensitive operations (database queries, secret access, token verification) must execute in Server Components or API Route Handlers.

### Server-Only Modules

You must mark modules containing sensitive logic with the `server-only` package to prevent accidental client-side bundling:

```typescript
// src/lib/auth.ts
import "server-only";

export function verifyToken(token: string) {
  // This code will throw a build error if imported from a Client Component
  const secret = process.env.JWT_SECRET;
  // ... verification logic
}
```

### API Route Authentication

You must verify authentication in every protected API route. Use the middleware pattern (see `patterns.md`) for global auth checks, and add per-route authorization checks:

```typescript
export async function DELETE(request: NextRequest, { params }: { params: { id: string } }) {
  const userId = request.headers.get("x-user-id");
  if (!userId) {
    return errorResponse("UNAUTHORIZED", "Authentication required", 401);
  }

  const resource = await prisma.resource.findUnique({ where: { id: params.id } });
  if (!resource) {
    return errorResponse("NOT_FOUND", "Resource not found", 404);
  }

  if (resource.ownerId !== userId) {
    return errorResponse("FORBIDDEN", "You do not have permission to delete this resource", 403);
  }

  await prisma.resource.delete({ where: { id: params.id } });
  return successResponse({ deleted: true });
}
```

## Response Protocol on Security Issues

When you discover a security vulnerability during implementation or review:

1. **STOP** -- Do not continue with the current task.
2. **Flag** -- Report the vulnerability to the Tech Lead with the exact file path, line number, and description of the risk.
3. **Invoke** -- The Tech Lead must assign the `security-reviewer` agent to audit the finding.
4. **Fix CRITICAL first** -- Do not proceed with any other work until CRITICAL security findings are resolved.
5. **Re-verify** -- After the fix, run the security review again to confirm the vulnerability is resolved.

## Violation Scenarios

- Hardcoded API key, token, or password in a source file -- Violation
- Raw SQL query built with string concatenation (`\`SELECT * FROM users WHERE id = '${id}'\``) -- Violation
- `NEXT_PUBLIC_` prefix on a sensitive environment variable (JWT secret, database URL, API secret key) -- Violation
- Protected API endpoint with no authentication check -- Violation
- Error response that exposes a stack trace or database schema details -- Violation
- Using `dangerouslySetInnerHTML` without sanitization -- Violation
- `.env` files not listed in `.gitignore` -- Violation

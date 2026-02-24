---
name: Security Review
description: Execute a structured security review checklist for Next.js web applications
---

# Security Review

## Purpose

Run this checklist before every deployment and during every code review. Every item must be verified -- no exceptions.

## Secrets Management

- Store all secrets in environment variables. Never hardcode secrets in source code.
- Add `.env.local` and `.env*.local` to `.gitignore`. Verify with `git ls-files | grep -i env` -- the result must be empty.
- Rotate any secret that has ever been committed to git history, even if the commit was reverted.
- Use distinct secrets for development, staging, and production environments.
- Access secrets only through `process.env` on the server side. Never expose secrets to client-side bundles (only `NEXT_PUBLIC_` prefixed vars reach the client).

## Input Validation

- Validate every user input at the API route entry point using a Zod schema. Reject requests that fail validation with HTTP 422.
- Validate file uploads against an allowlist of MIME types and extensions. Enforce a maximum file size (default: 5MB).
- Sanitize string inputs: trim whitespace, enforce length limits, reject null bytes.

```typescript
const CreatePostSchema = z.object({
  title: z.string().trim().min(1).max(200),
  body: z.string().trim().min(1).max(10000),
  tags: z.array(z.string().max(50)).max(10),
});
```

## SQL Injection Prevention

- Use Prisma's parameterized queries exclusively. Never construct queries with string concatenation or template literals.
- Prohibit `$queryRaw` with interpolated variables. Use `$queryRaw` only with `Prisma.sql` tagged template:

```typescript
// SECURE -- parameterized
const users = await prisma.$queryRaw(
  Prisma.sql`SELECT * FROM User WHERE email = ${email}`
);

// VULNERABLE -- string interpolation (never do this)
const users = await prisma.$queryRaw(`SELECT * FROM User WHERE email = '${email}'`);
```

## XSS Prevention

- Rely on React's built-in escaping for all rendered content. Never use `dangerouslySetInnerHTML` without sanitization.
- Sanitize user-provided HTML with DOMPurify before rendering:
  ```typescript
  import DOMPurify from "isomorphic-dompurify";
  const clean = DOMPurify.sanitize(userHtml, { ALLOWED_TAGS: ["b", "i", "a", "p"] });
  ```
- Set Content Security Policy headers in `next.config.js` or middleware. At minimum, set `default-src 'self'` and `script-src 'self'`.

## CSRF Protection

- Set `SameSite=Lax` or `SameSite=Strict` on all authentication cookies.
- Require a CSRF token for every state-changing operation (POST, PUT, PATCH, DELETE).
- Validate the `Origin` header on API routes that mutate data.

## Authentication

- Use NextAuth.js or a proven authentication library. Do not implement custom session management.
- Store session tokens in `httpOnly`, `Secure`, `SameSite=Lax` cookies. Never store tokens in localStorage.
- Set JWT expiration to 15 minutes maximum for access tokens. Use refresh tokens for extended sessions.
- Enforce password complexity: minimum 8 characters, at least one uppercase letter, one number, one special character.
- Implement account lockout after 5 failed login attempts within 15 minutes.

## Authorization

- Check user roles and permissions on every API route handler. Never rely on client-side route guards alone.
- Verify resource ownership: confirm the authenticated user owns or has access to the requested resource.

```typescript
export async function GET(req: NextRequest, { params }: { params: { id: string } }) {
  const session = await getServerSession(authOptions);
  if (!session) return NextResponse.json({ error: "UNAUTHORIZED" }, { status: 401 });

  const post = await prisma.post.findUnique({ where: { id: params.id } });
  if (!post) return NextResponse.json({ error: "NOT_FOUND" }, { status: 404 });
  if (post.authorId !== session.user.id && session.user.role !== "ADMIN") {
    return NextResponse.json({ error: "FORBIDDEN" }, { status: 403 });
  }

  return NextResponse.json({ success: true, data: post });
}
```

## Rate Limiting

- Apply rate limits to every public endpoint. Defaults: 100 requests per minute per IP, 1000 requests per minute per authenticated user.
- Apply stricter limits to authentication endpoints: 10 login attempts per minute per IP.
- Return HTTP 429 with a `Retry-After` header when the limit is exceeded.

## Sensitive Data Handling

- Never log passwords, tokens, API keys, or credit card numbers.
- Mask PII (email, phone) in application logs: `a***@example.com`.
- Return generic error messages to clients. Log detailed error information server-side only.
- Never include stack traces in production API responses.

## Pre-Deployment Checklist

| #  | Check                                              | Status |
|----|-----------------------------------------------------|--------|
| 1  | All secrets are in env vars, none in source code    | [ ]    |
| 2  | `.env.local` is in `.gitignore`                     | [ ]    |
| 3  | Every API route validates input with Zod            | [ ]    |
| 4  | No `$queryRaw` with string interpolation            | [ ]    |
| 5  | No `dangerouslySetInnerHTML` without DOMPurify      | [ ]    |
| 6  | CSP headers are configured                          | [ ]    |
| 7  | Auth cookies are httpOnly + Secure + SameSite       | [ ]    |
| 8  | Every API route checks authentication               | [ ]    |
| 9  | Every API route checks authorization                | [ ]    |
| 10 | Rate limiting is active on all endpoints             | [ ]    |
| 11 | No passwords or tokens appear in logs                | [ ]    |
| 12 | Production error responses contain no stack traces   | [ ]    |

## Example

### Secure API Route Handler

```typescript
import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";
import { z } from "zod";
import { prisma } from "@/lib/prisma";
import { authOptions } from "@/lib/auth";
import { rateLimit } from "@/lib/rate-limit";

const UpdateProfileSchema = z.object({
  name: z.string().trim().min(1).max(100),
  bio: z.string().trim().max(500).optional(),
});

export async function PATCH(req: NextRequest) {
  const rateLimitResult = await rateLimit(req, { max: 20, windowMs: 60_000 });
  if (!rateLimitResult.ok) {
    return NextResponse.json(
      { success: false, error: { code: "RATE_LIMITED", message: "Too many requests" } },
      { status: 429, headers: { "Retry-After": String(rateLimitResult.retryAfter) } }
    );
  }

  const session = await getServerSession(authOptions);
  if (!session) {
    return NextResponse.json(
      { success: false, error: { code: "UNAUTHORIZED", message: "Authentication required" } },
      { status: 401 }
    );
  }

  const body = await req.json();
  const parsed = UpdateProfileSchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json(
      { success: false, error: { code: "VALIDATION_ERROR", message: parsed.error.message } },
      { status: 422 }
    );
  }

  const user = await prisma.user.update({
    where: { id: session.user.id },
    data: parsed.data,
    select: { id: true, name: true, bio: true, email: true },
  });

  return NextResponse.json({ success: true, data: user });
}
```

### Insecure API Route Handler (Do Not Imitate)

```typescript
// BAD: no auth, no validation, no rate limit, logs sensitive data
export async function PATCH(req: NextRequest) {
  const body = await req.json();
  console.log("Update request:", JSON.stringify(body)); // Logs all input including sensitive data
  const user = await prisma.user.update({
    where: { id: body.userId }, // No auth check -- any user can update any profile
    data: body, // No validation -- accepts arbitrary fields
  });
  return NextResponse.json(user); // Returns full user object including password hash
}
```

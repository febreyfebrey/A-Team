---
name: API Design
description: Define RESTful API design standards for Next.js API routes with typed responses and validation
---

# API Design

## Purpose

Follow these standards for every API route in the project. Consistent API design reduces integration friction and eliminates ambiguity for frontend consumers.

## URL Structure

- Collection: `/api/{resource}` (GET list, POST create)
- Single item: `/api/{resource}/[id]` (GET, PUT, PATCH, DELETE)
- Use plural nouns: `/api/users`, not `/api/user`
- Use kebab-case for multi-word resources: `/api/blog-posts`
- Limit nesting to two levels: `/api/users/[id]/posts` is acceptable; deeper nesting must be flattened with query params

## HTTP Methods

| Method  | Purpose        | Body | Idempotent | Success Code |
|---------|----------------|------|------------|--------------|
| GET     | Read           | No   | Yes        | 200          |
| POST    | Create         | Yes  | No         | 201          |
| PUT     | Full replace   | Yes  | Yes        | 200          |
| PATCH   | Partial update | Yes  | Yes        | 200          |
| DELETE  | Remove         | No   | Yes        | 204          |

## Response Format

```typescript
// Success: { success: true, data: T, meta?: { total, page, limit, hasNext } }
// Error:   { success: false, error: { code: string, message: string } }

export interface ApiSuccessResponse<T> { success: true; data: T; meta?: PaginationMeta; }
export interface ApiErrorResponse { success: false; error: { code: string; message: string; }; }
export type ApiResponse<T> = ApiSuccessResponse<T> | ApiErrorResponse;
export interface PaginationMeta { total: number; page: number; limit: number; hasNext: boolean; }
```

## Status Codes

| Code | When to Use                                    |
|------|------------------------------------------------|
| 200  | Successful GET, PUT, PATCH                     |
| 201  | Successful POST that creates a resource        |
| 204  | Successful DELETE                              |
| 400  | Malformed JSON, missing required fields        |
| 401  | No valid authentication credentials            |
| 403  | Authenticated but lacks permission             |
| 404  | Resource does not exist                        |
| 409  | Duplicate unique constraint (e.g., email)      |
| 422  | Valid JSON but fails business validation       |
| 429  | Rate limit exceeded                            |
| 500  | Unhandled server error                         |

## Request Validation

Validate every request at the route handler entry using Zod:

```typescript
const CreateUserSchema = z.object({
  name: z.string().trim().min(1).max(100),
  email: z.string().email().max(255),
  role: z.enum(["user", "admin"]).default("user"),
});
const UpdateUserSchema = CreateUserSchema.partial().omit({ role: true });
```

## Pagination

- **Page-based** (datasets under 10,000): `GET /api/users?page=2&limit=20`
- **Cursor-based** (large datasets): `GET /api/posts?cursor=abc123&limit=20`

## Error Codes

Prefix with resource name: `USER_NOT_FOUND`, `USER_EMAIL_TAKEN`, `AUTH_TOKEN_EXPIRED`, `VALIDATION_ERROR`.

## Versioning

Do not version until a breaking change is required. Use URL prefix: `/api/v1/users`. Maintain the previous version for at least 90 days.

## Example: Complete CRUD API Route

```typescript
// src/app/api/posts/route.ts
import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";
import { z } from "zod";
import { prisma } from "@/lib/prisma";
import { authOptions } from "@/lib/auth";

const CreatePostSchema = z.object({
  title: z.string().trim().min(1).max(200),
  body: z.string().trim().min(1).max(50000),
  published: z.boolean().default(false),
});
const QuerySchema = z.object({
  page: z.coerce.number().int().min(1).default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
});

export async function GET(req: NextRequest) {
  const params = Object.fromEntries(req.nextUrl.searchParams);
  const query = QuerySchema.parse(params);
  const skip = (query.page - 1) * query.limit;

  const [posts, total] = await Promise.all([
    prisma.post.findMany({
      where: { published: true }, skip, take: query.limit,
      orderBy: { createdAt: "desc" },
      select: { id: true, title: true, published: true, createdAt: true },
    }),
    prisma.post.count({ where: { published: true } }),
  ]);

  return NextResponse.json({
    success: true, data: posts,
    meta: { total, page: query.page, limit: query.limit, hasNext: skip + query.limit < total },
  });
}

export async function POST(req: NextRequest) {
  const session = await getServerSession(authOptions);
  if (!session) {
    return NextResponse.json(
      { success: false, error: { code: "UNAUTHORIZED", message: "Authentication required" } },
      { status: 401 }
    );
  }
  const body = await req.json();
  const parsed = CreatePostSchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json(
      { success: false, error: { code: "VALIDATION_ERROR", message: parsed.error.message } },
      { status: 422 }
    );
  }
  const post = await prisma.post.create({
    data: { ...parsed.data, authorId: session.user.id },
    select: { id: true, title: true, published: true, createdAt: true },
  });
  return NextResponse.json({ success: true, data: post }, { status: 201 });
}
```

```typescript
// src/app/api/posts/[id]/route.ts
import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";
import { z } from "zod";
import { prisma } from "@/lib/prisma";
import { authOptions } from "@/lib/auth";

const UpdatePostSchema = z.object({
  title: z.string().trim().min(1).max(200).optional(),
  body: z.string().trim().min(1).max(50000).optional(),
  published: z.boolean().optional(),
});

export async function GET(_req: NextRequest, { params }: { params: { id: string } }) {
  const post = await prisma.post.findUnique({ where: { id: params.id } });
  if (!post) {
    return NextResponse.json(
      { success: false, error: { code: "POST_NOT_FOUND", message: "Post not found" } },
      { status: 404 }
    );
  }
  return NextResponse.json({ success: true, data: post });
}

export async function PATCH(req: NextRequest, { params }: { params: { id: string } }) {
  const session = await getServerSession(authOptions);
  if (!session) return NextResponse.json({ success: false, error: { code: "UNAUTHORIZED", message: "Authentication required" } }, { status: 401 });

  const post = await prisma.post.findUnique({ where: { id: params.id } });
  if (!post) return NextResponse.json({ success: false, error: { code: "POST_NOT_FOUND", message: "Post not found" } }, { status: 404 });
  if (post.authorId !== session.user.id) return NextResponse.json({ success: false, error: { code: "FORBIDDEN", message: "You do not own this post" } }, { status: 403 });

  const body = await req.json();
  const parsed = UpdatePostSchema.safeParse(body);
  if (!parsed.success) return NextResponse.json({ success: false, error: { code: "VALIDATION_ERROR", message: parsed.error.message } }, { status: 422 });

  const updated = await prisma.post.update({ where: { id: params.id }, data: parsed.data });
  return NextResponse.json({ success: true, data: updated });
}

export async function DELETE(_req: NextRequest, { params }: { params: { id: string } }) {
  const session = await getServerSession(authOptions);
  if (!session) return NextResponse.json({ success: false, error: { code: "UNAUTHORIZED", message: "Authentication required" } }, { status: 401 });

  const post = await prisma.post.findUnique({ where: { id: params.id } });
  if (!post) return NextResponse.json({ success: false, error: { code: "POST_NOT_FOUND", message: "Post not found" } }, { status: 404 });
  if (post.authorId !== session.user.id) return NextResponse.json({ success: false, error: { code: "FORBIDDEN", message: "You do not own this post" } }, { status: 403 });

  await prisma.post.delete({ where: { id: params.id } });
  return new NextResponse(null, { status: 204 });
}
```

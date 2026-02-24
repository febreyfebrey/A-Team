---
name: Prisma Patterns
description: Provides Prisma ORM patterns for MySQL including schema design, queries, transactions, and type safety
---

# Prisma Patterns

## Client Singleton

Prevent multiple instances in development. Import from `@/lib/prisma` everywhere.

```tsx
// lib/prisma.ts
import { PrismaClient } from "@prisma/client";
const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };
export const prisma = globalForPrisma.prisma ?? new PrismaClient();
if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;
```

## Schema Design

Use `@map` for snake_case columns, `@@map` for table names, `@@index` for query-critical columns.

```prisma
model User {
  id        BigInt   @id @default(autoincrement()) @db.UnsignedBigInt
  name      String   @db.VarChar(100)
  email     String   @unique @db.VarChar(255)
  createdAt DateTime @default(now()) @map("created_at") @db.DateTime(3)
  posts     Post[]
  @@map("users")
}

model Post {
  id       BigInt @id @default(autoincrement()) @db.UnsignedBigInt
  title    String @db.VarChar(255)
  content  String @db.Text
  authorId BigInt @map("author_id") @db.UnsignedBigInt
  author   User   @relation(fields: [authorId], references: [id])
  tags     Tag[]
  @@index([authorId])
  @@map("posts")
}

model Tag {
  id    BigInt @id @default(autoincrement()) @db.UnsignedBigInt
  name  String @unique @db.VarChar(50)
  posts Post[]
  @@map("tags")
}
```

## CRUD Operations

```tsx
// Read
const posts = await prisma.post.findMany({
  where: { author: { role: "admin" } }, orderBy: { createdAt: "desc" }, take: 20,
});
const user = await prisma.user.findUnique({ where: { email: "user@example.com" } });

// Create with nested relation
const user = await prisma.user.create({
  data: { name: "Alice", email: "alice@example.com", posts: { create: { title: "First" } } },
  include: { posts: true },
});

// Update
await prisma.post.update({ where: { id: postId }, data: { published: true } });

// Upsert
await prisma.user.upsert({
  where: { email: "alice@example.com" },
  update: { name: "Alice Updated" },
  create: { name: "Alice", email: "alice@example.com" },
});
```

## include vs select

Use `include` for full related records. Use `select` to pick specific fields and reduce payload.

```tsx
const user = await prisma.user.findUnique({
  where: { id: userId },
  select: { name: true, posts: { select: { id: true, title: true }, take: 5 } },
});
```

## Transactions

```tsx
// Batch (independent operations)
const [order, payment] = await prisma.$transaction([
  prisma.order.create({ data: { userId, total, status: "pending" } }),
  prisma.payment.create({ data: { userId, amount: total, method: "card" } }),
]);

// Interactive (dependent operations)
await prisma.$transaction(async (tx) => {
  const account = await tx.account.findUniqueOrThrow({ where: { userId } });
  if (account.balance < amount) throw new Error("Insufficient balance");
  await tx.account.update({ where: { userId }, data: { balance: { decrement: amount } } });
});
```

## Raw Queries

Use only when Prisma Query API cannot express the operation:

```tsx
const result = await prisma.$queryRaw<{ month: string; total: number }[]>`
  SELECT DATE_FORMAT(created_at, '%Y-%m') as month, SUM(total) as total
  FROM orders WHERE user_id = ${userId} GROUP BY month ORDER BY month DESC
`;
```

## Migrations

| Command | When to Use |
|---|---|
| `npx prisma migrate dev` | Development: after schema changes |
| `npx prisma migrate deploy` | Production deployment |
| `npx prisma db push` | Prototyping only, never production |
| `npx prisma generate` | After any schema change |

## Type Safety

Import generated types directly. Use `Prisma.ModelGetPayload` for custom select/include shapes:

```tsx
import type { User } from "@prisma/client";
import { Prisma } from "@prisma/client";

type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: { select: { id: true; title: true } } };
}>;
```

## N+1 Prevention

Never query inside a loop. Use `include` or `select` to batch-load relations:

```tsx
// WRONG: N+1
const users = await prisma.user.findMany();
for (const u of users) { await prisma.post.findMany({ where: { authorId: u.id } }); }

// CORRECT: single query
const users = await prisma.user.findMany({ include: { posts: true } });
```

## Example

**Input**: Fetch active authors with their 5 most recent post titles.

**Output -- Schema:**

```prisma
model User {
  id    BigInt @id @default(autoincrement()) @db.UnsignedBigInt
  name  String @db.VarChar(100)
  email String @unique @db.VarChar(255)
  posts Post[]
  @@map("users")
}
model Post {
  id       BigInt @id @default(autoincrement()) @db.UnsignedBigInt
  title    String @db.VarChar(255)
  authorId BigInt @map("author_id") @db.UnsignedBigInt
  author   User   @relation(fields: [authorId], references: [id])
  @@index([authorId])
  @@map("posts")
}
```

**Output -- Query:**

```tsx
type UserWithRecentPosts = Prisma.UserGetPayload<{
  select: { id: true; name: true; posts: { select: { id: true; title: true } } };
}>;

const authors: UserWithRecentPosts[] = await prisma.user.findMany({
  where: { posts: { some: {} } },
  select: {
    id: true, name: true,
    posts: { select: { id: true, title: true }, orderBy: { id: "desc" }, take: 5 },
  },
});
```

**Output -- Response:**

```json
[{ "id": 1, "name": "Alice", "posts": [{ "id": 42, "title": "Getting Started" }] }]
```

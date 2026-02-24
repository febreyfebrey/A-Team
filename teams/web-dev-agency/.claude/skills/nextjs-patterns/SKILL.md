---
name: Next.js Patterns
description: Provides App Router patterns, data fetching strategies, and component model guidance for Next.js
---

# Next.js Patterns

## App Router Structure

```
app/
├── layout.tsx          ← Root layout (required, wraps entire app)
├── page.tsx            ← Home page (/)
├── loading.tsx         ← Loading UI (automatic Suspense boundary)
├── error.tsx           ← Error UI (must be "use client")
├── not-found.tsx       ← 404 UI
├── (marketing)/        ← Route group (no URL segment)
│   └── about/page.tsx
└── dashboard/
    ├── layout.tsx
    └── page.tsx
```

Use `layout.tsx` for shared UI that persists across child routes. Use `page.tsx` for unique route content. Place `loading.tsx` for streaming skeletons and `error.tsx` (Client Component) for error recovery.

## Server Components vs Client Components

Use **Server Components** (the default) for: data fetching, backend resource access, keeping secrets server-side, reducing client JS bundle.

Use **Client Components** (`"use client"`) for: browser APIs, event handlers, React hooks (useState, useEffect), third-party browser-dependent libraries.

Pass Server Components as `children` to Client Components. Never import a Server Component inside a Client Component file.

## Data Fetching

Fetch data directly in Server Components with `async/await`:

```tsx
const data = await fetch("https://api.example.com/posts", {
  next: { revalidate: 60 },  // Time-based: re-fetch after 60s
});
const data = await fetch("https://api.example.com/posts", {
  cache: "no-store",          // Dynamic: no caching
});
```

For on-demand revalidation, call `revalidateTag("tag")` or `revalidatePath("/path")` from Server Actions or Route Handlers.

## API Route Handlers

Place in `app/api/` using `route.ts`. Export named functions matching HTTP methods.

```tsx
// app/api/users/route.ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const page = request.nextUrl.searchParams.get("page") ?? "1";
  return NextResponse.json({ users: [], page });
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  return NextResponse.json({ user: body }, { status: 201 });
}
```

Dynamic route params use `{ params }` with `Promise`:

```tsx
// app/api/users/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  return NextResponse.json({ id });
}
```

## Dynamic and Advanced Routes

- `[id]` -- single dynamic segment
- `[...slug]` -- catch-all (one or more segments)
- `[[...slug]]` -- optional catch-all (zero or more)
- `@slot` -- parallel routes (multiple pages in one layout)
- `(.)`, `(..)` -- intercepting routes (modal overlays)

## Environment Variables

- `NEXT_PUBLIC_` prefix exposes to client-side code
- Without prefix, server-only access
- Define in `.env.local` (gitignored) for development

## Built-in Components

Use `next/image` for all images (auto-optimization, lazy loading). Set `priority` on above-the-fold images. Use `next/link` for internal navigation (auto-prefetch). Use `next/font` for zero-layout-shift font loading.

```tsx
import Image from "next/image";
import Link from "next/link";
import { Inter } from "next/font/google";

const inter = Inter({ subsets: ["latin"] });
<Image src="/hero.jpg" alt="Hero" width={1200} height={600} priority />
<Link href="/dashboard">Dashboard</Link>
```

## Example

**Input**: Build a posts page with server-side data and client-side search filtering.

**Output -- Server Component (data fetching):**

```tsx
// app/posts/page.tsx
import { prisma } from "@/lib/prisma";
import { PostList } from "@/components/post-list";

export default async function PostsPage() {
  const posts = await prisma.post.findMany({
    orderBy: { createdAt: "desc" },
    take: 20,
    include: { author: { select: { name: true } } },
  });
  return (
    <main className="mx-auto max-w-3xl p-6">
      <h1 className="text-2xl font-bold mb-4">Posts</h1>
      <PostList posts={posts} />
    </main>
  );
}
```

**Output -- Client Component (interactivity):**

```tsx
// components/post-list.tsx
"use client";
import { useState } from "react";
import type { Post } from "@prisma/client";

type PostWithAuthor = Post & { author: { name: string } };

export function PostList({ posts }: { posts: PostWithAuthor[] }) {
  const [search, setSearch] = useState("");
  const filtered = posts.filter((p) =>
    p.title.toLowerCase().includes(search.toLowerCase())
  );
  return (
    <div>
      <input
        type="text" value={search} onChange={(e) => setSearch(e.target.value)}
        placeholder="Search posts..." className="w-full rounded border px-3 py-2 mb-4"
      />
      <ul className="space-y-3">
        {filtered.map((post) => (
          <li key={post.id} className="rounded border p-4">
            <h2 className="font-semibold">{post.title}</h2>
            <p className="text-sm text-gray-500">by {post.author.name}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

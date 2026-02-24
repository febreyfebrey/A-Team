---
name: Common Patterns
description: Defines approved Next.js and TypeScript patterns for API responses, hooks, services, and error handling
---

# Common Patterns

## API Response Type Interface

You must use this typed interface for all API responses:

```typescript
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
  };
  meta?: {
    total: number;
    page: number;
    limit: number;
  };
}
```

## Custom Hooks Pattern

You must encapsulate reusable client-side logic in custom hooks. Each hook must have a single responsibility.

### useDebounce

```typescript
import { useState, useEffect } from "react";

export function useDebounce<T>(value: T, delayMs: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delayMs);
    return () => clearTimeout(timer);
  }, [value, delayMs]);

  return debouncedValue;
}
```

### useQuery (Data Fetching)

```typescript
import { useState, useEffect } from "react";

interface UseQueryResult<T> {
  data: T | null;
  error: string | null;
  isLoading: boolean;
}

export function useQuery<T>(url: string): UseQueryResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchData() {
      setIsLoading(true);
      try {
        const response = await fetch(url, { signal: controller.signal });
        if (!response.ok) {
          throw new Error(`Request failed with status ${response.status}`);
        }
        const json: ApiResponse<T> = await response.json();
        if (!json.success) {
          throw new Error(json.error?.message ?? "Unknown error");
        }
        setData(json.data ?? null);
        setError(null);
      } catch (err) {
        if (err instanceof DOMException && err.name === "AbortError") return;
        setError(err instanceof Error ? err.message : "Fetch failed");
      } finally {
        setIsLoading(false);
      }
    }

    fetchData();
    return () => controller.abort();
  }, [url]);

  return { data, error, isLoading };
}
```

## Repository/Service Pattern

You must separate data access from route handlers. Route handlers validate input, call services, and format responses. Services contain business logic. Repositories (or Prisma calls within services) handle data access.

```typescript
// src/services/user.service.ts
import { prisma } from "@/lib/prisma";
import { AppError } from "@/lib/errors";
import type { CreateUserInput } from "@/lib/validation";

export async function createUser(input: CreateUserInput) {
  const existing = await prisma.user.findUnique({
    where: { email: input.email },
  });

  if (existing) {
    throw new AppError("DUPLICATE_EMAIL", "Email already registered", 409);
  }

  return prisma.user.create({
    data: {
      email: input.email,
      name: input.name,
    },
    select: { id: true, email: true, name: true, createdAt: true },
  });
}
```

## Prisma Client Singleton

You must use a singleton Prisma Client instance to prevent connection exhaustion during development:

```typescript
// src/lib/prisma.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") {
  globalForPrisma.prisma = prisma;
}
```

## Next.js Middleware Pattern

You must use Next.js middleware for cross-cutting concerns (authentication, request ID injection, CORS):

```typescript
// src/middleware.ts
import { NextRequest, NextResponse } from "next/server";
import { verifyToken } from "@/lib/auth";

const PUBLIC_PATHS = ["/api/auth/login", "/api/auth/register", "/api/health"];

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;

  if (PUBLIC_PATHS.some((path) => pathname.startsWith(path))) {
    return NextResponse.next();
  }

  const token = request.headers.get("Authorization")?.replace("Bearer ", "");
  if (!token) {
    return NextResponse.json(
      { success: false, error: { code: "UNAUTHORIZED", message: "Missing authentication token" } },
      { status: 401 }
    );
  }

  const payload = verifyToken(token);
  if (!payload) {
    return NextResponse.json(
      { success: false, error: { code: "UNAUTHORIZED", message: "Invalid authentication token" } },
      { status: 401 }
    );
  }

  const response = NextResponse.next();
  response.headers.set("x-user-id", payload.userId);
  return response;
}

export const config = {
  matcher: ["/api/:path*"],
};
```

## Error Boundary Pattern

You must use error boundaries for client-side error recovery:

```typescript
// src/components/ui/ErrorBoundary.tsx
"use client";

import { Component, type ReactNode, type ErrorInfo } from "react";

interface Props {
  children: ReactNode;
  fallback: ReactNode;
}

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(): State {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo): void {
    console.error("ErrorBoundary caught:", error, errorInfo);
  }

  render(): ReactNode {
    if (this.state.hasError) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}
```

## Violation Scenarios

- Using raw `fetch` without error handling (no try-catch, no response status check) -- Violation
- Placing business logic directly in route handlers instead of service modules -- Violation
- Creating multiple `new PrismaClient()` instances instead of using the singleton -- Violation
- Using inline anonymous functions for hooks logic instead of extracting into named custom hooks -- Violation

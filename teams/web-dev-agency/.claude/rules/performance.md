---
name: Performance Guidelines
description: Enforces frontend rendering, backend query optimization, and build-time performance standards
---

# Performance Guidelines

## Frontend Performance

### Component Rendering

You must wrap expensive components (those with heavy computation or large subtrees) in `React.memo`:

```typescript
import { memo } from "react";

interface DataTableProps {
  rows: Row[];
  columns: Column[];
}

export const DataTable = memo(function DataTable({ rows, columns }: DataTableProps) {
  // Expensive rendering logic here
  return <table>{/* ... */}</table>;
});
```

You must use `useMemo` for expensive computations and `useCallback` for callback functions passed as props to memoized children. You must not wrap every component in `React.memo` -- only those where profiling shows unnecessary re-renders.

### Code Splitting

You must use dynamic imports for route-level code splitting and heavy components:

```typescript
import dynamic from "next/dynamic";

const HeavyChart = dynamic(() => import("@/components/features/HeavyChart"), {
  loading: () => <ChartSkeleton />,
});
```

You must not dynamically import components that are visible in the initial viewport (above the fold).

### Image Optimization

You must use the Next.js `<Image>` component for all images. You must specify `width` and `height` or use the `fill` prop with a sized container:

```typescript
import Image from "next/image";

<Image src="/hero.jpg" alt="Hero banner" width={1200} height={600} priority />
```

You must set `priority` on images that appear above the fold. You must use `loading="lazy"` (the default) for images below the fold.

### Key Props

You must use stable, unique identifiers as `key` props in lists. You must not use array indices as keys when the list can be reordered, filtered, or modified:

```typescript
// Correct: use a stable ID
{users.map((user) => <UserCard key={user.id} user={user} />)}

// Violation: use array index on a mutable list
{users.map((user, index) => <UserCard key={index} user={user} />)}
```

### Re-render Prevention

You must avoid these patterns that cause unnecessary re-renders:

- Creating new objects or arrays inline in JSX props (`style={{ color: "red" }}`)
- Defining callback functions inline in JSX (`onClick={() => handleClick(id)}`) when passed to memoized children
- Storing derived data in state instead of computing it during render

## Backend Performance

### Prisma Query Optimization

You must select only the fields you need. You must not fetch entire records when a subset of fields is sufficient:

```typescript
// Correct: select specific fields
const users = await prisma.user.findMany({
  select: { id: true, name: true, email: true },
  where: { isActive: true },
});

// Violation: fetch all columns when only id, name, email are used
const users = await prisma.user.findMany({
  where: { isActive: true },
});
```

You must use `include` only when the related data is consumed by the caller. You must not eagerly load relations that the current request does not need.

### Connection Pooling

You must configure Prisma connection pooling for production by setting `connection_limit` in the database URL:

```
DATABASE_URL="mysql://user:pass@host:3306/db?connection_limit=10"
```

You must use the Prisma Client singleton pattern (see `patterns.md`) to prevent connection exhaustion during development.

### Response Caching

You must set appropriate cache headers for responses that do not change frequently:

```typescript
return NextResponse.json(
  { success: true, data },
  {
    headers: {
      "Cache-Control": "public, s-maxage=60, stale-while-revalidate=300",
    },
  }
);
```

You must not cache responses that contain user-specific or sensitive data.

### Pagination

You must paginate all list endpoints. You must not return unbounded result sets:

```typescript
const page = parsed.data.page;
const limit = parsed.data.limit;

const [items, total] = await Promise.all([
  prisma.item.findMany({
    skip: (page - 1) * limit,
    take: limit,
    select: { id: true, name: true },
  }),
  prisma.item.count({ where: filters }),
]);

return successResponse(items, 200, { total, page, limit });
```

## Build Performance

You must analyze bundle size after adding new dependencies. Run `npx next build` and review the output sizes. You must investigate any page bundle exceeding 200KB (first load JS).

You must verify tree shaking works for imported libraries. Import specific functions, not entire modules:

```typescript
// Correct: import specific function
import { format } from "date-fns/format";

// Violation: import entire library
import { format } from "date-fns";
```

You must minimize client-side JavaScript by using Server Components wherever state and interactivity are not required.

## Violation Scenarios

- Fetching all columns when only 2-3 fields are needed -- Violation
- List endpoint returns all records without pagination -- Violation
- Using `<img>` tag instead of Next.js `<Image>` component -- Violation
- Importing an entire library when only one function is used -- Violation
- Page bundle exceeds 200KB first-load JS without documented justification -- Violation

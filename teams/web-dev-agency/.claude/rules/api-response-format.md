---
name: API Response Format
description: Enforces a unified JSON envelope structure for all API success and error responses
---

# API Response Format

## Success Response

Every API endpoint returning a successful result must use this structure:

```typescript
interface SuccessResponse<T> {
  success: true;
  data: T;
  meta?: {
    total: number;
    page: number;
    limit: number;
  };
}
```

Example:

```json
{
  "success": true,
  "data": {
    "id": "usr_abc123",
    "email": "user@example.com",
    "name": "Jane Doe"
  }
}
```

Paginated example:

```json
{
  "success": true,
  "data": [
    { "id": "usr_abc123", "name": "Jane Doe" },
    { "id": "usr_def456", "name": "John Smith" }
  ],
  "meta": {
    "total": 42,
    "page": 1,
    "limit": 20
  }
}
```

## Error Response

Every API endpoint returning an error must use this structure:

```typescript
interface ErrorResponse {
  success: false;
  error: {
    code: string;
    message: string;
  };
}
```

Example:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email field is required"
  }
}
```

## HTTP Status Code Usage

You must use these status codes consistently across all API routes:

| Status Code | Meaning | When to Use |
|-------------|---------|-------------|
| 200 | OK | Successful GET, PUT, PATCH, DELETE |
| 201 | Created | Successful POST that creates a new resource |
| 400 | Bad Request | Input validation failure (Zod parse error) |
| 401 | Unauthorized | Missing or invalid authentication token |
| 403 | Forbidden | Authenticated user lacks permission for the action |
| 404 | Not Found | Requested resource does not exist |
| 409 | Conflict | Resource state conflict (duplicate email, version mismatch) |
| 422 | Unprocessable Entity | Input is syntactically valid but semantically wrong |
| 500 | Internal Server Error | Unhandled server exception |

## Request Validation

You must validate all incoming request data using Zod schemas:

```typescript
import { z } from "zod";

const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
});

// Infer the TypeScript type from the schema
type CreateUserInput = z.infer<typeof CreateUserSchema>;
```

You must parse request bodies with `safeParse` and return a 400 error with the wrapper structure on failure:

```typescript
const parsed = CreateUserSchema.safeParse(body);
if (!parsed.success) {
  return NextResponse.json(
    {
      success: false,
      error: {
        code: "VALIDATION_ERROR",
        message: parsed.error.issues[0].message,
      },
    },
    { status: 400 }
  );
}
```

## Typed Response Helpers

You must use response builder functions to ensure consistent formatting:

```typescript
export function successResponse<T>(data: T, status = 200) {
  return NextResponse.json({ success: true, data }, { status });
}

export function errorResponse(code: string, message: string, status: number) {
  return NextResponse.json(
    { success: false, error: { code, message } },
    { status }
  );
}
```

## Violation Scenarios

- API route returns raw data without the `{ success, data }` wrapper -- Violation
- API error response omits the `error.code` field -- Violation
- API returns 200 for a resource creation (must use 201) -- Violation
- API returns a generic 500 with a raw error message or stack trace -- Violation
- Request body is used without Zod validation -- Violation

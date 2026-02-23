---
name: SD Template
description: System Design document template in Confluence format with API specs and database schema
---

# SD Template

## Purpose

Use this template to produce a complete SD (System Design) document in Confluence page format. The target audience is the development team. Write in Traditional Chinese. Present technical terms bilingually on first occurrence: Chinese followed by English in parentheses.

## Document Structure

Every SD document must contain the following sections in this exact order. Do not omit or reorder any section.

### 1. 文件資訊

| Field | Description |
|-------|-------------|
| 文件名稱 | SD document title including project name |
| 版本 | Semantic version (e.g., v1.0) |
| 作者 | Author name |
| 建立日期 | YYYY-MM-DD |
| 最後更新 | YYYY-MM-DD |
| 狀態 | Draft / In Review / Confirmed |
| 關聯 SA | Link to the corresponding SA document |

### 2. 架構總覽圖

Provide a system architecture diagram showing: frontend components, backend services, data layer (databases, caches, message queues), infrastructure layer (load balancer, CDN), and external integrations.

Use Confluence's draw.io macro. Label every component with its technology choice (e.g., "API Gateway (Kong)", "Cache (Redis)"). Below the diagram, provide a 2-3 paragraph summary explaining the architecture rationale.

### 3. 技術選型

List every technology decision in a table:

| 類別 | 選擇 | 版本 | 選擇理由 | 備選方案 |
|------|------|------|---------|---------|
| 程式語言 | e.g., TypeScript | 5.x | ... | ... |
| Web 框架 | e.g., NestJS | 10.x | ... | ... |
| 資料庫 | e.g., PostgreSQL | 15 | ... | ... |
| 快取 | e.g., Redis | 7.x | ... | ... |
| 訊息佇列 | e.g., RabbitMQ | 3.x | ... | ... |

Every technology choice must have a rationale. Do not list a technology without explaining why it was selected over alternatives.

### 4. API 設計

For each API endpoint, document using this format:

```
### {API Name}

- **端點 (Endpoint)**: {HTTP Method} {Path}
- **描述**: {What this API does}
- **認證**: {Required / Optional / None}
- **權限**: {Which roles can call this API}

**Request:**

| 參數 | 位置 | 型態 | 必填 | 說明 |
|------|------|------|------|------|
| ... | Path / Query / Body / Header | String / Number / Boolean | Yes/No | ... |

**Request Body Example:**
\```json
{
  "field": "value"
}
\```

**Response (Success - 200/201):**
\```json
{
  "field": "value"
}
\```

**Error Responses:**

| HTTP Status | Error Code | 說明 |
|-------------|-----------|------|
| 400 | INVALID_INPUT | ... |
| 401 | UNAUTHORIZED | ... |
| 404 | NOT_FOUND | ... |
```

Group APIs by module. Present all endpoints for one module before moving to the next.

### 5. 資料庫 Schema

For each table, document:

```
### Table: {table_name}

| 欄位名稱 | 資料型態 | 約束 | 預設值 | 說明 |
|---------|---------|------|-------|------|
| id | UUID | PK | gen_random_uuid() | Primary key |
| ... | ... | ... | ... | ... |
| created_at | TIMESTAMP | NOT NULL | NOW() | Record creation time |
| updated_at | TIMESTAMP | NOT NULL | NOW() | Last update time |

**索引 (Indexes):**
- idx_{table}_{column} ON {column} — {reason for index}

**關聯 (Relations):**
- {table_name}.{column} → {other_table}.{column} (FK, ON DELETE {action})
```

After all table definitions, include a database ER diagram using draw.io.

### 6. 模組互動序列圖

For each major workflow, create a sequence diagram showing:

- The actors (user, frontend, backend services, database, external services)
- The message flow with labeled arrows (API calls, DB queries, responses)
- Error handling branches

Use PlantUML or draw.io within Confluence. Provide at least one sequence diagram per major feature module.

### 7. 錯誤處理策略

Define the error handling approach:

**Error Response Format:**
```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "details": {}
  }
}
```

**Error Categories:**

| 類別 | HTTP Status Range | 處理策略 | 重試策略 |
|------|------------------|---------|---------|
| 驗證錯誤 (Validation) | 400 | Return field-level errors | No retry |
| 認證錯誤 (Auth) | 401, 403 | Return error, log attempt | No retry |
| 資源不存在 (Not Found) | 404 | Return error | No retry |
| 伺服器錯誤 (Server) | 500 | Log full stack trace, return generic message | Retry with backoff |
| 外部服務錯誤 (External) | 502, 503 | Circuit breaker, fallback | Retry 3 times |

### 8. 部署架構

Describe the deployment setup covering: environment list (Development, Staging, Production), container strategy (Docker build, registry, orchestration), CI/CD pipeline (Build → Test → Deploy), and infrastructure diagram (servers, load balancers, DB clusters, monitoring). Use a Confluence draw.io diagram for the infrastructure layout.

## Example

### Input

Module: User Authentication (from SA document)

### Output (Partial — Section 4 excerpt)

```
## 4. API 設計

### 4.1 使用者認證 (Authentication)

### 使用者註冊 (User Registration)

- **端點 (Endpoint)**: POST /api/v1/auth/register
- **描述**: 建立新使用者帳號並寄送 Email 驗證信
- **認證**: None
- **權限**: Public

**Request:**

| 參數 | 位置 | 型態 | 必填 | 說明 |
|------|------|------|------|------|
| email | Body | String | Yes | 使用者 Email，格式驗證 |
| password | Body | String | Yes | 密碼，最少 8 字元，含大小寫與數字 |
| display_name | Body | String | Yes | 顯示名稱，2-50 字元 |

**Request Body Example:**
{ "email": "user@example.com", "password": "SecurePass123", "display_name": "John Doe" }

**Response (Success - 201):**
{ "data": { "user_id": "550e8400-...", "email": "user@example.com", "display_name": "John Doe", "status": "pending_verification" } }

**Error Responses:**

| HTTP Status | Error Code | 說明 |
|-------------|-----------|------|
| 400 | INVALID_EMAIL | Email 格式不正確 |
| 400 | WEAK_PASSWORD | 密碼不符合強度要求 |
| 409 | EMAIL_EXISTS | 此 Email 已被註冊 |
```

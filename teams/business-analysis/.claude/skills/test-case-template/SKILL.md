---
name: Test Case Template
description: Test Cases document template in Confluence format for PM and QA team execution
---

# Test Case Template

## Purpose

Use this template to produce a complete Test Cases document in Confluence page format. The target audience is the PM and QA team. Write in Traditional Chinese. Present technical terms bilingually on first occurrence: Chinese followed by English in parenthases.

Every test case must be concrete enough for a QA engineer to execute without asking follow-up questions.

## Document Structure

Every Test Cases document must contain the following sections in this exact order. Do not omit or reorder any section.

### 1. 文件資訊

| Field | Description |
|-------|-------------|
| 文件名稱 | Test Cases document title including project name |
| 版本 | Semantic version (e.g., v1.0) |
| 作者 | Author name |
| 建立日期 | YYYY-MM-DD |
| 最後更新 | YYYY-MM-DD |
| 狀態 | Draft / In Review / Confirmed |
| 關聯 PRD | Link to the corresponding PRD document |
| 關聯 SD | Link to the corresponding SD document |

### 2. 測試範圍

Define what is covered and what is excluded from testing:

**範圍內 (In Scope):**
- List every feature module included in testing
- Reference the PRD section for each module

**範圍外 (Out of Scope):**
- List features excluded from this test cycle with justification

**測試環境 (Test Environment):**
- Environment URL or setup instructions
- Test account credentials (reference, not actual values)
- Required test data setup

### 3. 測試案例清單

Organize test cases by feature module. Use a Confluence table with the following columns:

| 測試編號 | 功能模組 | 測試案例名稱 | 前置條件 | 測試步驟 | 預期結果 | 優先級 |
|---------|---------|------------|---------|---------|---------|--------|
| TC-{MOD}-{NNN} | Module name | Descriptive test name | State required before test | Numbered steps | Observable expected outcome | P0/P1/P2 |

Rules for writing test cases:

- **測試編號**: Use the format TC-{MODULE}-{NNN} (e.g., TC-AUTH-001). Number sequentially within each module.
- **前置條件**: State the exact system state required. Include specific test data (e.g., "User account user@test.com exists and is verified"). Never write "system is ready" — specify what "ready" means.
- **測試步驟**: Write numbered steps. Each step must be a single user action or system interaction. Do not combine multiple actions in one step.
- **預期結果**: State the observable outcome. Include specific values, messages, or state changes. Never write "system works correctly" — specify what "correctly" means.
- **優先級**: P0 = must pass for release, P1 = must pass for stable release, P2 = nice to verify.

### 4. 邊界條件測試

For each input field or parameter, define boundary test cases:

| 測試編號 | 欄位名稱 | 測試情境 | 輸入值 | 預期結果 |
|---------|---------|---------|-------|---------|
| TC-BND-{NNN} | Field name | Min/Max/Empty/Over limit/Special chars | Exact input value | Exact expected behavior |

Cover the following boundary types for every user input:

- **Empty input**: Null, empty string, whitespace only
- **Minimum valid**: Shortest or smallest acceptable value
- **Maximum valid**: Longest or largest acceptable value
- **Over maximum**: One character or unit beyond the limit
- **Special characters**: Unicode, emoji, HTML tags, SQL injection strings
- **Format validation**: Invalid email format, phone number format

### 5. API 測試案例

For each API endpoint in the SD document, define test cases:

| 測試編號 | API 端點 | HTTP Method | 測試情境 | Request Body | 預期 Status | 預期 Response |
|---------|---------|-------------|---------|-------------|------------|--------------|
| TC-API-{NNN} | /api/v1/... | GET/POST/PUT/DELETE | Scenario description | JSON body or "N/A" | HTTP status code | Key response fields |

Cover these scenarios for every API:

- **Happy path**: Valid request with all required fields
- **Missing required fields**: Omit each required field one at a time
- **Invalid field values**: Wrong data type, out-of-range values
- **Authentication failures**: No token, expired token, invalid token
- **Authorization failures**: Valid token but insufficient permissions
- **Concurrent requests**: Same operation executed simultaneously (where relevant)

### 6. 驗收測試對照表

Map every PRD acceptance criterion to one or more test cases:

| PRD 需求編號 | 驗收標準 | 對應測試案例 | 測試結果 | 備註 |
|-------------|---------|------------|---------|------|
| US-AUTH-001 | AC-1: ... | TC-AUTH-001, TC-AUTH-002 | Pass / Fail / Not Tested | ... |

Every acceptance criterion in the PRD must have at least one corresponding test case. If an acceptance criterion has no test case, flag it as a gap.

## Example

### Input

Feature: User Registration (from PRD US-AUTH-001)

### Output (Partial — Section 3 excerpt)

```
## 3. 測試案例清單

### 3.1 使用者認證 (Authentication)

| 測試編號 | 功能模組 | 測試案例名稱 | 前置條件 | 測試步驟 | 預期結果 | 優先級 |
|---------|---------|------------|---------|---------|---------|--------|
| TC-AUTH-001 | 使用者註冊 | Email 註冊成功 | 1. 測試環境已啟動 2. Email「newuser@test.com」尚未註冊 | 1. 開啟註冊頁面 2. 輸入 Email: newuser@test.com 3. 輸入密碼: Test1234 4. 輸入顯示名稱: Test User 5. 點擊「註冊」按鈕 | 1. 頁面顯示「註冊成功，請查收驗證信」訊息 2. 系統寄送驗證信至 newuser@test.com 3. 資料庫新增使用者記錄，狀態為 pending_verification | P0 |
| TC-AUTH-002 | 使用者註冊 | 重複 Email 註冊 | 1. Email「existing@test.com」已註冊且已驗證 | 1. 開啟註冊頁面 2. 輸入 Email: existing@test.com 3. 輸入密碼: Test1234 4. 輸入顯示名稱: Test User 5. 點擊「註冊」按鈕 | 1. 頁面顯示「此 Email 已被使用」錯誤訊息 2. 系統不寄送任何信件 3. 資料庫無新增記錄 | P0 |
| TC-AUTH-003 | 使用者註冊 | 密碼強度不足 | 1. 測試環境已啟動 | 1. 開啟註冊頁面 2. 輸入 Email: user@test.com 3. 輸入密碼: 123 4. 點擊「註冊」按鈕 | 1. 頁面顯示「密碼長度不足，至少需要 8 個字元」提示 2. 註冊表單不送出 | P0 |
```

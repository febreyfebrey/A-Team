---
name: JIRA Operation
description: JIRA ticket creation and update specifications at each project milestone
---

# JIRA Operation

## Purpose

Use this skill to create and update JIRA tickets at each project milestone. Every ticket operation must follow the defined specification for that milestone. Do not create tickets outside of the designated milestones.

## Milestone-Based Operations

### Milestone 1: PRD Confirmed

Trigger: PM has approved the PRD document.

**Create Epic:**

| Field | Value |
|-------|-------|
| Issue Type | Epic |
| Summary | Project name from PRD 文件資訊 |
| Description | Copy the 目標與範圍 section from PRD. Include scope-in and scope-out lists. |
| Priority | Highest |
| Labels | `prd-confirmed`, `{project-name}` |

**Create Stories (one per User Story):**

For each User Story (US-{MOD}-{NNN}) in the PRD:

| Field | Value |
|-------|-------|
| Issue Type | Story |
| Summary | US-{MOD}-{NNN}: {User Story title} |
| Description | Full User Story text ("As a... I want to... so that...") + all acceptance criteria in checklist format |
| Priority | Map from PRD: P0 → Highest, P1 → High, P2 → Medium |
| Labels | `{module-name}`, `{project-name}` |
| Epic Link | Link to the Epic created above |

### Milestone 2: SA Confirmed

Trigger: Development team has approved the SA document.

**Update each Story's description:**

Append the following section to each Story's existing description:

```
----
h3. System Analysis Context

*Related Modules:* {list modules from SA Section 3 that relate to this Story}
*Data Entities:* {list data entities from SA Section 6 that this Story touches}
*Business Rules:* {list business rule IDs from SA Section 7 that apply}
*External Dependencies:* {list external system names from SA Section 5 involved}
```

Do not overwrite the original Story description. Append the SA context below a horizontal rule.

### Milestone 3: SD Confirmed

Trigger: Development team has approved the SD document.

**Create Sub-tasks under each Story:**

For each Story, create sub-tasks based on the SD document's module breakdown:

| Sub-task Type | When to Create | Summary Format | Description Content |
|--------------|---------------|----------------|-------------------|
| Frontend Task | Story involves UI changes | `[FE] {feature description}` | Related UI/UX page from PRD Section 6 + relevant API endpoints from SD Section 4 |
| Backend Task | Story involves API or business logic | `[BE] {feature description}` | API endpoint specs from SD Section 4 + business rules from SA Section 7 |
| DB Task | Story involves new tables or schema changes | `[DB] {table or migration description}` | Schema definition from SD Section 5 + indexes and constraints |

Sub-task fields:

| Field | Value |
|-------|-------|
| Issue Type | Sub-task |
| Summary | As defined above |
| Description | As defined above |
| Priority | Inherit from parent Story |
| Labels | `{task-type}` (fe / be / db), `{project-name}` |

### Milestone 4: Test Cases Confirmed

Trigger: QA team has approved the Test Cases document.

**Create Test Sub-tasks under each Story:**

Group test cases by their parent Story (using the 驗收測試對照表 mapping). For each group:

| Field | Value |
|-------|-------|
| Issue Type | Sub-task |
| Summary | `[QA] {module name} - Test Execution` |
| Description | List all test case IDs (TC-{MOD}-{NNN}) assigned to this Story, with test case names. Include link to Test Cases document section. |
| Priority | Inherit from parent Story |
| Labels | `qa`, `{project-name}` |

## Field Constraints

Use only standard JIRA fields:

- **Summary**: Under 120 characters. Use prefixes ([FE], [BE], [DB], [QA]) for sub-tasks.
- **Description**: Use JIRA wiki markup format (h3. for headings, * for bullets, {code} for code blocks).
- **Priority**: Highest / High / Medium / Low / Lowest — only these five values.
- **Labels**: Lowercase, kebab-case, no spaces. Maximum 3 labels per ticket.

Do not use custom fields unless the user explicitly provides the custom field name and ID.

## Example

### Input (Confirmed PRD Summary)

Project name: 會員認證系統 (User Authentication System)
User Stories:
- US-AUTH-001: Email registration (P0)
- US-AUTH-002: Email login (P0)
- US-AUTH-003: Google SSO login (P1)

### Output (JIRA Epic + Stories Creation Specification)

**Epic:**
```
Issue Type: Epic
Summary: 會員認證系統 (User Authentication System)
Description:
h2. 目標與範圍

*專案目標:*
* 提供安全的使用者認證機制，支援 Email 與 Google SSO 登入
* 登入 API 回應時間 < 500ms (P95)
* 系統可用性 99.9%

*範圍內 (In Scope):*
* Email 註冊與登入
* Google SSO 登入
* 密碼重設流程

*範圍外 (Out of Scope):*
* Apple ID 登入（延至第二階段）
* 多因子認證 MFA（延至第二階段）

Priority: Highest
Labels: prd-confirmed, user-auth-system
```

**Story 1:**
```
Issue Type: Story
Summary: US-AUTH-001: Email 註冊功能
Description:
*User Story:* 身為一般使用者，我想要透過 Email 註冊帳號，以便開始使用平台服務。

*驗收標準 (Acceptance Criteria):*
* [x] Given 使用者在註冊頁面，when 輸入有效 Email 與密碼並送出，then 系統建立帳號並寄送驗證信
* [x] Given 使用者輸入已存在的 Email，when 送出表單，then 顯示「此 Email 已被使用」
* [x] Given 密碼少於 8 字元，when 送出表單，then 顯示密碼長度不足提示

Priority: Highest
Labels: auth, user-auth-system
Epic Link: 會員認證系統 (User Authentication System)
```

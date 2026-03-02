---
name: PRD Template
description: Product Requirements Document template in Confluence format with UI/UX page descriptions
---

# PRD Template

## Purpose

Use this template to produce a complete PRD (Product Requirements Document) in Confluence page format. Write in Traditional Chinese. Present technical terms bilingually on first occurrence: Chinese followed by English in parentheses.

## Document Structure

Every PRD must contain the following sections in this exact order. Do not omit or reorder any section.

### 1. 文件資訊

Place this at the top of the Confluence page using an info panel or table macro.

| Field | Description |
|-------|-------------|
| 文件名稱 | PRD title including project name |
| 版本 | Semantic version (e.g., v1.0, v1.1) |
| 作者 | Author name |
| 建立日期 | YYYY-MM-DD |
| 最後更新 | YYYY-MM-DD |
| 狀態 | Draft / In Review / Confirmed |
| 審核者 | Reviewer names |

### 2. 專案背景

Write 2-4 paragraphs covering:
- The business problem or opportunity driving this project
- Current pain points or gaps in existing solutions
- Why this project is prioritized now

Do not include implementation details. Keep the focus on the "why."

### 3. 目標與範圍

Define the project goals using measurable outcomes. List scope boundaries explicitly.

- **專案目標**: 2-5 bullet points, each with a measurable success metric
- **範圍內 (In Scope)**: Features and capabilities included in this release
- **範圍外 (Out of Scope)**: Features explicitly excluded, with brief justification

### 4. 使用者角色

Define every user role with a structured profile:

| Field | Content |
|-------|---------|
| 角色名稱 | Role name (bilingual) |
| 描述 | One-sentence role description |
| 主要目標 | What this user wants to achieve |
| 使用頻率 | Daily / Weekly / Occasional |
| 技術程度 | High / Medium / Low |

### 5. 功能需求（User Stories）

Organize by feature module. Each User Story must follow this format:

```
**US-{module}-{number}**: As a {role}, I want to {action}, so that {benefit}.

**驗收標準 (Acceptance Criteria):**
- Given {context}, when {action}, then {result}
- Given {context}, when {action}, then {result}

**優先級**: P0 (Must) / P1 (Should) / P2 (Could)
**關聯頁面**: {page name from UI/UX section}
```

### 6. 頁面 UI/UX 需求描述

For each page or screen, provide:

| Field | Content |
|-------|---------|
| 頁面名稱 | Page name (bilingual) |
| 用途 | What the user accomplishes on this page |
| 進入方式 | How the user navigates to this page |
| 頁面佈局 | Describe the layout structure (header, sidebar, main content area, footer) |
| 關鍵元件 | List UI components: buttons, forms, tables, modals, with their purpose |
| 互動行為 | Describe user interactions: click, hover, drag, form validation feedback |
| 狀態變化 | Empty state, loading state, error state, success state |
| 關聯 User Stories | Reference US-{module}-{number} |

Use Confluence layout macros (two-column, three-column) for page layout descriptions when the page has distinct zones.

### 7. 非功能需求

Cover the following categories. Provide specific numeric targets, not vague descriptions.

- **效能 (Performance)**: Response time, throughput, concurrent user count
- **可用性 (Availability)**: Uptime SLA percentage
- **安全性 (Security)**: Authentication method, data encryption, compliance standards
- **相容性 (Compatibility)**: Supported browsers, devices, OS versions
- **可擴展性 (Scalability)**: Expected growth, capacity planning targets

### 8. 驗收標準

Provide a summary checklist mapping each functional requirement to a testable acceptance criterion. Use a table:

| 需求編號 | 需求摘要 | 驗收條件 | 驗證方式 |
|---------|---------|---------|---------|
| US-AUTH-001 | User login | ... | Manual / Automated |

### 9. 排除範圍

List features, integrations, or scenarios explicitly not covered in this release. For each exclusion, state the reason (e.g., "deferred to phase 2", "business decision", "technical constraint").

### 10. 名詞定義

Define all domain-specific terms used in the document. Use a table:

| 名詞 | 英文 | 定義 |
|------|------|------|
| ... | ... | One-sentence definition |

## Confluence Formatting Guidance

- Use Confluence heading levels: h1 for document title, h2 for major sections, h3 for subsections
- Use info panels (`{info}`) for important notes and context blocks
- Use status macros for document status (Draft / In Review / Confirmed)
- Use table macros for structured data — avoid free-text lists when a table provides clearer structure
- Use the expand macro for lengthy content that readers may skip (e.g., detailed acceptance criteria)

## Example

### Input

Project: User Authentication Module for e-commerce platform

### Output (Partial — Section 5 excerpt)

```
## 5. 功能需求（User Stories）

### 5.1 使用者認證 (Authentication)

**US-AUTH-001**: 身為一般使用者 (Consumer)，我想要透過 Email 註冊帳號，以便開始使用平台的購物功能。

**驗收標準 (Acceptance Criteria):**
- Given 使用者在註冊頁面，when 輸入有效 Email 與密碼並送出，then 系統建立帳號並寄送驗證信
- Given 使用者輸入已註冊的 Email，when 送出註冊表單，then 系統顯示「此 Email 已被使用」錯誤訊息
- Given 使用者輸入少於 8 字元的密碼，when 送出表單，then 系統顯示密碼長度不足提示

**優先級**: P0 (Must)
**關聯頁面**: 註冊頁面 (Registration Page)
```

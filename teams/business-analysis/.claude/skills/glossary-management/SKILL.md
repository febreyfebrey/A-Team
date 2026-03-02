---
name: Glossary Management
description: Maintain company-level glossary with term definitions, preferred usage, and cross-project consistency
---

# Glossary Management

## Purpose

Use this skill to create, maintain, and enforce a company-level glossary that ensures consistent terminology across all projects. The glossary is the single source of truth — all agents must defer to it when choosing terminology.

## Glossary File Format

The glossary file is stored at `projects/{company-name}/glossary.md` and follows this structure:

```markdown
# {Company Name} Glossary

> Last updated: YYYY-MM-DD
> Total terms: {count}

## Terms

| 術語 ID | 中文術語 | 英文術語 | 定義 | 偏好用法 | 已棄用別名 | 適用範圍 | 最後更新 |
|---------|---------|---------|------|---------|-----------|---------|---------|
| TERM-001 | 會員 | Member | 已完成註冊並通過驗證的平台使用者 | 「會員」 | 用戶、使用者（指已註冊者時） | 所有專案 | 2024-01-15 |

## Change Log

| 日期 | 術語 ID | 變更類型 | 變更內容 | 變更原因 |
|------|---------|---------|---------|---------|
| YYYY-MM-DD | TERM-001 | 新增/修改/棄用別名 | {description} | {reason} |
```

## Term Lifecycle

### 新增術語

When a new term is identified during a project:

1. Check the glossary for existing entries covering the same concept
2. If no match exists, create a new entry with all required fields
3. If a similar term exists with a different name, do NOT create a duplicate — update the existing entry's 已棄用別名 field
4. Record the addition in the Change Log with the reason and source project

### 更新術語

When a term's definition or preferred usage must change:

1. Update the term entry with the new information
2. Move the old preferred usage to 已棄用別名 if the name changed
3. Record the change in the Change Log
4. Notify all active projects using this term (via Coordinator)

### 解決衝突

When two projects use different terms for the same concept:

1. Compare both terms' usage frequency and clarity
2. Select the term that is more precise and widely understood
3. Set the selected term as 偏好用法
4. Add the other term to 已棄用別名
5. Record the resolution in the Change Log with rationale

## Cross-Project Term Search

When preparing a project background pack, search for relevant terms using:

1. **Domain keyword match** — Search glossary for terms related to the project's domain (e.g., "會員", "認證", "訂單")
2. **Project overlap scan** — Read the PRD of each existing project under the company directory, extract key entities, and cross-reference with the glossary
3. **Entity name comparison** — Compare SA data entity names across projects to detect naming inconsistencies

## Glossary Compliance Check

When reviewing a document for glossary compliance:

1. Extract all domain-specific terms from the document
2. Look up each term in the company glossary
3. Flag terms that:
   - Use a 已棄用別名 instead of the 偏好用法
   - Are not in the glossary and need to be added
   - Have a different definition than the glossary entry
4. Produce a compliance report:

```
### Glossary Compliance Report

| 文件位置 | 使用的術語 | Glossary 偏好用法 | 問題類型 | 建議修正 |
|---------|-----------|-----------------|---------|---------|
| Section 3, para 2 | 用戶 | 會員 (TERM-001) | 使用已棄用別名 | 替換為「會員」 |
| Section 5, US-001 | 購物車 | — | 術語未收錄 | 新增至 Glossary |
```

## Example

### Input

Company: 好買科技
New project: 商品評價系統 (Product Review System)
Existing projects: 會員系統 (Member System), 訂單系統 (Order System)

### Output (Glossary Term Search Result)

**相關既有術語：**

| 術語 ID | 中文術語 | 英文術語 | 來源專案 | 與本專案關聯 |
|---------|---------|---------|---------|------------|
| TERM-001 | 會員 | Member | 會員系統 | 評價者身份識別 |
| TERM-008 | 商品 | Product | 訂單系統 | 評價對象 |
| TERM-012 | 訂單 | Order | 訂單系統 | 評價前提條件（已購買） |

**建議新增術語：**

| 中文術語 | 英文術語 | 建議定義 |
|---------|---------|---------|
| 評價 | Review | 會員針對已購買商品提交的文字評論與評分 |
| 評分 | Rating | 會員給予商品的數值分數（1-5 星） |

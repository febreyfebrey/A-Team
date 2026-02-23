---
name: SA Template
description: System Analysis document template in Confluence format for development team consumption
---

# SA Template

## Purpose

Use this template to produce a complete SA (System Analysis) document in Confluence page format. The target audience is the development team. Write in Traditional Chinese. Present technical terms bilingually on first occurrence: Chinese followed by English in parentheses.

## Document Structure

Every SA document must contain the following sections in this exact order. Do not omit or reorder any section.

### 1. 文件資訊

| Field | Description |
|-------|-------------|
| 文件名稱 | SA document title including project name |
| 版本 | Semantic version (e.g., v1.0) |
| 作者 | Author name |
| 建立日期 | YYYY-MM-DD |
| 最後更新 | YYYY-MM-DD |
| 狀態 | Draft / In Review / Confirmed |
| 關聯 PRD | Link to the corresponding PRD document |

### 2. 系統概覽

Provide a high-level description of the system in 2-3 paragraphs covering:

- What the system does (functional summary)
- Where the system sits within the broader architecture (upstream/downstream systems)
- Key design drivers (performance, security, scalability priorities derived from PRD non-functional requirements)

Include a system context diagram showing the system boundary, external actors, and connected systems. Use Confluence's draw.io macro or PlantUML macro for the diagram.

### 3. 功能模組拆解

Break the system into functional modules. For each module, provide:

| Field | Content |
|-------|---------|
| 模組名稱 | Module name (bilingual) |
| 職責描述 | What this module is responsible for (2-3 sentences) |
| 輸入 | Data or events this module receives |
| 輸出 | Data or events this module produces |
| 關聯 User Stories | Reference US-{module}-{number} from the PRD |

Present modules in a table. After the table, include a module dependency diagram showing which modules interact with each other.

### 4. 資料流程圖

Create data flow diagrams (DFD) for each major workflow. Each diagram must show:

- External entities (users, external systems)
- Processes (system operations)
- Data stores (databases, caches)
- Data flows with labeled arrows indicating what data moves between elements

Use Confluence's draw.io macro. Provide at least one Level-0 (context) diagram and one Level-1 (detailed) diagram for the primary workflow.

Label every flow with the data entity name (e.g., "使用者認證請求 (Auth Request)").

### 5. 外部系統介面

For each external system integration, define:

| Field | Content |
|-------|---------|
| 系統名稱 | External system name |
| 整合方式 | REST API / Webhook / Message Queue / File Transfer |
| 通訊方向 | Inbound / Outbound / Bidirectional |
| 資料格式 | JSON / XML / CSV |
| 認證方式 | API Key / OAuth2 / mTLS |
| 錯誤處理 | What happens when the external system is unavailable |
| SLA | Expected response time and availability |

### 6. 資料實體定義

Define each data entity with its attributes. Use a table per entity:

**Entity: 使用者 (User)**

| 屬性名稱 | 英文欄位名 | 資料型態 | 必填 | 說明 |
|---------|-----------|---------|------|------|
| 使用者 ID | user_id | UUID | Yes | System-generated unique identifier |
| ... | ... | ... | ... | ... |

After all entity tables, include an entity relationship diagram (ERD) showing relationships (one-to-one, one-to-many, many-to-many) between entities. Use draw.io or PlantUML in Confluence.

### 7. 業務規則

List all business rules that govern system behavior. For each rule:

| 規則編號 | 規則描述 | 觸發條件 | 處理邏輯 | 關聯模組 |
|---------|---------|---------|---------|---------|
| BR-001 | ... | When {condition} | Then {action} | Module name |

Business rules must be traceable to PRD requirements. Include the source User Story reference.

### 8. 限制與假設

**限制 (Constraints):**
List technical, business, or regulatory constraints that restrict design options. Each constraint must have a source (e.g., "PRD non-functional requirement", "company security policy").

**假設 (Assumptions):**
List assumptions made during analysis. Each assumption must have a risk statement describing what happens if the assumption proves false.

| 假設編號 | 假設內容 | 風險影響 |
|---------|---------|---------|
| A-001 | ... | If false, then ... |

## Confluence Diagram Guidance

- Use draw.io macro for all diagrams — it supports export, editing, and version history within Confluence
- Keep diagram complexity manageable: no more than 10 entities per diagram; split into multiple diagrams for larger systems
- Label every arrow and connection — unlabeled arrows are prohibited
- Use consistent color coding: blue for internal modules, green for external systems, orange for data stores

## Example

### Input

Project: User Authentication Module (from PRD)

### Output (Partial — Section 2 excerpt)

```
## 2. 系統概覽

使用者認證模組 (User Authentication Module) 負責處理平台所有使用者的身份驗證
與授權管理。本模組支援 Email 密碼登入及 Google SSO 登入兩種認證方式。

本模組位於系統架構的閘道層 (Gateway Layer)，接收來自前端應用 (Frontend App)
的認證請求，並與以下外部系統互動：
- Google OAuth2 服務 — 處理第三方社交登入
- Email 服務 (SendGrid) — 發送驗證信與密碼重設信

關鍵設計驅動因素：
- 安全性優先：所有密碼必須使用 bcrypt 雜湊，所有通訊必須透過 HTTPS
- 效能目標：登入 API 回應時間 < 500ms (P95)
- 可用性目標：99.9% uptime SLA
```

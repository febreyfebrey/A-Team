---
name: Jira Query
description: Provides JQL query patterns for the RDC JIRA project with custom field support
---

# Jira Query

## 目的

針對 RDC 專案（TutorABC Education）提供標準化的 JQL（JIRA Query Language）查詢模板。你必須使用本技能中定義的查詢模式來建構所有 JIRA 查詢。

**範圍限制：本技能僅適用於 RDC 專案。SDC 和 SRN 專案不在範圍內，你不得對這些專案執行任何查詢。**

## 專案環境

- 專案代碼：`RDC`
- 自訂欄位對照表：
  - `customfield_10092`：預估開發規模（Estimated Development Size）
  - `customfield_10237`：Rank Number
  - `customfield_10134`：專案完成日（Project Completion Date）
- 優先順序等級：`Highest` / `Medium-High` / `Medium` / `Medium-Low` / `Lowest`

## 工作流程狀態

### 需求工作流（Requirement Workflow）

草稿 → 需求評審 → 待規劃 → 規劃中 → 待設計 → 設計中 → 待排期 → 待開發 → 開發中 → 待測試 → 測試中 → 已關閉

### 開發工作流（Development Workflow）

待排期 → 待執行 → 執行中 → 驗收中 → 已關閉

## 基礎查詢模式

### 依優先順序查詢

```jql
project = RDC AND priority = "Highest" ORDER BY created ASC
```

```jql
project = RDC AND priority in ("Medium-High", "Medium") ORDER BY priority ASC, created ASC
```

### 依狀態查詢

```jql
project = RDC AND status = "開發中" ORDER BY priority ASC
```

```jql
project = RDC AND status in ("待測試", "測試中") ORDER BY priority ASC, updated DESC
```

### 依經辦人查詢

```jql
project = RDC AND assignee = "username" AND status != "已關閉" ORDER BY priority ASC
```

### 依日期範圍查詢

```jql
project = RDC AND created >= "2025-01-01" AND created <= "2025-03-31" ORDER BY created DESC
```

```jql
project = RDC AND updated >= startOfWeek() ORDER BY updated DESC
```

## 自訂欄位查詢模式

### 預估開發規模（customfield_10092）

```jql
project = RDC AND cf[10092] is not EMPTY ORDER BY cf[10092] DESC
```

```jql
project = RDC AND cf[10092] > 5 AND status != "已關閉" ORDER BY cf[10092] DESC
```

### Rank Number（customfield_10237）

```jql
project = RDC AND cf[10237] is not EMPTY ORDER BY cf[10237] ASC
```

```jql
project = RDC AND cf[10237] >= 201 AND cf[10237] < 301 ORDER BY cf[10237] ASC
```

### 專案完成日（customfield_10134）

```jql
project = RDC AND cf[10134] is not EMPTY ORDER BY cf[10134] ASC
```

## 停滯偵測查詢（Stagnation Detection）

### 需求 Highest 停滯超過 14 天

你必須使用此查詢偵測需求類型中 Highest 優先順序且狀態未變更超過 14 天的工單：

```jql
project = RDC AND priority = "Highest" AND issuetype = "需求" AND status not in ("已關閉") AND status changed before -14d ORDER BY updated ASC
```

### 開發 Highest 停滯超過 7 天

你必須使用此查詢偵測開發類型中 Highest 優先順序且狀態未變更超過 7 天的工單：

```jql
project = RDC AND priority = "Highest" AND issuetype = "開發" AND status not in ("已關閉") AND status changed before -7d ORDER BY updated ASC
```

### 通用停滯偵測（可調天數）

```jql
project = RDC AND status not in ("已關閉") AND status changed before -{N}d AND priority = "{PRIORITY}" ORDER BY updated ASC
```

將 `{N}` 替換為天數，`{PRIORITY}` 替換為優先順序等級。

## 逾期偵測查詢（Overdue Detection）

### 超過專案完成日的工單

你必須使用此查詢找出已超過專案完成日但尚未關閉的工單：

```jql
project = RDC AND cf[10134] < now() AND status != "已關閉" ORDER BY cf[10134] ASC
```

### 即將到期（7 天內）

```jql
project = RDC AND cf[10134] >= now() AND cf[10134] <= endOfDay("+7d") AND status != "已關閉" ORDER BY cf[10134] ASC
```

## 組合查詢模式

### 高優先順序且逾期

```jql
project = RDC AND priority in ("Highest", "Medium-High") AND cf[10134] < now() AND status != "已關閉" ORDER BY priority ASC, cf[10134] ASC
```

### 未排序的新工單

```jql
project = RDC AND cf[10237] is EMPTY AND status != "已關閉" ORDER BY priority ASC, created ASC
```

## 範例

### 輸入

> 查詢目標：找出所有 Highest 優先順序、目前正在開發中且已超過專案完成日的 RDC 工單。

### 輸出

**JQL 查詢：**

```jql
project = RDC AND priority = "Highest" AND status = "開發中" AND cf[10134] < now() ORDER BY cf[10134] ASC
```

**預期結果格式：**

| Key | 摘要 | 經辦人 | 狀態 | 優先順序 | 專案完成日 | 逾期天數 |
|-----|------|--------|------|----------|-----------|---------|
| RDC-1234 | 會員系統重構 | wang.ming | 開發中 | Highest | 2025-02-15 | 13 天 |
| RDC-1256 | 支付流程優化 | li.hua | 開發中 | Highest | 2025-02-20 | 8 天 |

### 輸入

> 查詢目標：找出需求 Highest 停滯超過 14 天、以及開發 Highest 停滯超過 7 天的工單。

### 輸出

**需求停滯 JQL：** `project = RDC AND priority = "Highest" AND issuetype = "需求" AND status not in ("已關閉") AND status changed before -14d ORDER BY updated ASC`

**開發停滯 JQL：** `project = RDC AND priority = "Highest" AND issuetype = "開發" AND status not in ("已關閉") AND status changed before -7d ORDER BY updated ASC`

**預期結果格式：**

| Key | 摘要 | 類型 | 狀態 | 最後更新日 | 停滯天數 |
|-----|------|------|------|-----------|---------|
| RDC-1100 | 課程排程引擎 | 需求 | 待設計 | 2025-02-01 | 27 天 |
| RDC-1305 | API Gateway 升級 | 開發 | 執行中 | 2025-02-21 | 7 天 |

## 限制

- 你不得查詢 RDC 以外的專案（SDC、SRN 等均不在範圍內）。
- 你不得在 JQL 中使用未列於本技能的自訂欄位代碼，除非使用者明確提供欄位 ID。
- 所有查詢結果必須包含表格格式化輸出，包含至少 Key、摘要、狀態、優先順序欄位。

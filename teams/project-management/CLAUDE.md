# Project Management Team

## 團隊目標

追蹤 RDC 專案（TutorABC Education）在 JIRA 上的執行進度，透過自動化排序、停滯偵測與定期報告，回答「現在進展如何」這個核心問題。本團隊僅管理 RDC 專案，SDC 與 SRN 不在自動追蹤範圍內。

## JIRA 環境

| 項目 | 值 |
|------|-----|
| 專案代碼 (Project Key) | RDC |
| 預估開發規模 (Estimated Dev Size) | `customfield_10092` |
| 排序序號 (Rank Number) | `customfield_10237` |
| 專案完成日 (Project Due Date) | `customfield_10134` |

### 優先級定義 Priority Levels

| 優先級 | 說明 |
|--------|------|
| Highest | 最高優先，排序不可變動 |
| Medium-High | 中高優先，RankNumber 從 201 起編 |
| Medium | 中等優先，RankNumber 從 301 起編 |
| Medium-Low | 中低優先，RankNumber 從 401 起編 |
| Lowest | 最低優先，RankNumber 從 501 起編 |

### 工作流狀態定義 Workflow Status Definitions

**需求票 (Requirement Ticket)：**

```
草稿 → 需求評審 → 待規劃 → 規劃中 → 待設計 → 設計中 → 待排期 → 待開發 → 開發中 → 待測試 → 測試中 → 已關閉
```

**開發票 (Development Ticket)：**

```
待排期 → 待執行 → 執行中 → 驗收中 → 已關閉
```

## 停滯規則 Stagnation Rules

| 票券類型 | 適用優先級 | 停滯狀態 | 門檻天數 |
|----------|-----------|----------|----------|
| 需求票 | Highest 限定 | 待規劃、待設計、待排期、待開發、待測試 | 14 天 |
| 開發票 | Highest 限定 | 待排期、待執行 | 7 天 |
| 所有票券 | 所有優先級 | 任何狀態 | 超過 customfield_10134（專案完成日）即為逾期 |

## 部署模式

本團隊使用 **Subagent 模式**運作。Coordinator 透過 Task tool 調度所有執行角色，統一管理週報與月報的產出流程。所有 JIRA 操作由 Coordinator 指派 Backlog Manager 執行，其他角色不得直接操作 JIRA。

## 通用行為規範

### 撰寫語言

- 所有報告與文件以繁體中文為主要語言
- 技術名詞中英並列（如：優先級 Priority、停滯 Stagnation、排序序號 Rank Number）
- JIRA 欄位名稱保留英文原名並附中文說明

### 報告格式

- 所有報告同時產出 HTML 格式（線上檢視用）與 PDF 格式（email 給主管用）
- HTML 報告使用內嵌 CSS，確保無外部依賴即可正確顯示
- PDF 報告使用 A4 版面，包含頁首（報告標題、日期）與頁尾（頁碼）

### 資料來源

- 所有數據必須透過 Atlassian MCP 工具即時查詢 JIRA，不得使用快取或假設數據
- 每次查詢必須限定專案 = RDC，不得查詢其他專案

## JIRA MCP 整合

本團隊透過 Atlassian 官方 MCP Server 存取 Jira Cloud。配置檔為專案根目錄的 `.mcp.json`。

### 首次使用

首次啟動 session 時，MCP 會自動導向瀏覽器完成 Atlassian OAuth 認證。認證完成後即可使用所有 JIRA 工具。

### 可用操作

透過 MCP 可執行以下 JIRA 操作：

| 操作 | 說明 | 使用者 |
|------|------|--------|
| JQL 查詢 | 搜尋與篩選 RDC 專案工單 | Ticket Analyst、Development Tracker |
| 讀取工單詳情 | 取得工單欄位、狀態、歷史記錄 | 全部分析角色 |
| 更新欄位 | 更新 RankNumber (`customfield_10237`) | Backlog Manager |
| 讀取變更日誌 | 取得狀態轉換歷史以計算停滯天數 | Development Tracker |

### 使用限制

- 所有 JIRA 寫入操作必須由 Coordinator 指派 Backlog Manager 執行
- 分析角色（Ticket Analyst、Development Tracker）僅限讀取操作
- 每次 MCP 呼叫必須包含 `project = RDC` 篩選條件

## 跨團隊協作流程

本團隊在組織工作流中的定位：

```
product-management（決策）→ project-management（排序 + 追蹤）→ business-analysis（定義）→ engineering（建造）
```

- 從 product-management 接收優先級決策與新需求指示
- 在 JIRA 中執行排序與追蹤，產出進度報告
- 將排序結果與進度資訊提供給 business-analysis 與 engineering 參考

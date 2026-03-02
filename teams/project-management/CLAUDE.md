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

- 所有數據必須來自 JIRA API 即時查詢，不得使用快取或假設數據
- 每次查詢必須限定專案 = RDC，不得查詢其他專案

## 跨團隊協作流程

本團隊在組織工作流中的定位：

```
product-management（決策）→ project-management（排序 + 追蹤）→ requirements-docs（定義）→ engineering（建造）
```

- 從 product-management 接收優先級決策與新需求指示
- 在 JIRA 中執行排序與追蹤，產出進度報告
- 將排序結果與進度資訊提供給 requirements-docs 與 engineering 參考

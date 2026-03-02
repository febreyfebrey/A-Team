# Requirements Documentation Team

## 團隊目標

將客戶/PM 的模糊需求構想，透過結構化訪談與逐步確認流程，轉化為完整的開發文件集（PRD、SA、SD、Test Cases）。每階段文件經品質審查後提交 PM 確認，確認後才進入下一階段，並自動同步至 JIRA。

## 適用領域

- Web 應用開發
- API / 後端服務開發

## 部署模式

本團隊使用 **Subagent 模式**運作。Coordinator 透過 Task tool 調度所有執行角色，管理嚴格的線性工作流程。每個階段必須依序完成，不可跳過或並行。

未來計畫整合至 Web App，届時透過預留的整合介面 API 與外部系統對接。

## 通用行為規範

### 撰寫語言

- 所有文件以繁體中文撰寫
- 技術名詞中英並列（如：資料庫 Database、應用程式介面 API）

### 寫作風格

- 用字簡潔，講重點，易讀易懂
- 避免冗長描述與重複內容
- 使用短句，每段落聚焦一個概念

### 文件格式

- 所有文件使用 Confluence 頁面格式
- 包含目錄、表格、資訊面板等 Confluence 元素
- 每份文件開頭包含文件資訊區塊（版本、作者、日期、狀態）

## 工作流程

每階段遵循以下流動：

```
執行者產出文件 → Document Reviewer 品質審查 → 修正（如需要）→ Coordinator 提交 PM → PM 確認 → 下一階段
```

### 階段定義

| 階段 | 執行者 | 產出物 | PM 確認 | JIRA 動作 |
|------|--------|--------|---------|-----------|
| ⓪ 專案背景準備 | Knowledge Curator | 專案背景包（Glossary + 跨專案參照） | — | — |
| ① 訪談 | Requirements Analyst | 訪談紀錄 | — | — |
| ② 需求摘要 | Requirements Analyst | 文字摘要 | 是 | — |
| ③ 需求草稿 | Requirements Analyst | 基本需求草稿 | 是 | — |
| ④ 完整 PRD | Requirements Analyst | PRD（含 UI/UX） | 是 | 建立 Epic + Stories |
| ⑤ 系統分析 | System Architect | SA 文件 | 是 | 更新 Story 描述 |
| ⑥ 系統設計 | System Architect | SD / 架構文件 | 是 | 建立 Sub-tasks |
| ⑦ 測試案例 | Test Case Designer | Test Cases | 是 | 建立測試 Sub-tasks |
| ⑧ 流程回顧 | Process Reviewer | 回顧報告 | — | — |

## Web App 整合介面（預留）

| 介面 | 用途 | 方向 |
|------|------|------|
| 對話介面 API | 需求方與 Requirements Analyst 互動訪談 | 雙向 |
| 文件提交 API | 各階段文件提交給 PM 檢視 | 團隊 → Web App |
| 確認回饋 API | PM 確認 / 退回 / 附註意見 | Web App → 團隊 |
| 進度狀態 API | 當前階段與等待確認狀態查詢 | 團隊 → Web App |

## 公司專案目錄結構

所有專案文件依以下結構存放：

```
projects/
├── {company-name}/
│   ├── glossary.md                    ← 公司級 Glossary（Knowledge Curator 維護）
│   ├── {project-a}/
│   │   ├── prd.md
│   │   ├── sa.md
│   │   ├── sd.md
│   │   └── test-cases.md
│   └── {project-b}/
│       └── ...
```

- 每間公司一個資料夾，每個專案一個子資料夾
- `glossary.md` 放在公司根目錄，所有專案共用
- 所有文件中的術語必須與 glossary.md 中的偏好用法一致

## JIRA 整合

| 觸發時機 | 操作 | 欄位 |
|----------|------|------|
| PRD 確認後 | 建立 Epic + Stories | Summary, Description, Priority, Labels |
| SA 確認後 | 更新 Story Description | 補充系統分析資訊 |
| SD 確認後 | 建立 Sub-tasks | Summary, Description, Priority, Labels |
| Test Cases 確認後 | 建立測試 Sub-tasks | Summary, Description, Priority, Labels |

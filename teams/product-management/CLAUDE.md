# Product Management Team

## 團隊目標

決定「要做什麼」。本團隊負責 TutorABC 三條產品線的產品策略、競爭情報、路線圖管理與需求准入決策：

- **教育產品 (RDC)**：AI 學習鏈驅動的線上教育平台
- **電商產品 (SDC)**：Shop 電商系統
- **軟體發佈 (SRN)**：Software Release 管理系統

本團隊的產出是「做什麼」的決策與優先順序，不涉及「怎麼做」的實作細節。

## 跨團隊整合

本團隊處於產品開發流程 (Product Development Pipeline) 的最上游：

```
product-management (決策) → project-management (排程與追蹤) → requirements-docs (定義) → engineering (實作)
```

### 向下游傳遞的產出物

| 產出物 | 接收團隊 | 用途 |
|--------|----------|------|
| 產品路線圖 (Product Roadmap) | project-management | 排定專案優先順序與時程 |
| 需求准入決策 (Demand Intake Decision) | project-management | 確認哪些需求進入開發排程 |
| 競爭情報報告 (Competitive Intelligence Report) | project-management, requirements-docs | 提供市場背景與功能對標資訊 |
| 產品框架定義 (Product Framework) | requirements-docs | 作為撰寫 PRD 的產品架構參考 |

### 從上游接收的輸入

| 輸入 | 來源 | 用途 |
|------|------|------|
| 老闆週會指示 (Boss's Weekly Direction) | 使用者手動輸入 | Phase 2 策略整合的核心輸入 |
| 跨部門需求 (Cross-Department Requests) | 各部門 | Phase 3 需求准入的評估對象 |
| 客戶回饋 (Customer Feedback) | 客服、業務團隊 | 需求優先順序的參考依據 |

## 部署模式

本團隊使用 **Subagent 模式**運作。Coordinator 透過 Task tool 調度所有執行角色，管理工作流程階段轉換。每個階段的任務由 Coordinator 指派給對應的執行者，執行者完成後回報 Coordinator。

本團隊共 3 個 agent（含 Coordinator），屬於小型團隊。Coordinator 兼任流程回顧 (Process Review) 職責。

## 通用行為規範

### 撰寫語言

- 所有文件與報告以繁體中文撰寫
- 技術名詞與產品術語中英並列，首次出現時標註英文（如：路線圖 Roadmap、需求准入 Demand Intake）
- 後續出現可使用中文或英文，同一份文件內保持一致

### 寫作風格

- 使用祈使句為主要語氣
- 用字簡潔，講重點，避免冗長描述
- 每段落聚焦一個概念，不超過 5 句
- 禁止使用模糊詞彙：「大概」「差不多」「應該可以」「可能需要」「等等」（未列舉完整項目時）

### 輸出格式

所有報告與文件必須支援雙格式輸出：

| 格式 | 用途 | 說明 |
|------|------|------|
| HTML | 線上瀏覽 | 供團隊成員即時查閱，支援互動元素 |
| PDF | 郵件寄送 | 供主管審閱，格式固定、適合列印 |

每份文件必須包含文件資訊區塊：

```
| 欄位 | 內容 |
|------|------|
| 文件標題 | {標題} |
| 版本 | {版本號} |
| 作者 | {agent 名稱} |
| 日期 | {YYYY-MM-DD} |
| 狀態 | 草稿 / 審查中 / 已確認 |
```

## 工作流程概覽

```
Phase 0: 產品框架定義 (Product Framework Definition)
  ├── 一次性執行
  ├── Product Strategist 與使用者討論
  └── 定義三條產品線的產品架構

Phase 1: 競爭情報 (Competitive Intelligence)
  ├── 每週執行
  ├── Market Analyst 收集中國、美國、海外市場情報
  └── 產出結構化競爭分析報告

Phase 2: 策略整合 (Strategic Integration)
  ├── 每週會議後執行
  ├── 使用者輸入老闆週會指示
  └── Product Strategist 更新產品路線圖

Phase 3: 需求准入 (Demand Intake)
  ├── 持續執行
  ├── Product Strategist 評估外部需求
  └── 產出准入 / 駁回決策與理由
```

## 產品領域知識

### AI 學習鏈 (AI Learning Chain)

教育產品 (RDC) 的核心框架，定義學生的完整學習旅程：

```
定級評估 → 課前預習小遊戲 → 課中深度互動學習 → 課後學習評估報告 → AI 深度複習 → 每日任務遊戲 → 延伸課外讀物或 Podcast
```

所有教育產品的功能規劃必須對齊此學習鏈的某一個或多個環節。

### 產品線代號

| 代號 | 全名 | 領域 |
|------|------|------|
| RDC | Education | 線上教育平台 |
| SDC | Shop | 電商系統 |
| SRN | Software Release | 軟體發佈管理 |

---
name: Decision Traceability
description: Every product decision must record rationale, data source, and date traceable to a specific meeting or report
---

# 決策可追溯性 (Decision Traceability)

## 適用範圍 (Applicability)

- 適用於：所有產品管理團隊成員 (All product management team members)

## 規則內容 (Rule Content)

### 必須記錄的決策類型 (Decision Types Requiring Documentation)

以下三類決策必須完整記錄追溯資訊：

1. **功能優先排序 (Feature Prioritization)** — 任何功能的優先等級變更
2. **路線圖變更 (Roadmap Changes)** — 路線圖中任何項目的新增、移除、延期或提前
3. **需求收納決策 (Demand Intake Decisions)** — 每一筆需求的接受、延後或拒絕

### 每筆決策必須記錄的欄位 (Required Fields)

你必須為每筆決策記錄以下三個欄位，缺一不可：

| 欄位 (Field) | 說明 (Description) | 格式 (Format) |
|---|---|---|
| 決策理由 (Rationale) | 做出此決策的具體原因 | 至少一句完整說明，禁止僅寫「經討論決定」 |
| 數據來源 (Data Source) | 支撐此決策的具體數據或資訊來源 | 必須為以下之一：會議紀錄（含日期與會議名稱）、數據報告（含報告名稱與日期）、主管書面指示（含指示者與日期） |
| 決策日期 (Decision Date) | 做出決策的日期 | YYYY-MM-DD 格式 |

### 數據來源的具體性要求 (Data Source Specificity)

你不得使用以下模糊來源描述：
- 「根據團隊共識」（必須改為「根據 2025-01-15 產品週會會議紀錄」）
- 「根據市場趨勢」（必須改為「根據 2025-01 IDC 全球教育科技市場報告」）
- 「根據用戶反饋」（必須改為「根據 2024-Q4 用戶滿意度調查報告，樣本數 N=1,200」）
- 「老闆說的」（必須改為「根據產品副總 2025-01-10 email 指示」）

### 決策變更的追溯鏈 (Decision Change Trail)

當一筆決策被修改時，你必須保留原始決策紀錄並新增變更紀錄。禁止覆蓋或刪除原始決策。變更紀錄必須包含：
- 原始決策 (Original Decision) 與日期
- 變更後決策 (Updated Decision) 與日期
- 變更原因 (Change Reason) 與新的數據來源

## 違規判定 (Violation Determination)

- 決策紀錄缺少決策理由、數據來源、決策日期三個欄位中的任一個 → 違規
- 數據來源使用模糊描述而非具體的會議、報告或指示 → 違規
- 決策變更時覆蓋原始紀錄而非新增變更紀錄 → 違規

### 違規情境範例 (Violation Scenario)

某產品經理在路線圖中將「AI 深度複習功能」從 Q1 延期至 Q2，紀錄如下：

```
變更項目：AI 深度複習功能延期
變更內容：Q1 → Q2
決策理由：工程資源不足
```

此紀錄構成違規，原因如下：
1. **數據來源缺失** — 未記錄「工程資源不足」的判斷依據（應引用工程團隊的產能報告或會議紀錄）
2. **決策日期缺失** — 未記錄做出延期決策的具體日期
3. **原始決策未保留** — 未記錄原始排入 Q1 的決策及其理由

正確紀錄方式：

```
變更項目 (Change Item)：AI 深度複習功能延期
原始決策 (Original Decision)：排入 2025-Q1，決策日期 2024-12-01，
  依據 2024-11-28 產品策略會議紀錄
變更後決策 (Updated Decision)：延期至 2025-Q2
決策日期 (Decision Date)：2025-01-20
決策理由 (Rationale)：AI 平台團隊 Q1 產能已被定級評估模組佔滿，
  無法同時支撐兩個 AI 專案平行開發
數據來源 (Data Source)：2025-01-18 工程產能週報、
  2025-01-19 產品與工程協調會議紀錄
```

## 例外 (Exceptions)

本規則無例外。

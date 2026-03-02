---
name: Requirement List
description: Generate priority-sorted requirement ticket lists with status, stagnation markers, and overdue flags
---

# Requirement List

## 目的

產生依優先級（Priority）與排序編號（Rank Number）排列的需求工單清單，標註停滯（Stagnation）與逾期（Overdue）狀態。

## 適用範圍

僅適用於 JIRA RDC 專案的需求工單。不得包含其他專案的工單。

## 排序規則

1. 第一層排序：依優先級分組，順序為 Highest → Medium-High → Medium → Medium-Low → Lowest
2. 第二層排序：每個優先級群組內，依 `customfield_10237`（Rank Number）升序排列
3. 無 Rank Number 的工單排在該優先級群組的最後

## 顯示欄位

清單必須包含以下欄位，順序固定不可調換：

| 欄位順序 | 中文欄位名 | English Column Name | 資料來源 |
|----------|-----------|---------------------|---------|
| 1 | 排序編號 | Rank Number | `customfield_10237` |
| 2 | 工單編號 | Ticket Key | JIRA key |
| 3 | 摘要 | Summary | JIRA summary |
| 4 | 狀態 | Status | JIRA status |
| 5 | 負責人 | Assignee | JIRA assignee |
| 6 | 當前狀態停留天數 | Days in Status | 當前日期減去最後狀態轉換日期 |
| 7 | 停滯標記 | Stagnation Flag | 依停滯規則判定 |
| 8 | 逾期標記 | Overdue Flag | 依逾期規則判定 |

## 標記規則

### 停滯標記（Stagnation Flag）

僅適用於優先級為 Highest 的工單：

- 需求工單在以下狀態停留超過 14 天時標記 🔴：「待規劃」、「待設計」、「待排期」、「待開發」、「待測試」
- 非 Highest 優先級的工單不標記停滯
- 「活躍中」狀態（規劃中、設計中、開發中、測試中）不計入停滯

### 逾期標記（Overdue Flag）

適用於所有優先級：

- 當前日期超過 `customfield_10134`（專案完成日）且工單狀態不為「已關閉」時，標記 ⚠️
- `customfield_10134` 為空的工單不標記逾期

### 複合標記

一張工單可以同時擁有停滯標記 🔴 與逾期標記 ⚠️。

## 輸入 / 輸出範例（Example）

### 輸入（Input）：工單資料

```json
{
  "report_date": "2025-03-10",
  "tickets": [
    {
      "key": "RDC-1001",
      "summary": "會員系統重構",
      "priority": "Highest",
      "status": "待開發",
      "assignee": "王大明",
      "customfield_10237": 1,
      "customfield_10134": "2025-03-01",
      "last_transition_date": "2025-02-10"
    },
    {
      "key": "RDC-1002",
      "summary": "課程推薦優化",
      "priority": "Highest",
      "status": "設計中",
      "assignee": "李小華",
      "customfield_10237": 2,
      "customfield_10134": "2025-04-15",
      "last_transition_date": "2025-03-05"
    },
    {
      "key": "RDC-1003",
      "summary": "報表匯出功能",
      "priority": "Medium",
      "status": "待規劃",
      "assignee": "張三",
      "customfield_10237": 5,
      "customfield_10134": "2025-03-08",
      "last_transition_date": "2025-02-25"
    }
  ]
}
```

### 輸出（Output）：格式化清單

```
=== Highest ===

| 排序編號 / Rank | 工單編號 / Key | 摘要 / Summary | 狀態 / Status | 負責人 / Assignee | 停留天數 / Days | 停滯 / Stag. | 逾期 / Over. |
|----------------|---------------|---------------|--------------|------------------|----------------|-------------|-------------|
| 1              | RDC-1001      | 會員系統重構    | 待開發        | 王大明            | 28             | 🔴          | ⚠️          |
| 2              | RDC-1002      | 課程推薦優化    | 設計中        | 李小華            | 5              |             |             |

=== Medium ===

| 排序編號 / Rank | 工單編號 / Key | 摘要 / Summary | 狀態 / Status | 負責人 / Assignee | 停留天數 / Days | 停滯 / Stag. | 逾期 / Over. |
|----------------|---------------|---------------|--------------|------------------|----------------|-------------|-------------|
| 5              | RDC-1003      | 報表匯出功能    | 待規劃        | 張三              | 13             |             | ⚠️          |
```

### 輸出說明

- RDC-1001：Highest 優先級，在「待開發」停留 28 天（超過 14 天門檻），標記 🔴；已超過專案完成日 2025-03-01，標記 ⚠️
- RDC-1002：Highest 優先級，在「設計中」停留 5 天，設計中為活躍狀態不計停滯；未超過專案完成日，不標記
- RDC-1003：Medium 優先級，不適用停滯規則（僅 Highest）；已超過專案完成日 2025-03-08，標記 ⚠️

## 注意事項

- 每個優先級群組必須有群組標題，格式為 `=== {Priority} ===`。
- 表頭必須中英雙語。
- 停留天數以自然天計算，不排除假日。
- 優先級群組內無工單時，不顯示該群組。

---
name: Development Metrics
description: Calculate development lifecycle metrics including cycle time, completion rate, overdue rate, and stagnation count
---

# Development Metrics

## 目的

計算並彙整 RDC 專案的開發生命週期指標（Development Lifecycle Metrics），涵蓋狀態週期時間（Cycle Time）、完成率（Completion Rate）、逾期率（Overdue Rate）與停滯計數（Stagnation Count）。

## 適用範圍

僅適用於 JIRA RDC 專案。不得將其他專案的工單納入計算。

## 指標定義

### 1. 狀態週期時間（Cycle Time per Status）

針對每張工單，計算其在各狀態中停留的天數。

- **需求工作流（Requirement Workflow）**：草稿 → 需求評審 → 待規劃 → 規劃中 → 待設計 → 設計中 → 待排期 → 待開發 → 開發中 → 待測試 → 測試中 → 已關閉
- **開發工作流（Development Workflow）**：待排期 → 待執行 → 執行中 → 驗收中 → 已關閉

計算方式：取每次狀態轉換記錄（Status Transition），以「進入該狀態的時間」至「離開該狀態的時間」計算天數。若工單仍在該狀態中，以當前日期計算。

### 2. 完成率（Completion Rate）

```
完成率 = 已關閉工單數 / 總工單數 × 100%
```

依優先級（Priority）分組計算：Highest、Medium-High、Medium、Medium-Low、Lowest。

### 3. 逾期率（Overdue Rate）

```
逾期率 = 逾期工單數 / 總工單數 × 100%
```

逾期判定：當前日期超過 `customfield_10134`（專案完成日）且工單狀態不為「已關閉」。

### 4. 停滯計數（Stagnation Count）

僅統計優先級為 Highest 的工單：

- **需求工單**：在「待規劃」、「待設計」、「待排期」、「待開發」、「待測試」任一狀態停留超過 14 天
- **開發工單**：在「待排期」、「待執行」任一狀態停留超過 7 天

計算方式：當前日期減去最後一次狀態轉換日期（Last Transition Date）。

## 狀態轉換追蹤（Status Transition Tracking）

你必須從 JIRA 的變更歷史（Changelog）中提取每張工單的狀態轉換記錄，記錄以下欄位：

| 欄位 | 說明 |
|------|------|
| ticket_key | 工單編號（Ticket Key） |
| from_status | 轉換前狀態 |
| to_status | 轉換後狀態 |
| transition_date | 轉換日期（Transition Date） |
| days_in_status | 在前一狀態停留的天數 |

## 輸入 / 輸出範例（Example）

### 輸入（Input）：工單狀態歷史

```json
{
  "tickets": [
    {
      "key": "RDC-1234",
      "type": "requirement",
      "priority": "Highest",
      "customfield_10134": "2025-03-15",
      "current_status": "待開發",
      "transitions": [
        {"from": "草稿", "to": "需求評審", "date": "2025-01-10"},
        {"from": "需求評審", "to": "待規劃", "date": "2025-01-15"},
        {"from": "待規劃", "to": "規劃中", "date": "2025-01-20"},
        {"from": "規劃中", "to": "待設計", "date": "2025-01-25"},
        {"from": "待設計", "to": "設計中", "date": "2025-02-01"},
        {"from": "設計中", "to": "待排期", "date": "2025-02-10"},
        {"from": "待排期", "to": "待開發", "date": "2025-02-15"}
      ]
    },
    {
      "key": "RDC-1235",
      "type": "development",
      "priority": "Highest",
      "customfield_10134": "2025-04-01",
      "current_status": "待執行",
      "transitions": [
        {"from": "待排期", "to": "待執行", "date": "2025-02-20"}
      ]
    }
  ],
  "report_date": "2025-03-10"
}
```

### 輸出（Output）：指標摘要

```json
{
  "report_date": "2025-03-10",
  "project": "RDC",
  "cycle_time_summary": {
    "RDC-1234": {
      "草稿": 5,
      "需求評審": 5,
      "待規劃": 5,
      "規劃中": 5,
      "待設計": 7,
      "設計中": 9,
      "待排期": 5,
      "待開發": 23,
      "total_days": 59
    },
    "RDC-1235": {
      "待排期": "N/A (未記錄進入時間)",
      "待執行": 18,
      "total_days": 18
    }
  },
  "completion_rate": {
    "overall": "0.0%",
    "by_priority": {
      "Highest": "0.0%"
    }
  },
  "overdue_rate": {
    "overall": "50.0%",
    "overdue_tickets": ["RDC-1234"]
  },
  "stagnation": {
    "count": 2,
    "details": [
      {
        "key": "RDC-1234",
        "type": "requirement",
        "status": "待開發",
        "days_in_status": 23,
        "threshold": 14,
        "flag": "🔴"
      },
      {
        "key": "RDC-1235",
        "type": "development",
        "status": "待執行",
        "days_in_status": 18,
        "threshold": 7,
        "flag": "🔴"
      }
    ]
  }
}
```

## 注意事項

- 所有計算僅限 RDC 專案，不得混入其他專案資料。
- 狀態週期時間以自然天（Calendar Days）計算，不排除假日。
- 停滯計數僅針對 Highest 優先級，其他優先級不列入停滯統計。
- 逾期判定適用於所有優先級。

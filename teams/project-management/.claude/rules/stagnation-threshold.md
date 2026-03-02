---
name: Stagnation Threshold
description: Define stagnation and overdue detection thresholds for Highest priority tickets
---

# Stagnation Threshold

## 規則內容

### 停滯適用範圍

停滯（Stagnation）檢測僅適用於優先級為 Highest 的工單。其他優先級（Medium-High、Medium、Medium-Low、Lowest）的工單不進行停滯檢測。

### 停滯門檻

**需求工單（Requirement）— Highest 優先級**：

在以下等待中狀態停留超過 **14 天**即為停滯：
- 待規劃、待設計、待排期、待開發、待測試

**開發工單（Development）— Highest 優先級**：

在以下等待中狀態停留超過 **7 天**即為停滯：
- 待排期、待執行

### 不適用停滯的狀態

活躍中（Active）狀態不計入停滯，無論停留多久：
- 需求：草稿、需求評審、規劃中、設計中、開發中、測試中
- 開發：執行中、驗收中

已關閉（Closed）狀態不計入停滯。

### 停滯天數計算

```
停滯天數 = 當前日期 - 最後一次狀態轉換日期（Last Transition Date）
```

以自然天計算，不排除週末與假日。最後一次狀態轉換日期取自 JIRA 變更歷史（Changelog）中最近一筆狀態變更記錄。

### 逾期規則（Overdue）

逾期檢測適用於所有優先級，不限於 Highest：

```
逾期 = 當前日期 > customfield_10134（專案完成日）且狀態 ≠ 已關閉
```

- `customfield_10134` 為空的工單不判定為逾期。
- 已關閉的工單不判定為逾期，即使關閉日期晚於專案完成日。

### 停滯與逾期可並存

一張工單可以同時被判定為停滯與逾期。兩種標記互相獨立，不互斥。

## 違規判定（Violation Determination）

- 對非 Highest 優先級的工單進行停滯標記 → 違規
- 對活躍中狀態（如「開發中」、「執行中」）進行停滯判定 → 違規
- 使用工作天而非自然天計算停滯天數 → 違規
- 需求工單使用 7 天門檻或開發工單使用 14 天門檻 → 違規
- `customfield_10134` 為空時將工單標記為逾期 → 違規

### 違規情境

將一張 Medium 優先級的需求工單在「待開發」狀態停留 20 天後標記為停滯 🔴。此判定違規，因為停滯檢測僅適用於 Highest 優先級。Medium 優先級的工單無論在等待中狀態停留多久，均不觸發停滯標記。

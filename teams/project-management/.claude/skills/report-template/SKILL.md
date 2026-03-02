---
name: Report Template
description: Generate bilingual HTML/PDF report templates for weekly and monthly project management reports
---

# Report Template

## 目的

產生 RDC 專案的週報（Weekly Report）與月報（Monthly Report）範本，支援 HTML（線上檢視）與 PDF（Email 寄送）兩種格式。所有報告內容以中文為主、英文為輔。

## 適用範圍

僅適用於 JIRA RDC 專案。報告中不得包含其他專案的資料。

## 週報結構（Weekly Report Structure）

週報必須包含以下五個章節，順序固定不可調換：

### 1. 執行摘要（Executive Summary）

- 本週關鍵數據總覽：工單總數、已關閉數、新建數、逾期數、停滯數
- 以一段文字摘要本週最重要的進展與風險

### 2. 優先級排序（Priority Ranking）

- 依優先級分組（Highest → Lowest），每組內依 `customfield_10237`（Rank Number）排序
- 顯示欄位：Rank Number、工單編號（Ticket Key）、摘要（Summary）、狀態（Status）、負責人（Assignee）

### 3. 停滯警示（Stagnation Alerts）

- 列出所有 Highest 優先級且觸發停滯門檻的工單
- 顯示欄位：工單編號、摘要、當前狀態、停留天數、門檻天數
- 使用 🔴 標記停滯工單

### 4. 開發進度（Development Progress）

- 依開發工作流狀態分組統計工單數量
- 顯示各狀態的工單清單與負責人

### 5. 工單建立統計（Ticket Creation Stats）

- 本週新建工單數量，依類型（需求 / 開發）與優先級分組
- 與上週比較，標示增減趨勢

## 月報結構（Monthly Report Structure）

月報包含週報的全部五個章節，另加以下兩個章節：

### 6. 趨勢分析（Trend Analysis）

- 過去四週的完成率、逾期率、停滯數量折線圖資料
- 各優先級的工單狀態分佈變化

### 7. 月度比較（Month-over-Month Comparison）

- 與上月同期比較：完成率變化、逾期率變化、平均週期時間變化
- 標示改善或惡化方向（↑ 改善 / ↓ 惡化）

## 格式要求

### HTML 格式

- 使用內聯樣式（Inline CSS），不依賴外部樣式表
- 表格使用 `border-collapse: collapse`，欄位有明確邊框
- 標題層級：報告標題 `<h1>`、章節標題 `<h2>`、子章節 `<h3>`
- 所有標題與表頭必須中英雙語

### PDF 格式

- 與 HTML 結構相同，確保列印時分頁合理
- 每個章節起始於新頁面頂部
- 頁首包含報告名稱與日期，頁尾包含頁碼

### 雙語規範

- 標題格式：`中文標題（English Title）`
- 表頭格式：`中文欄位名 / English Column Name`
- 首次出現的技術術語在中文後加括號標註英文

## 輸入 / 輸出範例（Example）

### 輸入（Input）：報告資料

```json
{
  "report_type": "weekly",
  "report_date": "2025-03-10",
  "period": "2025-03-03 ~ 2025-03-09",
  "summary": {
    "total_tickets": 45,
    "closed": 8,
    "new_created": 5,
    "overdue": 3,
    "stagnation": 2
  },
  "stagnation_alerts": [
    {
      "key": "RDC-1234",
      "summary": "會員系統重構",
      "status": "待開發",
      "days_in_status": 23,
      "threshold": 14
    }
  ]
}
```

### 輸出（Output）：HTML 結構片段

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <title>RDC 專案週報（Weekly Report）- 2025-03-10</title>
</head>
<body style="font-family: 'Microsoft JhengHei', Arial, sans-serif; max-width: 960px; margin: 0 auto; padding: 20px;">

  <h1 style="border-bottom: 2px solid #333; padding-bottom: 10px;">
    RDC 專案週報（Weekly Report）
  </h1>
  <p>報告日期（Report Date）：2025-03-10</p>
  <p>報告期間（Report Period）：2025-03-03 ~ 2025-03-09</p>

  <h2>1. 執行摘要（Executive Summary）</h2>
  <table style="border-collapse: collapse; width: 100%;">
    <tr>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        指標 / Metric
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        數值 / Value
      </th>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">工單總數（Total Tickets）</td>
      <td style="border: 1px solid #ccc; padding: 8px;">45</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">已關閉（Closed）</td>
      <td style="border: 1px solid #ccc; padding: 8px;">8</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">新建（New Created）</td>
      <td style="border: 1px solid #ccc; padding: 8px;">5</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">逾期（Overdue）</td>
      <td style="border: 1px solid #ccc; padding: 8px;">3</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">停滯（Stagnation）</td>
      <td style="border: 1px solid #ccc; padding: 8px;">2</td>
    </tr>
  </table>

  <h2>3. 停滯警示（Stagnation Alerts）</h2>
  <table style="border-collapse: collapse; width: 100%;">
    <tr>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        標記 / Flag
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        工單編號 / Ticket Key
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        摘要 / Summary
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        狀態 / Status
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        停留天數 / Days
      </th>
      <th style="border: 1px solid #ccc; padding: 8px; background: #f5f5f5;">
        門檻 / Threshold
      </th>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">🔴</td>
      <td style="border: 1px solid #ccc; padding: 8px;">RDC-1234</td>
      <td style="border: 1px solid #ccc; padding: 8px;">會員系統重構</td>
      <td style="border: 1px solid #ccc; padding: 8px;">待開發</td>
      <td style="border: 1px solid #ccc; padding: 8px;">23</td>
      <td style="border: 1px solid #ccc; padding: 8px;">14</td>
    </tr>
  </table>

</body>
</html>
```

## 注意事項

- 所有報告僅包含 RDC 專案資料，不得混入其他專案。
- HTML 格式必須使用內聯樣式，不得引用外部 CSS 檔案。
- 所有標題與表頭必須中英雙語，格式為「中文（English）」。
- 月報必須包含週報的全部內容，再加上趨勢分析與月度比較。

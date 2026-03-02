---
name: Bilingual Output
description: Enforce Chinese-primary English-secondary bilingual format for all report output
---

# Bilingual Output

## 規則內容

### 主要語言

所有報告輸出必須以中文為主要語言，英文為輔助語言。

### 技術術語標註

首次出現的技術術語，你必須在中文後以括號標註英文。後續出現同一術語時，僅使用中文即可。

格式：`中文術語（English Term）`

範例：
- 正確：「停滯（Stagnation）門檻為 14 天」
- 正確（後續使用）：「停滯門檻為 14 天」
- 違規：「Stagnation 門檻為 14 天」

### 報告標題

所有報告標題必須中英雙語並列。

格式：`中文標題（English Title）`

範例：
- 正確：`執行摘要（Executive Summary）`
- 違規：`Executive Summary`
- 違規：`執行摘要`

### 表頭

所有表格的表頭必須中英雙語。

格式：`中文欄位名 / English Column Name`

範例：
- 正確：`工單編號 / Ticket Key`
- 違規：`Ticket Key`
- 違規：`工單編號`

### 內文

報告內文以中文撰寫。JIRA 欄位值（如工單編號、狀態名稱）保持原始語言不翻譯。

## 違規判定（Violation Determination）

- 報告標題僅有英文而無中文 → 違規
- 報告標題僅有中文而無英文 → 違規
- 表頭僅使用單一語言 → 違規
- 技術術語首次出現時未標註英文 → 違規
- 內文主體以英文撰寫而非中文 → 違規

### 違規情境

產生一份週報，其中章節標題為「Executive Summary」而非「執行摘要（Executive Summary）」，且表格欄位標題為「Ticket Key」而非「工單編號 / Ticket Key」。此報告違反雙語輸出規則，因為標題與表頭均未包含中文。

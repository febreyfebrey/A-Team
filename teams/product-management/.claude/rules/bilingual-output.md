---
name: Bilingual Output
description: All output must use Chinese as primary language with English terms in parentheses on first use
---

# 雙語輸出規範 (Bilingual Output)

## 適用範圍 (Applicability)

- 適用於：所有產品管理團隊成員 (All product management team members)

## 規則內容 (Rule Content)

### 主語言與輔助語言 (Primary and Secondary Language)

所有輸出文件必須以繁體中文 (Traditional Chinese) 為主語言。英文 (English) 作為輔助語言，用於標註專業術語。

### 專業術語標註規則 (Technical Term Annotation)

你必須在每個專業術語首次出現時，以括號附上英文原文。同一文件中，同一術語僅需在首次出現時標註英文。

格式：`中文術語 (English Term)`

正確範例：
- 產品路線圖 (Roadmap) 必須涵蓋三個時間維度
- 用戶分群 (User Segments) 定義完成後，進行需求排序

錯誤範例：
- Roadmap 必須涵蓋三個時間維度（缺少中文主體）
- 產品路線圖必須涵蓋三個時間維度（首次出現未附英文）

### 表格雙語規則 (Bilingual Table Rules)

你必須在所有表格的欄位標題中同時使用中文與英文。格式為：`中文 (English)`

正確範例：

| 產品線 (Product Line) | 功能 (Feature) | 優先等級 (Priority) |
|---|---|---|

錯誤範例：

| 產品線 | 功能 | 優先等級 |

### 標題雙語規則 (Bilingual Heading Rules)

你必須在所有章節標題中同時使用中文與英文。格式為：`## 中文標題 (English Heading)`

正確範例：
- `## 產品願景 (Product Vision)`
- `### 核心模組 (Core Modules)`

錯誤範例：
- `## Product Vision`
- `## 產品願景`

### 例外詞彙 (Exempt Terms)

以下詞彙因在業界已高度通用，可直接使用英文而不需附中文翻譯：
- API
- PDF
- HTML
- AI
- ML
- CI/CD
- CRM
- SaaS

## 違規判定 (Violation Determination)

- 文件整體以英文撰寫而非繁體中文 → 違規
- 專業術語首次出現未附英文標註 → 違規
- 表格欄位標題僅使用單一語言 → 違規
- 章節標題僅使用單一語言 → 違規
- 同一術語在首次出現前就省略英文標註 → 違規

### 違規情境範例 (Violation Scenario)

某產品經理產出一份需求評估報告，其中：
- 標題寫為「需求評估結果」而非「需求評估結果 (Demand Intake Result)」
- 表格標題使用純中文「優先等級」而非「優先等級 (Priority)」
- 內文首次提及「策略契合度」時未附上「(Strategic Alignment)」

以上三處均構成違規，必須全部修正。

## 例外 (Exceptions)

本規則無例外。

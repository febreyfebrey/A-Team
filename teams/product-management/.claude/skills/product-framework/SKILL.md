---
name: Product Framework
description: Define product architecture using vision, user segments, core modules, data flow, and integration points for each product line
---

# 產品架構框架 (Product Framework)

## 目的

為 TutorABC 三大產品線——教育產品 (RDC)、電商平台 (SDC)、軟體發佈管理 (SRN)——提供統一的產品架構定義範本 (Product Architecture Template)，確保每個產品線的架構描述具有一致的結構與深度。

## 框架組件 (Framework Components)

每個產品架構定義必須包含以下五個組件：

### 1. 產品願景 (Product Vision)

你必須用一段話（不超過 3 句）描述該產品線的核心願景，回答以下問題：
- 這個產品為誰解決什麼問題？
- 產品的獨特價值主張 (Unique Value Proposition) 是什麼？
- 成功的衡量標準 (Success Metrics) 是什麼？

### 2. 用戶分群 (User Segments)

你必須定義該產品線的所有用戶角色 (User Personas)，每個角色包含：

| 欄位 (Field) | 說明 (Description) |
|---|---|
| 角色名稱 (Persona Name) | 用戶類型的簡稱 |
| 角色描述 (Persona Description) | 一句話描述此類用戶的特徵 |
| 核心需求 (Core Needs) | 此類用戶使用產品的主要動機 |
| 使用頻率 (Usage Frequency) | 每日 / 每週 / 每月 / 按需 |
| 付費意願 (Willingness to Pay) | 高 / 中 / 低 |

### 3. 核心模組 (Core Modules)

你必須列出產品的所有核心功能模組，每個模組包含：
- 模組名稱 (Module Name)
- 模組職責 (Module Responsibility) — 一句話描述此模組做什麼
- 輸入 (Inputs) — 此模組需要接收的資料
- 輸出 (Outputs) — 此模組產生的資料或效果
- 擁有者 (Owner) — 負責此模組的團隊

### 4. 資料流 (Data Flow)

你必須描述核心模組之間的資料流轉方式，使用以下格式：

```
[模組 A] —{資料類型}→ [模組 B] —{資料類型}→ [模組 C]
```

你必須標明每個資料流轉的觸發條件 (Trigger) 與頻率 (Frequency)。

### 5. 整合點 (Integration Points)

你必須列出該產品線與外部系統或其他產品線的所有整合點：

| 欄位 (Field) | 說明 (Description) |
|---|---|
| 整合對象 (Integration Target) | 外部系統或其他產品線名稱 |
| 整合方式 (Integration Method) | API / Webhook / 資料庫共享 / 檔案傳輸 |
| 資料方向 (Data Direction) | 單向輸入 / 單向輸出 / 雙向 |
| 整合用途 (Purpose) | 此整合解決的問題 |

## 三大產品線適用說明 (Product Line Application)

### RDC（教育產品）

RDC 的核心模組必須對應 AI 學習鏈 (AI Learning Chain) 的七大環節：

1. 定級評估 (Placement Assessment)
2. 課前預習小遊戲 (Pre-class Preview Game)
3. 課中深度互動學習 (In-class Deep Interactive Learning)
4. 課後學習評估報告 (Post-class Assessment Report)
5. AI 深度複習 (AI Deep Review)
6. 每日任務遊戲 (Daily Mission Game)
7. 延伸課外讀物或 Podcast (Extended Reading or Podcast)

### SDC（電商平台）

SDC 的核心模組必須涵蓋電商核心流程：商品管理、會員系統、購物車、結帳、訂單管理、物流追蹤、客戶服務。

### SRN（軟體發佈管理）

SRN 的核心模組必須涵蓋發佈管理核心流程：版本規劃、開發追蹤、測試管理、發佈審核、部署執行、上線監控。

## 輸出格式 (Output Format)

產品架構定義必須同時支援以下兩種格式：

- **HTML 格式** — 供線上檢視 (Online Viewing)，含可展開摺疊的模組細節
- **PDF 格式** — 供 email 寄送給主管 (Email to Boss)，含完整展開的架構圖

## 範例 (Example)

### 輸入 (Input)

```
目標產品線 (Target Product Line)：RDC（教育產品）
焦點：AI 學習鏈完整架構定義
```

### 輸出 (Output)

#### 產品願景 (Product Vision)

RDC 為成人英語學習者提供以 AI 驅動的完整學習閉環 (AI-Powered Learning Loop)，從定級評估到延伸閱讀，覆蓋學習旅程的每一個環節。獨特價值在於透過遊戲化 (Gamification) 與自適應學習 (Adaptive Learning) 技術，將被動學習轉化為主動參與。成功衡量標準為學員月活躍率 (MAU) 超過 70%、課程完成率 (Course Completion Rate) 超過 85%。

#### 用戶分群 (User Segments)

| 角色名稱 (Persona) | 描述 (Description) | 核心需求 (Core Needs) | 使用頻率 (Frequency) | 付費意願 (WTP) |
|---|---|---|---|---|
| 職場進修者 (Career Learner) | 25-40 歲上班族，需要提升商務英語能力 | 高效利用碎片時間學習 | 每日 | 高 |
| 考試備戰者 (Exam Preparer) | 準備 TOEFL/IELTS 等英語檢定的學生 | 針對性練習與精準評估 | 每日 | 中 |
| 興趣學習者 (Casual Learner) | 對英語學習有興趣但無迫切需求 | 輕鬆有趣的學習體驗 | 每週 | 低 |
| 教師 (Instructor) | 平台授課的中外籍教師 | 高效的教學工具與學員數據 | 每日 | 不適用 |

#### 核心模組 (Core Modules) — AI 學習鏈

| 模組名稱 (Module) | 職責 (Responsibility) | 輸入 (Inputs) | 輸出 (Outputs) | 擁有者 (Owner) |
|---|---|---|---|---|
| 定級評估 (Placement Assessment) | 評估學員英語程度並分配等級 | 學員基本資料、測試答題數據 | 學員等級標籤、能力雷達圖 | AI 平台團隊 |
| 課前預習小遊戲 (Pre-class Preview Game) | 以遊戲形式預習下堂課核心詞彙與句型 | 課程大綱、學員等級 | 預習完成狀態、薄弱點標記 | 遊戲化團隊 |
| 課中深度互動學習 (In-class Interaction) | 提供即時互動的線上課堂體驗 | 預習數據、教師教案 | 課堂表現數據、互動紀錄 | 教學產品團隊 |
| 課後學習評估報告 (Post-class Report) | 生成學員本堂課的學習成效報告 | 課堂表現數據、答題正確率 | 評估報告 PDF、進步趨勢圖 | 數據分析團隊 |
| AI 深度複習 (AI Deep Review) | 根據薄弱點自動生成個人化複習內容 | 評估報告、歷史錯題數據 | 個人化複習題組、知識點地圖 | AI 平台團隊 |
| 每日任務遊戲 (Daily Mission Game) | 以遊戲化每日任務維持學習黏性 | 學員等級、複習進度 | 任務完成狀態、獎勵積分 | 遊戲化團隊 |
| 延伸課外讀物/Podcast (Extended Content) | 推薦符合學員程度的課外學習資源 | 學員等級、興趣標籤 | 推薦內容清單、閱讀/收聽紀錄 | 內容營運團隊 |

#### 資料流 (Data Flow)

```
[定級評估] —{學員等級標籤}→ [課前預習小遊戲] —{預習完成狀態}→ [課中深度互動學習]
    —{課堂表現數據}→ [課後學習評估報告] —{評估報告與薄弱點}→ [AI 深度複習]
    —{複習進度}→ [每日任務遊戲] —{學習進度與興趣標籤}→ [延伸課外讀物/Podcast]
```

- **觸發條件 (Triggers)**：每個模組在前一模組輸出完成後自動觸發
- **資料更新頻率 (Frequency)**：定級評估每季更新一次，其餘模組每堂課後即時更新

#### 整合點 (Integration Points)

| 整合對象 (Target) | 整合方式 (Method) | 資料方向 (Direction) | 用途 (Purpose) |
|---|---|---|---|
| SDC 電商平台 | API | 單向輸出 | 將學習積分同步至商城供兌換使用 |
| 第三方語音辨識服務 | API | 雙向 | 提供課中語音即時辨識與糾錯 |
| CRM 系統 | API | 單向輸出 | 將學員學習數據同步至客戶關係管理 |
| 推播通知服務 (Push Notification) | Webhook | 單向輸出 | 發送每日任務提醒與學習報告 |

## 注意事項 (Notes)

- 你必須為每個核心模組指定明確的擁有者團隊
- 你不得省略任何資料流中的觸發條件與頻率描述
- 你必須標明整合點的資料方向，不得使用「視情況而定」等模糊描述
- 你必須在 RDC 產品架構中完整涵蓋 AI 學習鏈的全部七個環節，不得遺漏

---
name: Demand Intake
description: Evaluate incoming requirements using a scoring matrix and decide to accept, defer, or reject with documented rationale
---

# 需求收納評估 (Demand Intake)

## 目的

建立系統化的需求評估流程 (Demand Intake Process)，確保進入產品開發管線 (Pipeline) 的每一個需求都經過一致的評分標準審核，並記錄完整的決策理由 (Decision Rationale)。

## 評分維度 (Scoring Dimensions)

每個需求必須依照以下五個維度進行評分，每個維度的分數範圍為 1-5 分：

| 維度 (Dimension) | 權重 (Weight) | 1 分 (Low) | 3 分 (Medium) | 5 分 (High) |
|---|---|---|---|---|
| 策略契合度 (Strategic Alignment) | 30% | 與年度策略目標無關 | 與策略目標間接相關 | 直接支撐年度核心策略 |
| 營收影響 (Revenue Impact) | 25% | 無明確營收影響 | 預估營收提升 5-15% | 預估營收提升超過 15% |
| 開發成本 (Effort Estimate) | 20% | 超過 3 個月工期 | 1-3 個月工期 | 少於 1 個月工期 |
| 緊急程度 (Urgency) | 15% | 無外部截止日期 | 截止日期在 3 個月內 | 截止日期在 1 個月內 |
| 相依風險 (Dependency Risk) | 10% | 依賴 3 個以上外部團隊 | 依賴 1-2 個外部團隊 | 無外部相依性 |

## 加權總分計算 (Weighted Score Calculation)

```
加權總分 = (策略契合度 × 0.30) + (營收影響 × 0.25) + (開發成本 × 0.20) + (緊急程度 × 0.15) + (相依風險 × 0.10)
```

## 決策矩陣 (Decision Matrix)

| 加權總分 (Weighted Score) | 決策 (Decision) | 說明 (Description) |
|---|---|---|
| 3.5 分以上 | 接受 (Accept) | 立即納入產品管線，安排開發排程 |
| 2.5 – 3.49 分 | 延後 (Defer) | 列入觀察清單，下一季度重新評估 |
| 2.5 分以下 | 拒絕 (Reject) | 不納入管線，記錄拒絕原因 |

## 決策紀錄格式 (Decision Record Format)

每個需求評估必須產出以下格式的決策紀錄：

```
需求名稱 (Requirement Name)：{名稱}
來源 (Source)：{需求提出方}
提出日期 (Submission Date)：{YYYY-MM-DD}
評估日期 (Evaluation Date)：{YYYY-MM-DD}
所屬產品線 (Product Line)：RDC / SDC / SRN

評分明細 (Score Breakdown)：
- 策略契合度 (Strategic Alignment)：{1-5} — {評分理由}
- 營收影響 (Revenue Impact)：{1-5} — {評分理由}
- 開發成本 (Effort Estimate)：{1-5} — {評分理由}
- 緊急程度 (Urgency)：{1-5} — {評分理由}
- 相依風險 (Dependency Risk)：{1-5} — {評分理由}

加權總分 (Weighted Score)：{X.XX}
決策 (Decision)：接受 (Accept) / 延後 (Defer) / 拒絕 (Reject)
決策理由 (Decision Rationale)：{具體說明做出此決策的原因，引用數據或會議紀錄}
決策者 (Decision Maker)：{姓名或角色}
```

## 執行步驟 (Execution Steps)

### 步驟一：接收需求

你必須確認需求包含以下基本資訊：
- 需求描述 (Requirement Description)
- 需求來源與提出方 (Source)
- 預期效益 (Expected Benefits)
- 目標產品線 (Target Product Line)

### 步驟二：逐項評分

你必須為每個評分維度給出具體分數與評分理由。評分理由不得少於一句完整的說明。

### 步驟三：計算加權總分

你必須依照公式計算加權總分，精確到小數點後兩位。

### 步驟四：做出決策

你必須依照決策矩陣的分數門檻做出決策。不得在未計算總分的情況下直接做出決策。

### 步驟五：記錄決策

你必須以決策紀錄格式完整填寫所有欄位。決策理由必須引用至少一個具體的數據來源或會議紀錄。

## 輸出格式 (Output Format)

評估結果必須同時支援以下兩種格式：

- **HTML 格式** — 供線上檢視 (Online Viewing)
- **PDF 格式** — 供 email 寄送給主管 (Email to Boss)

## 範例 (Example)

### 輸入 (Input)

```
需求名稱 (Requirement Name)：新增 AI 語音即時糾錯功能
來源 (Source)：教學團隊主管，2025-01-15 產品需求會議
所屬產品線 (Product Line)：RDC
需求描述 (Description)：在課中深度互動學習 (In-class Deep Interactive Learning) 階段，
加入 AI 語音即時糾錯 (Real-time AI Speech Correction)，當學員發音偏差超過閾值時，
系統自動提供即時反饋與糾正建議。
預期效益 (Expected Benefits)：提升學習成效、增加用戶留存率
```

### 輸出 (Output)

```
需求名稱 (Requirement Name)：新增 AI 語音即時糾錯功能
來源 (Source)：教學團隊主管，2025-01-15 產品需求會議
提出日期 (Submission Date)：2025-01-15
評估日期 (Evaluation Date)：2025-01-20
所屬產品線 (Product Line)：RDC

評分明細 (Score Breakdown)：
- 策略契合度 (Strategic Alignment)：5 — 直接支撐「AI 學習鏈完整上線」年度核心策略，
  課中互動為學習鏈七大環節之一
- 營收影響 (Revenue Impact)：4 — 根據 2024-Q4 用戶調查，語音糾錯是排名第二的
  付費意願驅動功能，預估可提升續約率 12%
- 開發成本 (Effort Estimate)：2 — 需整合第三方語音辨識 API 並訓練中文糾錯模型，
  預估工期 2.5 個月
- 緊急程度 (Urgency)：4 — 競品 VIPKid 已於 2024-12 上線類似功能，
  市場窗口期約 3 個月
- 相依風險 (Dependency Risk)：3 — 依賴 AI 平台團隊提供語音辨識基礎設施

加權總分 (Weighted Score)：3.80
決策 (Decision)：接受 (Accept)
決策理由 (Decision Rationale)：此功能直接支撐年度 AI 學習鏈策略，
  且 2024-Q4 用戶調查數據顯示強烈付費意願。競品已搶先上線，
  延後將導致市場競爭力下降。儘管開發成本偏高，
  策略價值與營收潛力足以支撐優先開發決策。
  數據來源：2025-01-15 產品需求會議紀錄、2024-Q4 用戶調查報告。
決策者 (Decision Maker)：產品總監
```

## 注意事項 (Notes)

- 你不得跳過任何評分維度直接做出決策
- 你必須為每次「拒絕」決策提供替代方案建議 (Alternative Suggestion)
- 你必須在「延後」決策中註明下次重新評估的日期
- 你不得修改決策矩陣的分數門檻，除非經過產品總監書面核准

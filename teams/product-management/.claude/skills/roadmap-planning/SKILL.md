---
name: Roadmap Planning
description: Build and maintain multi-product roadmaps across three time horizons for RDC, SDC, and SRN product lines
---

# 產品路線圖規劃 (Roadmap Planning)

## 目的

針對 TutorABC 三大產品線——教育產品 (RDC)、電商平台 (SDC)、軟體發佈管理 (SRN)——建立跨產品路線圖 (Roadmap)，涵蓋三個時間維度，確保所有利害關係人 (Stakeholders) 對優先順序與交付時程有一致理解。

## 時間維度 (Time Horizons)

每份路線圖必須涵蓋以下三個時間維度：

| 時間維度 (Time Horizon) | 範圍 (Scope) | 粒度 (Granularity) |
|---|---|---|
| 本季度 (Current Quarter) | 當前季度內的功能與里程碑 | 以週為單位追蹤進度 |
| 下季度 (Next Quarter) | 下一季度的規劃項目 | 以月為單位規劃 |
| 六個月展望 (6-Month Outlook) | 未來六個月的策略方向 | 以季度為單位描述 |

## 路線圖欄位定義 (Roadmap Columns)

每個路線圖項目必須包含以下欄位：

| 欄位 (Column) | 說明 (Description) | 必填 (Required) |
|---|---|---|
| 產品線 (Product Line) | RDC / SDC / SRN | 是 |
| 功能名稱 (Feature) | 功能的簡短描述 | 是 |
| 優先等級 (Priority) | P0（必須）/ P1（重要）/ P2（一般） | 是 |
| 目標季度 (Target Quarter) | 預計交付的季度，格式為 YYYY-QN | 是 |
| 狀態 (Status) | 未啟動 / 進行中 / 已完成 / 已延期 / 已取消 | 是 |
| 相依性 (Dependencies) | 列出所有前置條件與跨團隊相依項目 | 是（無相依性時填寫「無」） |

## 執行步驟 (Execution Steps)

### 步驟一：收集輸入資料 (Gather Inputs)

你必須收集以下資料作為路線圖規劃的輸入：

1. **老闆方向指示 (Boss Direction)** — 來自高層的策略指令與重點方向
2. **市場數據 (Market Data)** — 競品動態、市場趨勢、用戶反饋
3. **技術限制 (Technical Constraints)** — 工程團隊的產能與技術債
4. **營收數據 (Revenue Data)** — 各產品線的營收表現與成長目標

### 步驟二：優先排序 (Prioritize)

你必須使用以下標準對功能進行優先排序：

- **策略契合度 (Strategic Alignment)** — 與公司年度目標的契合程度（權重 30%）
- **營收影響 (Revenue Impact)** — 預估對營收的正面影響（權重 25%）
- **開發成本 (Effort Estimate)** — 所需工程資源與時間（權重 20%）
- **緊急程度 (Urgency)** — 是否有外部截止日期或競爭壓力（權重 15%）
- **相依風險 (Dependency Risk)** — 跨團隊協作的複雜度與風險（權重 10%）

### 步驟三：建立路線圖表格 (Build Roadmap Table)

你必須以表格格式呈現路線圖，按產品線分組，每個時間維度一個區塊。

### 步驟四：標註相依性 (Map Dependencies)

你必須明確標註跨產品線的相依性。當 A 功能依賴 B 功能時，兩者都必須在路線圖中互相引用。

## 輸出格式 (Output Format)

路線圖必須同時支援以下兩種格式：

- **HTML 格式** — 供線上檢視 (Online Viewing)，含互動式篩選與排序
- **PDF 格式** — 供 email 寄送給主管 (Email to Boss)，含固定版面與頁首頁尾

## 範例 (Example)

### 輸入 (Input)

```
老闆方向 (Boss Direction)：
- 本季度全力推進 AI 學習鏈 (AI Learning Chain) 在 RDC 的完整上線
- SDC 商城需要在 Q2 前完成會員系統改版
- SRN 發佈流程需要導入自動化測試

市場數據 (Market Data)：
- 競品「VIPKid」已推出 AI 課後複習功能
- 電商平台用戶反饋：結帳流程過於繁瑣
- 軟體發佈業界趨勢：CI/CD 自動化已成標配
```

### 輸出 (Output)

#### 本季度路線圖 (Current Quarter Roadmap) — 2025-Q1

| 產品線 (Product Line) | 功能 (Feature) | 優先等級 (Priority) | 目標季度 (Target Quarter) | 狀態 (Status) | 相依性 (Dependencies) |
|---|---|---|---|---|---|
| RDC | AI 定級評估模組上線 (AI Placement Assessment Launch) | P0 | 2025-Q1 | 進行中 | 無 |
| RDC | 課前預習小遊戲開發 (Pre-class Preview Game Dev) | P0 | 2025-Q1 | 進行中 | 依賴定級評估模組完成 |
| RDC | 課後學習評估報告 (Post-class Assessment Report) | P1 | 2025-Q1 | 未啟動 | 依賴課中互動學習模組數據 |
| SDC | 會員系統改版第一期 (Membership System Revamp Phase 1) | P0 | 2025-Q1 | 進行中 | 無 |
| SDC | 結帳流程優化 (Checkout Flow Optimization) | P1 | 2025-Q1 | 未啟動 | 依賴會員系統改版 |
| SRN | 自動化測試框架建置 (Automated Testing Framework Setup) | P0 | 2025-Q1 | 進行中 | 無 |

#### 下季度路線圖 (Next Quarter Roadmap) — 2025-Q2

| 產品線 (Product Line) | 功能 (Feature) | 優先等級 (Priority) | 目標季度 (Target Quarter) | 狀態 (Status) | 相依性 (Dependencies) |
|---|---|---|---|---|---|
| RDC | AI 深度複習功能 (AI Deep Review Feature) | P0 | 2025-Q2 | 未啟動 | 依賴課後評估報告完成 |
| RDC | 每日任務遊戲 (Daily Mission Game) | P1 | 2025-Q2 | 未啟動 | 依賴 AI 深度複習 |
| SDC | 會員系統改版第二期 (Membership System Revamp Phase 2) | P0 | 2025-Q2 | 未啟動 | 依賴第一期完成 |
| SRN | CI/CD 流程全面自動化 (Full CI/CD Automation) | P1 | 2025-Q2 | 未啟動 | 依賴自動化測試框架 |

#### 六個月展望 (6-Month Outlook)

| 產品線 (Product Line) | 功能 (Feature) | 優先等級 (Priority) | 目標季度 (Target Quarter) | 狀態 (Status) | 相依性 (Dependencies) |
|---|---|---|---|---|---|
| RDC | 延伸課外讀物與 Podcast 平台 (Extended Reading & Podcast Platform) | P1 | 2025-Q3 | 未啟動 | 依賴 AI 學習鏈全面上線 |
| SDC | 跨境電商功能 (Cross-border E-commerce) | P2 | 2025-Q3 | 未啟動 | 依賴會員系統全面完成 |
| SRN | 發佈風險自動評估 (Release Risk Auto-Assessment) | P2 | 2025-Q3 | 未啟動 | 依賴 CI/CD 自動化 |

## 注意事項 (Notes)

- 你必須在每次路線圖更新時記錄變更原因 (Change Rationale)
- 你必須在跨產品線相依性發生變更時，同步更新所有受影響的路線圖項目
- 你不得將沒有明確負責人的功能加入路線圖
- 你必須在路線圖中標註每個功能的決策來源（會議紀錄、數據報告或主管指示）

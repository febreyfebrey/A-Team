
# A-Team

A Team Designer agent system running on Claude Code. Through in-depth dialogue to clarify your requirements, automatically generates complete multi-agent team structures (agents / skills / rules).

一組運行在 Claude Code 上的團隊設計師 agent 系統。透過深度對話釐清你的需求，自動產出完整的 multi-agent 團隊結構（agents / skills / rules）。

---

## English

### Problem Statement

You know agents work better with proper division of labor, but manually planning roles and writing individual .md files is time-consuming and error-prone. A-Team transforms "team design" itself into a conversational, automatable process.

### Quick Start

1. Copy this folder's contents to your project's `.claude/` directory
2. Launch the `team-architect` agent in Claude Code
3. Tell it what team you want to build, e.g., "I want to build a content production team for an online English teaching company"
4. Follow the interview, answer questions
5. When complete, the generated team structure appears in `teams/{team-name}/` directory

### System Architecture

```
.claude/
├── agents/
│   ├── team-architect.md            ← Chief coordinator, leads entire process
│   │
│   ├── discovery/                   ← Phase 1: Discovery
│   │   ├── requirements-analyst.md  ← In-depth interviews, extract requirements
│   │   └── role-designer.md         ← Responsibility decomposition, define roles
│   │
│   ├── planning/                    ← Phase 2: Planning
│   │   └── skill-planner.md         ← Plan skills and rules
│   │
│   ├── generation/                  ← Phase 3: Generation
│   │   ├── generation-lead.md       ← Sub-coordinator, orchestrate generation
│   │   ├── agent-writer.md          ← Write agent .md files
│   │   ├── skill-writer.md          ← Write skill .md files
│   │   └── rule-writer.md           ← Write rule .md files
│   │
│   └── optimization/                ← Phase 4: Optimization
│       └── prompt-optimizer.md      ← Review and optimize all .md prompts
│
├── skills/
│   ├── shared/                      ← Capabilities shared by multiple agents
│   │   ├── structured-interview.md  ← Structured interview methodology
│   │   ├── role-decomposition.md    ← Responsibility decomposition framework (MECE)
│   │   └── md-generation-standard.md← .md file format specifications
│   │
│   └── specialized/                 ← Capabilities specific to individual agents
│       ├── team-topology-analysis.md← Team topology analysis
│       ├── granularity-calibration.md← Granularity calibration
│       ├── quality-validation.md    ← Output quality validation
│       └── prompt-optimization.md   ← Prompt optimization methodology
│
└── rules/
    ├── coordinator-mandate.md       ← Every team must have a coordinator
    ├── output-structure.md          ← Output directory structure specification
    ├── conversation-protocol.md     ← Conversation flow protocol
    └── writing-quality-standard.md  ← .md writing quality specification
```

### Workflow

```mermaid
flowchart LR
 subgraph P1["Phase 1: Discovery"]
    direction TB
        RA["requirements-analyst <br> Interviews"]
        RD["role-designer <br> Role design"]
  end
 subgraph P2["Phase 2: Planning"]
        SP["skill-planner <br> Skill plan"]
  end
 subgraph P3["Phase 3: Generation"]
    direction TB
        GL["generation-lead <br> Sub-coordinator"]
        AW["agent-writer"]
        SW["skill-writer"]
        RW["rule-writer"]
  end
 subgraph P4["Phase 4: Optimization"]
        PO["prompt-optimizer <br> Prompt optimize"]
  end
 subgraph DIR["teams/name/"]
    direction TB
        agents["agents/"]
        skills["skills/"]
        rules["rules/"]
  end
    GL --- AW & SW & RW
    TA(["team-architect <br> Chief coordinator, spans entire process"]) -.-> P1
    P1 --> P2
    P2 --> P3
    P3 --> P4
    P4 --> RD_OUT{"Review & Deliver"}
    TA ----> RD_OUT
    RD_OUT --> DIR
```

### Design Principles

**Coordinator Mandate** — Every team must have a coordinator responsible for task planning and assignment. Coordinators do not perform execution work. When there are more than 7 agents, sub-coordinators are automatically introduced.

**Focus on Generation Quality** — The three writers in the generation phase each handle only one file type (agent / skill / rule), ensuring each .md is written with full attention.

**Prompt Optimization** — After all .md files are generated, the prompt-optimizer reviews and optimizes them, improving instruction quality while preserving original characteristics, eliminating vague expressions, and enhancing actionability.

**Depth-First Dialogue** — Requirements exploration is not skipped. Even if you have clear ideas, A-Team still validates assumptions and uncovers blind spots through interviews.

**Skills Separation** — General capabilities go in shared, specialized capabilities go in specialized, avoiding duplication and coupling.

**Multi-Language Support** — All agents communicate in the user's language. Write in English, get responses in English. Write in Traditional Chinese, get responses in Traditional Chinese.

### Customization and Extension

A-Team produces an initial framework. You can:

- Directly modify any .md file's prompt content
- Add or remove roles
- Adjust skill and rule details
- Copy the generated `teams/{name}/` folder to your actual project's `.claude/` for use

---

## 繁體中文

### 解決什麼問題

你知道 agent 要適度分工效果才好，但每次手動規劃角色、逐一撰寫 .md 檔案既耗時又容易遺漏。A-Team 把「團隊設計」這件事本身變成一個可對話、可自動化的流程。

### 快速開始

1. 將本資料夾內容複製到你的專案 `.claude/` 目錄下
2. 在 Claude Code 中啟動 `team-architect` agent
3. 告訴它你想建什麼團隊，例如：「我想幫一個線上英語教學公司建立教材製作團隊」
4. 跟著它的訪談走，回答問題即可
5. 完成後，產出的團隊結構會出現在 `teams/{team-name}/` 目錄下

### 系統架構

```
.claude/
├── agents/
│   ├── team-architect.md            ← 總調度者，主導全流程
│   │
│   ├── discovery/                   ← Phase 1: 需求探索
│   │   ├── requirements-analyst.md  ← 深度訪談，挖掘需求
│   │   └── role-designer.md         ← 職責拆分，定義角色
│   │
│   ├── planning/                    ← Phase 2: 技能規劃
│   │   └── skill-planner.md         ← 規劃 skills 與 rules
│   │
│   ├── generation/                  ← Phase 3: 結構生成
│   │   ├── generation-lead.md       ← 子調度者，統籌生成
│   │   ├── agent-writer.md          ← 專寫 agent .md
│   │   ├── skill-writer.md          ← 專寫 skill .md
│   │   └── rule-writer.md           ← 專寫 rule .md
│   │
│   └── optimization/                ← Phase 4: Prompt 優化
│       └── prompt-optimizer.md      ← 審視並優化所有 .md 的 prompt
│
├── skills/
│   ├── shared/                      ← 多 agent 共用的能力
│   │   ├── structured-interview.md  ← 結構化訪談方法論
│   │   ├── role-decomposition.md    ← 職責拆解框架（MECE）
│   │   └── md-generation-standard.md← .md 檔案格式規範
│   │
│   └── specialized/                 ← 特定 agent 專用的能力
│       ├── team-topology-analysis.md← 團隊拓撲分析
│       ├── granularity-calibration.md← 顆粒度校準
│       ├── quality-validation.md    ← 產出品質驗證
│       └── prompt-optimization.md   ← Prompt 優化方法論
│
└── rules/
    ├── coordinator-mandate.md       ← 每個團隊必須有調度者
    ├── output-structure.md          ← 產出目錄結構規範
    ├── conversation-protocol.md     ← 對話流程規範
    └── writing-quality-standard.md  ← .md 撰寫品質規範
```

### 工作流程

```mermaid
flowchart LR
    %% 總架構師
    TA([團隊架構師 <br/> 首席協調員，橫跨整個流程])

    %% 第一階段
    subgraph P1 [第一階段：需求探索]
        direction TB
        RA[需求分析師 <br/> 訪談]
        RD[角色設計師 <br/> 角色設計]
    end

    %% 第二階段
    subgraph P2 [第二階段：規劃]
        SP[技能規劃師 <br/> 技能方案]
    end

    %% 第三階段
    subgraph P3 [第三階段：生成]
        direction TB
        GL[生成組長 <br/> 次級協調員]
        AW[代理人撰寫員]
        SW[技能撰寫員]
        RW[規則撰寫員]
        
        GL --- AW & SW & RW
    end

    %% 第四階段
    subgraph P4 [第四階段：優化]
        PO[提示詞優化師 <br/> 提示詞優化]
    end

    %% 最後交付
    RD_OUT{審查與交付}
    
    subgraph DIR [teams/名稱/]
        direction TB
        agents[agents/ 代理人]
        skills[skills/ 技能]
        rules[rules/ 規則]
    end

    %% 連線邏輯
    TA -.-> P1
    P1 --> P2
    P2 --> P3
    P3 --> P4
    P4 --> RD_OUT
    TA ----> RD_OUT
    RD_OUT --> DIR
```

### 產出範例

當你告訴 A-Team「我要建立一個英語教材製作團隊」，它會產出類似這樣的結構：

```
teams/english-teaching-content/
├── agents/
│   ├── project-coordinator.md       ← 調度者（強制存在）
│   ├── content-creation/
│   │   ├── curriculum-designer.md
│   │   ├── content-writer.md
│   │   └── illustrator.md
│   ├── quality/
│   │   ├── education-reviewer.md
│   │   └── language-editor.md
│   └── delivery/
│       ├── web-developer.md
│       └── seo-specialist.md
├── skills/
│   ├── {skill-1}/
│   │   └── SKILL.md
│   └── ...
└── rules/
    └── ...
```

### 設計原則

**調度者強制存在** — 每個團隊必定有調度者負責任務規劃與分配，調度者不兼任執行工作。超過 7 個 agent 時自動引入子調度者。

**專注產生品質** — generation 階段的三個 writer 各自只負責一種檔案類型（agent / skill / rule），確保每個 .md 都被專心寫好。

**Prompt 優化** — 所有 .md 檔案生成後，由 prompt-optimizer 進行審視與優化，在保持原有特性的前提下提升指令品質、消除模糊表達、強化可執行性。

**深度對話優先** — 不跳過需求探索。即使你已經有明確想法，A-Team 仍會透過訪談驗證假設、挖掘盲點。

**Skills 共用與專用分離** — 通用能力放 shared，專業能力放 specialized，避免重複也避免耦合。

**多語言支援** — 所有 agent 都使用用戶的語言溝通。用英文寫就用英文回覆，用繁體中文寫就用繁體中文回覆。

### 自訂與擴展

A-Team 產出的是初版框架。你可以：

- 直接修改任何 .md 檔案的 prompt 內容
- 增加或移除角色
- 調整 skill 和 rule 的細節
- 將產出的 `teams/{name}/` 資料夾複製到實際專案的 `.claude/` 中使用

---

## License

MIT

# A-Team

A-Team is a multi-agent team designer. It interviews the user, decomposes responsibilities, plans skills and rules, and generates ready-to-run team structures.

This repository supports **dual-platform** operation:

- **Claude Code**: `CLAUDE.md` + `.claude/` (agents, skills, rules)
- **Codex**: `AGENTS.md` + project `.codex/` + runtime `agents/` + `.agents/skills/`

Each platform has its own native configuration. Format conversion between platforms is also supported.

---

## 設計理念

**Anthropic 官方研究的蒸餾**

把 Anthropic 的 prompt engineering 教程、課程、Claude 4 最佳實踐、Context Engineering 部落格全部讀完，系統性地轉化成 agent 的行為約束規則。

結構性方案優先於指令、XML tag 分離、Uncertainty Protocol、Claude 4.6 語氣校準，全部有對應的規則檔。

**Agent 架構設計哲學**

扁平架構，一個 coordinator 管全部 agent，不搞 sub-coordinator。

Context 分級，不是每個 agent 都要知道所有事。

每個生成的團隊強制要有 process reviewer，專門看協作流程的問題，跟看產出品質的 QA 分開。

**可追溯的決策鏈**

每個設計決策都有 worklog 記錄，references → findings → decisions 形成證據鏈。

可以回溯每個「為什麼這樣設計」。

worklog 同時拿來做 context offloading，寫進去就能釋放 context window。

**Anti-Sycophancy**

禁止 agent 講「That's an interesting approach」這種模糊討好的話。

有問題直接講，同時給替代方案。

**雙模式、雙平台**

運行支援 subagent（coordinator 在同一 session 調度）跟 Agent Teams（每個 agent 獨立跑、平行作業）。

平台支援 Claude Code 跟 Codex，雙平台原生設定共存，格式也能互轉。

---

## Design Philosophy

**Distilled from Anthropic's Official Research**

Read through Anthropic's prompt engineering tutorials, courses, Claude 4 best practices, and Context Engineering blog, then systematically converted them into enforceable agent behavior rules.

Structural solutions over instructions, XML tag separation, Uncertainty Protocol, Claude 4.6 tone calibration — all backed by dedicated rule files.

**Agent Architecture Philosophy**

Flat architecture: one coordinator manages all agents, no sub-coordinators.

Context tiering: not every agent needs to know everything.

Every generated team must have a process reviewer — dedicated to reviewing collaboration quality, separate from QA.

**Traceable Decision Chains**

Every design decision is recorded in a worklog: references → findings → decisions, forming a full evidence chain.

Every "why was it designed this way" is traceable.

The worklog doubles as context offloading — write it down, free up the context window.

**Anti-Sycophancy**

Agents are prohibited from saying things like "That's an interesting approach" or other vague, agreeable responses.

If there's a problem, say it directly. Always provide an alternative.

**Dual Mode, Dual Platform**

Supports subagent mode (coordinator dispatches within one session) and Agent Teams (each agent runs independently, working in parallel).

Supports both Claude Code and Codex, with native configs coexisting in one repo. Format conversion works both ways.

---

## English

### What A-Team Does

A-Team is not the final agent team. It is a **team-design system** that generates agent teams.

It takes a vague request such as:

> "I need a content operations team for an education product"

and turns it into:

- a coordinator role
- grouped specialist roles
- reusable skills
- hard rules
- worklog and context management rules
- a ready-to-use team folder under `teams/{team-name}/`

### Dual-Platform Generation

A-Team supports **dual-platform team generation**:

- **Claude Code native generation**: produces `CLAUDE.md` + `.claude/` structure
- **Codex native generation**: produces `AGENTS.md` + project `.codex/` + runtime `agents/` + `.agents/skills/` structure
- **Dual-format**: generates both platform formats in one pass

During discovery, A-Team asks which delivery format the user wants and generates the corresponding native structure directly.

### Format Conversion Support

A-Team also supports **format conversion** between Codex and Claude Code team layouts.

Supported conversion flows:

- **Claude Code → Codex**: convert an existing Claude Code team to Codex format
- **Codex → Claude Code**: convert an existing Codex team to Claude Code format

### Codex Quick Start

1. Open this repo in Codex.
2. Let Codex load the root `AGENTS.md`.
3. Use the existing A-Team runtime directly at the repo root. This repo already ships project-level multi-agent registration in `.codex/config.toml` and `agents/`.
4. Tell A-Team what kind of team you want to design.
5. Follow the discovery interview.
6. Review the generated output under `teams/{team-name}/`.

### Codex Settings

The project-level Codex settings live in `.codex/config.toml`:

```toml
model = "gpt-5.4"
model_reasoning_effort = "xhigh"
disable_response_storage = true
project_doc_fallback_filenames = ["AGENTS.md", "CLAUDE.md"]

[features]
multi_agent = true

[agents]
max_threads = 6
max_depth = 1

[agents.team_architect]
description = "Coordinate A-Team discovery, planning, generation, review, and delivery."
config_file = "../agents/team-architect.toml"
```

`config_file` is resolved relative to `.codex/config.toml`, not the repo root. Because this repo keeps runtime agent configs in the top-level `agents/` directory, the correct path form is `../agents/...`.

What these settings are for:

- `model = "gpt-5.4"`: use the current frontier agentic coding model as the repo default
- `model_reasoning_effort = "xhigh"`: prefer maximum reasoning headroom for coordination-heavy and multi-step design work
- `disable_response_storage = true`: reduces saved output overhead for multi-agent sessions
- `project_doc_fallback_filenames = ["AGENTS.md", "CLAUDE.md"]`: lets Codex open both new Codex teams and older Claude-only teams
- `[features] multi_agent = true`: enables Codex multi-agent at the project level for this repo
- `[agents]` plus `[agents.<id>]`: registers A-Team's coordinator and specialists for Codex runtime

### How Multi-Agent Works In Codex Here

For this repo, the orchestration logic is driven by `AGENTS.md` and `.codex/agents/team-architect.md`. Codex multi-agent is enabled from this project's `.codex/config.toml`, not from a required user-level flag.

This repo also ships its own official agent registry under `agents/`. The runtime `agents/*.toml` files are thin Codex-facing configs; the `.codex/agents/*.md` files remain the source-of-truth playbooks those configs point to.

When a team or generation pass uses `multi-agent` mode, the coordinator:

- checks the target project's `.codex/config.toml` for `[features] multi_agent = true` and agent registry conflicts
- checks `~/.claude/settings.json` for `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` only when Claude Code support also matters
- delegates focused specialist work with `spawn_agent`
- sends clarifications or follow-up tasks with `send_input`
- joins work only at real synchronization points with `wait`

Recommended rule of thumb:

- use `single-agent` when the work is mostly sequential or tightly coupled
- use `multi-agent` when role boundaries are clear and file ownership can stay non-overlapping

### Skills In Codex

Codex runtime skill discovery happens from `.agents/skills/`.

That means:

- `.codex/skills/` = authored/maintained mirror
- `.agents/skills/` = runtime-discoverable mirror

Do not rely on `.codex/skills/` alone if you want Codex to auto-discover project skills.

### Repository Layout

```text
.
├── AGENTS.md
├── .codex/
│   ├── config.toml
│   ├── agents/             # A-Team's internal playbook library
│   ├── rules/
│   ├── skills/
│   └── docs/
├── agents/                  # A-Team's own Codex runtime agent configs
├── .agents/
│   └── skills/
├── .claude/                 # Claude Code native config
├── .worklog/                # Phase-level work documentation
│   └── {yyyymm}/{task}/    # references.md, findings.md, decisions.md per phase
└── teams/
```

### Quick Start

```
/A-Team
```

This slash command spawns the Team Architect coordinator and runs the full 6-phase workflow. You can also pass arguments:

```
/A-Team 自動化測試團隊          # start with a direction
/A-Team --restructure teams/x   # restructure an existing team
```

Every generated team also gets a `/boss` entry-point skill so team members can invoke their coordinator the same way.

### Core Workflow

1. Discovery
   Clarify the requested delivery format, objectives, scope, workflow, role candidates, and whether `single-agent` or `multi-agent` is appropriate. Domain Researcher investigates best practices in parallel.
2. Planning
   Decide shared skills, specialist skills, external skill reuse, hard rules, and retained conversion requirements. Decision Auditor reviews Phase 1 decisions.
3. Generation
   Generate `AGENTS.md`, `.codex/`, `agents/`, `.agents/skills/`, worklog/context management rules, and mapping artifacts for the target team.
4. Optional optimization
   Tighten prompts and reduce ambiguity.
5. Review
   Validate structure, paths, ownership, and execution mode consistency.
6. Dialogue review
   Audit the quality of the consultation itself.

Cross-phase support: `domain-researcher` (external investigation) and `decision-auditor` (decision quality audit) are available at any phase boundary or on-demand.

### Worklog System

Every task maintains a `.worklog/` directory with phase-level documentation:

```
.worklog/202603/team-name/
├── phase-1-discovery/
│   ├── references.md      ← Sources consulted
│   ├── findings.md        ← Key discoveries
│   └── decisions.md       ← Decisions + rationale
└── phase-2-planning/
    └── ...
```

This provides **verifiability** (every decision traces to evidence), **traceability** (full audit trail), and **continuity** (interrupted work can be resumed from worklog). The worklog also serves as a context offloading mechanism — agents read from worklog files instead of carrying full context.

### Generated Team Output

Generated teams follow this structure:

```text
teams/{team-name}/
├── AGENTS.md
├── agents/
├── .codex/
│   ├── config.toml
│   ├── docs/
│   │   ├── format-mapping.md
│   │   └── format-mapping.manifest.yaml
│   ├── rules/                # Includes worklog + context management rules
│   └── skills/
├── .worklog/                  # Phase-level documentation (created at runtime)
└── .agents/
    └── skills/
```

### Platform Mapping

Both platforms coexist in this repo, each with its own native configuration:

| Claude Code | Codex | Notes |
| --- | --- | --- |
| `CLAUDE.md` | `AGENTS.md` | Project entrypoint |
| `.claude/agents/` | `agents/` + `.codex/config.toml` | Agent definitions and registry |
| `.claude/rules/` | `.codex/rules/` | Rule definitions |
| `.claude/skills/` | `.codex/skills/` + `.agents/skills/` | Skills (Codex has runtime mirror) |

Format conversion between the two is supported in both directions.

### Output Example: Business Analysis Team

A real team generated by A-Team: [`teams/business-analysis/`](teams/business-analysis/) — a business analysis team that transforms vague PM/client requirements into complete development documents (PRD, SA, SD, Test Cases) with PM confirmation gates, JIRA integration, and company-level glossary management.

**Workflow:**

```
⓪ Knowledge Curator prepares background pack (glossary + cross-project refs)
① Interview → ② Requirements Summary → ③ Requirements Draft → ④ Full PRD
⑤ SA → ⑥ SD → ⑦ Test Cases → ⑧ Glossary Update → ⑨ Process Review

Each phase: Executor → Document Reviewer → PM Confirmation → Next Phase
JIRA: PRD→Epic+Stories | SA→Update Stories | SD→Sub-tasks | TC→Test Sub-tasks
```

### Deployment Modes

A-Team generates teams that support two deployment modes:

**Subagent Mode (Default)** — Agents are invoked via the Task tool within a single Claude Code session. The coordinator manages all delegation. Best for sequential workflows with clear handoffs. Works out of the box.

**Agent Teams Mode (Experimental)** — Agents run as independent Claude Code instances with shared task lists and peer-to-peer messaging. Best for parallel workflows where agents need direct communication. Requires enabling:

```json
// settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

Reference: https://code.claude.com/docs/en/agent-teams

### Design Principles

**Coordinator Mandate** — Every team must have a coordinator responsible for task planning and assignment. Coordinators do not perform execution work. Flat architecture only — no sub-coordinators.

**Parallelism-Aware Design** — During requirements exploration, A-Team identifies which tasks can run in parallel and designs communication patterns (peer-to-peer messaging, broadcast) for Agent Teams deployment.

**Focus on Generation Quality** — The three writers in the generation phase each handle only one file type (agent / skill / rule), ensuring each .md is written with full attention.

**Prompt Optimization** — After all .md files are generated, the prompt-optimizer reviews and optimizes them, improving instruction quality while preserving original characteristics, eliminating vague expressions, and enhancing actionability.

**Depth-First Dialogue** — Requirements exploration is not skipped. Even if you have clear ideas, A-Team still validates assumptions and uncovers blind spots through interviews.

**CLAUDE.md as Team Contract** — Every generated team includes a `CLAUDE.md` containing team-wide instructions that all agents must follow. Role-specific rules go in `rules/`.

**Multi-Language Support** — All agents communicate in the user's language. Write in English, get responses in English. Write in Traditional Chinese, get responses in Traditional Chinese.

### Customization and Extension

A-Team produces an initial framework. You can:

- Directly modify any .md file's prompt content
- Add or remove roles
- Adjust skill and rule details
- Copy the generated `teams/{name}/` folder to your actual project root for use

---

## 繁體中文

### A-Team 是做什麼的

A-Team 不是最終要執行工作的 agent team，本身是**用來設計 agent team 的系統**。

它會把像這樣的需求：

>「我想做一個教育產品的內容營運團隊」

轉成：

- 一個 coordinator
- 一組有清楚分工的 specialist agents
- 可重用的 skills
- 不可違反的 rules
- worklog 與上下文管理規則
- 一個可直接拿去用的 `teams/{team-name}/` 輸出資料夾

### 支援雙平台生成

A-Team 支援**雙平台團隊生成**：

- **Claude Code 原生生成**：產出 `CLAUDE.md` + `.claude/` 結構
- **Codex 原生生成**：產出 `AGENTS.md` + 專案 `.codex/` + runtime `agents/` + `.agents/skills/` 結構
- **Dual-format**：一次同時產出兩個平台的格式

A-Team 會在 discovery 階段詢問使用者要哪一種團隊格式，直接生成對應的原生結構。

### 支援格式轉換

A-Team 也支援 **Codex 與 Claude Code 團隊格式之間的互相轉換**：

- **Claude Code → Codex**：把現有 Claude Code 團隊轉成 Codex 格式
- **Codex → Claude Code**：把現有 Codex 團隊轉成 Claude Code 格式

### Codex 快速開始

1. 用 Codex 開啟這個 repo。
2. 讓 Codex 讀取 root `AGENTS.md`。
3. 直接在 repo root 使用 A-Team。這個 repo 已經內建 project-level multi-agent registration：`.codex/config.toml` + `agents/`
4. 告訴它你想設計什麼團隊。
5. 跟著 discovery 訪談把需求講清楚。
6. 到 `teams/{team-name}/` 檢查產出的 Codex 版團隊結構。

### Codex 設定放哪裡

Codex 的專案設定放在 `.codex/config.toml`：

```toml
model = "gpt-5.4"
model_reasoning_effort = "xhigh"
disable_response_storage = true
project_doc_fallback_filenames = ["AGENTS.md", "CLAUDE.md"]

[features]
multi_agent = true

[agents]
max_threads = 6
max_depth = 1

[agents.team_architect]
description = "Coordinate A-Team discovery, planning, generation, review, and delivery."
config_file = "../agents/team-architect.toml"
```

`config_file` 是相對於 `.codex/config.toml` 解析，不是相對於 repo root。因為這個 repo 把 runtime agent config 放在頂層 `agents/` 目錄，所以正確寫法是 `../agents/...`。

這些設定的用途：

- `model = "gpt-5.4"`：把目前的 frontier agentic coding model 設成這個 repo 的預設
- `model_reasoning_effort = "xhigh"`：對高協調、長鏈推理、稍微複雜的 agent 工作優先給最大 reasoning headroom
- `disable_response_storage = true`：降低多代理流程的回應儲存負擔
- `project_doc_fallback_filenames = ["AGENTS.md", "CLAUDE.md"]`：讓 Codex 既能讀新的 Codex team，也能讀舊的 Claude team
- `[features] multi_agent = true`：直接在這個專案內開啟 Codex multi-agent
- `[agents]` 與 `[agents.<id>]`：把 A-Team 自己的 coordinator 與 specialists 註冊給 Codex runtime

### Codex 要怎麼「開啟 Multi-Agent」

這個 repo 的多代理流程由 `AGENTS.md` 和 `.codex/agents/team-architect.md` 驅動，而 Codex multi-agent 是透過這個專案自己的 `.codex/config.toml` 啟用，不依賴使用者家目錄設定。

這個 repo 也已經在 `agents/` 提供 A-Team 自己的官方 agent registry。`agents/*.toml` 是給 Codex runtime 讀的薄設定，而 `.codex/agents/*.md` 仍是 source-of-truth 的 authored playbook。

Codex 版 multi-agent 的做法是：

- 先檢查目標專案的 `.codex/config.toml` 是否已有 `[features] multi_agent = true` 與既有 agent registry
- 如果也要兼容 Claude Code，再檢查 `~/.claude/settings.json` 的 `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS`
- root `AGENTS.md` 定義 coordinator contract
- `.codex/agents/team-architect.md` 定義整個多代理流程
- coordinator 在需要時用 `spawn_agent` 拆給 specialist
- 需要補充上下文時用 `send_input`
- 只有在真正需要 join 的時候才用 `wait`

簡單判斷方式：

- `single-agent`：任務高度耦合、順序很重、拆了反而會多溝通成本
- `multi-agent`：角色邊界清楚、可平行、而且檔案 ownership 能明確切開

### Codex 的 Skills 放哪裡

Codex runtime 會從 `.agents/skills/` 掃描 project skills，所以這裡分兩層：

- `.codex/skills/`：作者維護用、設計來源
- `.agents/skills/`：Codex runtime 真正會掃描的 skill surface

也就是說，**只有 `.codex/skills/` 不夠**。如果你希望 Codex 自動發現技能，還是要同步到 `.agents/skills/`。

### 目前 Repo 結構

```text
.
├── AGENTS.md
├── .codex/
│   ├── config.toml
│   ├── agents/             # A-Team 內部的 playbook library
│   ├── rules/
│   ├── skills/
│   └── docs/
├── agents/                  # A-Team 自己的 Codex runtime agent configs
├── .agents/
│   └── skills/
├── .claude/                 # Claude Code 原生設定
├── .worklog/                # 階段性工作文件
│   └── {yyyymm}/{task}/    # 每個 phase 有 references.md, findings.md, decisions.md
└── teams/
```

### 快速開始

```
/A-Team
```

這個 slash command 會啟動 Team Architect 指揮官，跑完整的 6 階段流程。也可以帶參數：

```
/A-Team 自動化測試團隊          # 帶方向直接開始
/A-Team --restructure teams/x   # 重構現有團隊
```

每個產出的團隊也會自動生成 `/boss` 入口 skill，讓團隊成員可以用同樣的方式啟動自己的指揮官。

### A-Team 的工作流程

1. Discovery
   先確認需要的團隊格式，再釐清目標、範圍、workflow、角色候選，以及到底該用 `single-agent` 還是 `multi-agent`。Domain Researcher 同步調查領域最佳實踐。
2. Planning
   規劃 shared skills、specialized skills、external skill reuse、rules，以及要保留哪些轉換資訊。Decision Auditor 審核 Phase 1 決策。
3. Generation
   為目標團隊產出 `AGENTS.md`、`.codex/`、`agents/`、`.agents/skills/`、worklog/上下文管理規則 與 mapping artifacts
4. Optional optimization
   收斂 prompt、減少模糊與冗語
5. Review
   驗證結構、路徑、ownership 與 execution mode 是否一致
6. Dialogue review
   回頭審視整個諮詢對話品質

跨階段支援：`domain-researcher`（外部調查）和 `decision-auditor`（決策品質審核）可在任何階段邊界或臨時需要時調用。

### Worklog 系統

每個任務都在 `.worklog/` 目錄下維護階段性文件：

```
.worklog/202603/team-name/
├── phase-1-discovery/
│   ├── references.md      ← 參考資訊來源
│   ├── findings.md        ← 關鍵發現與分析
│   └── decisions.md       ← 決策 + 理由
└── phase-2-planning/
    └── ...
```

三大核心價值：**可驗證性**（每個決策可追溯到證據）、**可追溯性**（完整審計軌跡）、**可接續性**（中斷的工作可從 worklog 恢復）。Worklog 同時作為上下文卸載機制 — agent 從 worklog 讀取資料，而非攜帶完整 context。

### 產出的團隊長什麼樣子

```text
teams/{team-name}/
├── AGENTS.md
├── agents/
├── .codex/
│   ├── config.toml
│   ├── docs/
│   │   ├── format-mapping.md
│   │   └── format-mapping.manifest.yaml
│   ├── rules/                # 包含 worklog + 上下文管理規則
│   └── skills/
├── .worklog/                  # 階段性工作文件（執行時建立）
└── .agents/
    └── skills/
```

### 雙平台對照

兩個平台各自有原生設定，共存於此 repo：

| Claude Code | Codex | 說明 |
| --- | --- | --- |
| `CLAUDE.md` | `AGENTS.md` | 專案入口 |
| `.claude/agents/` | `agents/` + `.codex/config.toml` | Agent 定義與註冊 |
| `.claude/rules/` | `.codex/rules/` | Rule 定義 |
| `.claude/skills/` | `.codex/skills/` + `.agents/skills/` | Skills（Codex 有 runtime mirror） |

兩個方向的格式轉換皆有支援。

### 產出範例：Business Analysis 團隊

由 A-Team 實際產出的團隊：[`teams/business-analysis/`](teams/business-analysis/) — 一個 business analysis 團隊，將 PM/客戶的模糊需求轉化為完整開發文件（PRD、SA、SD、Test Cases），具備 PM 確認閘門、JIRA 整合、公司級 Glossary 管理。

**工作流程：**

```
⓪ Knowledge Curator 準備背景包（Glossary + 跨專案參照）
① 訪談 → ② 需求摘要 → ③ 需求草稿 → ④ 完整 PRD
⑤ SA → ⑥ SD → ⑦ 測試案例 → ⑧ Glossary 更新 → ⑨ 流程回顧

每階段：執行者 → Document Reviewer 審查 → PM 確認 → 下一階段
JIRA：PRD→Epic+Stories | SA→更新 Stories | SD→Sub-tasks | TC→測試 Sub-tasks
```

### 部署模式

A-Team 產出的團隊支援兩種部署模式：

**Subagent 模式（預設）** — Agent 透過 Task tool 在單一 Claude Code session 中被調用。由 coordinator 管理所有委派。適合有明確交接的順序性工作流程。開箱即用。

**Agent Teams 模式（實驗性）** — Agent 作為獨立的 Claude Code 實例運行，具有共享任務清單和 peer-to-peer 訊息傳遞。適合需要直接溝通的並行工作流程。需要啟用：

```json
// settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

參考文件：https://code.claude.com/docs/zh-TW/agent-teams

### 設計原則

**調度者強制存在** — 每個團隊必定有調度者負責任務規劃與分配，調度者不兼任執行工作。採用扁平架構——禁止子調度者。

**並行感知設計** — 在需求探索階段，A-Team 識別哪些任務可以並行執行，並為 Agent Teams 部署設計溝通模式（peer-to-peer 訊息、廣播）。

**專注產生品質** — generation 階段的三個 writer 各自只負責一種檔案類型（agent / skill / rule），確保每個 .md 都被專心寫好。

**Prompt 優化** — 所有 .md 檔案生成後，由 prompt-optimizer 進行審視與優化，在保持原有特性的前提下提升指令品質、消除模糊表達、強化可執行性。

**深度對話優先** — 不跳過需求探索。即使你已經有明確想法，A-Team 仍會透過訪談驗證假設、挖掘盲點。

**CLAUDE.md 作為團隊契約** — 每個生成的團隊都包含 `CLAUDE.md`，內含所有 agent 必須遵守的團隊級指令。角色專屬規則放在 `rules/`。

**多語言支援** — 所有 agent 都使用用戶的語言溝通。用英文寫就用英文回覆，用繁體中文寫就用繁體中文回覆。

### 自訂與擴展

A-Team 產出的是初版框架。你可以：

- 直接修改任何 .md 檔案的 prompt 內容
- 增加或移除角色
- 調整 skill 和 rule 的細節
- 將產出的 `teams/{name}/` 資料夾複製到實際專案根目錄中使用

---

## License

MIT

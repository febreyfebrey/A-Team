---
name: Document Format Standard
description: All documents must follow the defined Confluence format and structure standards
---

# Document Format Standard

## Applicability

- Applies to: All agents that produce or review documents in the requirements-docs team

## Rule Content

### 文件資訊區塊 (Document Info Block)

Every document must start with a 文件資訊區塊 containing the following fields:

| Field | Description |
|-------|-------------|
| 文件標題 | Full document title in Traditional Chinese |
| 版本號 | Semantic version (e.g., v1.0, v1.1) |
| 作者 | Agent or person who authored the document |
| 建立日期 | Creation date in YYYY-MM-DD format |
| 最後更新日期 | Last updated date in YYYY-MM-DD format |
| 狀態 | One of: 草稿 (Draft) / 審核中 (Under Review) / 已確認 (Confirmed) |

### Table of Contents

Every document must include a Confluence TOC macro immediately after the 文件資訊區塊:

```
{toc:printable=true|style=disc|maxLevel=3}
```

### Confluence Element Usage

Use these Confluence elements throughout all documents:

- **Tables**: Use for structured data (API endpoints, field definitions, test cases, role matrices).
- **Info panels**: Use `{info}`, `{note}`, `{warning}` macros to highlight key information, caveats, and risks.
- **Status macros**: Use `{status}` to indicate item status (e.g., confirmed, pending, blocked).
- **Expand macros**: Use `{expand}` for detailed content that supports but is not essential to the main narrative.

### Chapter Structure Compliance

Each document type must follow its defined chapter structure as specified in the corresponding skill template. You must not omit, reorder, or merge required chapters.

### Diagram Notation

All diagrams must use text-based notation that Confluence can render:

- Use **Mermaid** or **PlantUML** syntax.
- Embed diagrams as code blocks with the appropriate macro identifier.
- You must not use image files or external diagram tools that produce non-editable outputs.

## Violation Determination

- Document missing 文件資訊區塊 → Violation
- 文件資訊區塊 missing any of the 6 required fields → Violation
- Document missing TOC macro → Violation
- PRD missing any of its required chapters as defined in the PRD skill template → Violation
- Document uses plain text lists where a table is clearer (e.g., for API endpoints, field definitions, test case steps) → Violation
- Diagram uses an image file instead of text-based notation → Violation

## Exceptions

This rule has no exceptions.

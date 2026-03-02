---
name: Document Review Checklist
description: Structured checklist for reviewing document quality across five dimensions before PM submission
---

# Document Review Checklist

## Purpose

Use this checklist to systematically review any document (PRD, SA, SD, Test Cases) before submitting it to the PM for approval. Every review must evaluate all five dimensions. Do not skip any dimension.

## Review Dimensions

### 1. 完整性 Completeness

Verify that every required section exists and contains substantive content.

Checklist items:

- [ ] All sections defined in the document template are present
- [ ] No section contains only placeholder text (e.g., "TBD", "To be determined", "...")
- [ ] 文件資訊 block has all required fields filled (version, author, date, status)
- [ ] Every User Story (PRD) has acceptance criteria defined
- [ ] Every feature module has at least one associated requirement or test case
- [ ] Non-functional requirements have specific numeric targets, not vague descriptions
- [ ] 名詞定義 section covers all domain-specific terms used in the document
- [ ] Cross-reference links (PRD ↔ SA ↔ SD ↔ Test Cases) are present and valid

### 2. 一致性 Consistency

Verify that content does not contradict itself across sections or documents.

Checklist items:

- [ ] The same feature is described identically in every section where it appears
- [ ] User role names are consistent throughout the document (no aliases without definition)
- [ ] Data entity field names match between SA entity definitions and SD database schema
- [ ] API endpoint paths in SD match the module structure described in SA
- [ ] Priority labels (P0/P1/P2) use the same scale and meaning throughout
- [ ] Terminology is used consistently — the same concept uses the same term everywhere
- [ ] Scope section and feature list do not contradict each other (no feature listed in scope but missing from details, or vice versa)
- [ ] Acceptance criteria in PRD align with test cases in the Test Cases document

### 3. 可讀性 Readability

Verify that the document is concise, clear, and easy to navigate.

Checklist items:

- [ ] Each paragraph conveys one main idea — no paragraph exceeds 5 sentences
- [ ] Technical terms include English in parentheses on first occurrence
- [ ] Abbreviations are defined before first use
- [ ] Tables are used for structured data instead of long prose lists
- [ ] Heading hierarchy is logical (h2 → h3 → h4, no skipping levels)
- [ ] No sentence contains more than 2 clauses
- [ ] Actionable items use imperative sentences, not passive voice
- [ ] Examples are provided for complex business rules or calculations

### 4. 格式合規 Format Compliance

Verify that the document follows Confluence format and project conventions.

Checklist items:

- [ ] Document starts with 文件資訊 block
- [ ] Confluence macros are used correctly (info panels, status macros, expand macros, table macros)
- [ ] Diagrams use draw.io or PlantUML macros (not pasted images without source)
- [ ] All diagrams have labeled connections — no unlabeled arrows
- [ ] Code blocks use proper language tags for syntax highlighting
- [ ] Bilingual format is followed: Chinese term followed by English in parentheses on first use
- [ ] Document status is set correctly (Draft for new, In Review for submitted)
- [ ] Version number has been incremented from the previous version

### 5. 受眾適配 Audience Fit

Verify that content depth and language match the target audience.

Checklist items:

**PRD (audience: PM, stakeholders):**
- [ ] No implementation details (no database names, no API paths, no code snippets)
- [ ] Business value is clearly stated for each feature
- [ ] UI/UX descriptions focus on user experience, not technical components

**SA (audience: development team):**
- [ ] Analysis focuses on "what" the system does, not "how" it is implemented
- [ ] Business rules are traceable to PRD requirements
- [ ] Data entities are defined at the logical level, not physical database level

**SD (audience: development team):**
- [ ] Implementation details are specific enough for a developer to start coding
- [ ] API specifications include request/response examples
- [ ] Database schema includes data types, constraints, and indexes

**Test Cases (audience: PM, QA team):**
- [ ] Test steps are executable without additional interpretation
- [ ] Expected results are observable and verifiable
- [ ] No test case requires reading source code to understand

## Review Output Format

Produce the review result in the following structure:

```
## Document Review Report

- **Document**: {document name and version}
- **Reviewer**: {reviewer name}
- **Review Date**: YYYY-MM-DD
- **Overall Verdict**: Pass / Pass with Comments / Revise Required

### Review Summary

| Dimension | Result | Issues Found |
|-----------|--------|-------------|
| 完整性 Completeness | Pass / Fail | {count} |
| 一致性 Consistency | Pass / Fail | {count} |
| 可讀性 Readability | Pass / Fail | {count} |
| 格式合規 Format Compliance | Pass / Fail | {count} |
| 受眾適配 Audience Fit | Pass / Fail | {count} |

### Issues Detail

| # | Dimension | Section | Issue Description | Severity | Recommendation |
|---|-----------|---------|------------------|----------|---------------|
| 1 | ... | ... | ... | Critical / Major / Minor | Specific fix action |

### Positive Highlights

- {What the document does well}
```

Severity definitions:
- **Critical**: Document cannot be submitted — missing sections, contradictions, or wrong audience level
- **Major**: Document can be submitted but will cause confusion — inconsistent terms, vague requirements
- **Minor**: Document quality improvement — formatting, readability, minor clarifications

## Example

### Input (Document with Issues)

A PRD containing: Section 5 (功能需求) references a "super admin" role that is not defined in Section 4 (使用者角色). Section 7 (非功能需求) states "system must be fast" without a numeric target. Section 6 (UI/UX) includes the sentence "Frontend uses React with Redux state management."

### Output (Review Feedback)

```
### Issues Detail

| # | Dimension | Section | Issue Description | Severity | Recommendation |
|---|-----------|---------|------------------|----------|---------------|
| 1 | 一致性 | 5. 功能需求 | "Super admin" role referenced in US-ADMIN-003 is not defined in Section 4 使用者角色 | Critical | Add "Super Admin" role definition to Section 4 with role description, primary goal, usage frequency, and technical level |
| 2 | 完整性 | 7. 非功能需求 | Performance requirement states "must be fast" without a measurable target | Major | Replace with specific metric: "API response time < 500ms (P95), page load time < 2 seconds" |
| 3 | 受眾適配 | 6. UI/UX 需求 | Contains implementation detail "Frontend uses React with Redux" — PRD audience is PM, not dev team | Major | Remove technology references. Rewrite as: "頁面必須支援即時狀態更新，使用者操作後 UI 立即反映變更" |
```

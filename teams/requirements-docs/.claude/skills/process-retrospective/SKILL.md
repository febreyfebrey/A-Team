---
name: Process Retrospective
description: Evaluation framework for reviewing team collaboration process quality with scoring and evidence
---

# Process Retrospective

## Purpose

Use this skill to evaluate the team's collaboration process after each project cycle. Focus exclusively on how the team worked together, not on whether the deliverables are correct. Deliverable quality is the QA reviewer's responsibility — this skill covers process quality only.

## Evaluation Framework

Evaluate every project cycle across all five dimensions below. Do not skip any dimension. Assign a score from 1 to 5 for each dimension.

### Scoring Scale

| Score | Label | Definition |
|-------|-------|-----------|
| 1 | Critical Issues | Process breakdown — critical information lost, phases skipped, or major rework caused by process failures |
| 2 | Significant Issues | Multiple process problems that caused delays or quality degradation |
| 3 | Acceptable | Process mostly followed, minor issues that did not cause significant impact |
| 4 | Good | Process followed consistently with only trivial improvements possible |
| 5 | Excellent | Process executed smoothly, proactive improvements surfaced, no issues identified |

### Dimension 1: 溝通品質 Inter-Agent Communication Quality

Evaluate whether handoff messages between agents contained all necessary information.

Check the following indicators:

- **Handoff completeness**: When Agent A passed work to Agent B, did the handoff include: task context, input data, expected output format, and constraints?
- **Ambiguity**: Were there instances where the receiving agent had to ask clarifying questions that the sending agent could have anticipated?
- **Information loss**: Did any critical requirement, constraint, or decision get dropped during handoff between agents?
- **Response clarity**: Were agent responses structured and scannable, or were they buried in long unstructured text?

### Dimension 2: 流程遵循 Workflow Adherence

Evaluate whether the team followed the defined workflow phases in the correct order.

Check the following indicators:

- **Phase order**: Were phases executed in sequence (Requirements → SA → SD → Test Cases)? Were any phases started before the preceding phase was confirmed?
- **Milestone gates**: Was each document reviewed and confirmed before the next phase began?
- **JIRA operations**: Were JIRA tickets created at the correct milestones, not earlier or later?
- **Review cycle**: Did every document go through the review process before being marked as confirmed?

### Dimension 3: 協作效率 Collaboration Efficiency

Evaluate whether the team avoided unnecessary overhead and resolved blockers promptly.

Check the following indicators:

- **Rework cycles**: How many times was a document sent back for revision? More than 2 revision cycles for the same document indicates a process issue.
- **Blocker resolution**: When an agent was blocked (waiting for input, unclear requirement), how quickly was the blocker identified and resolved?
- **Redundant work**: Did multiple agents work on the same task without coordination, producing duplicate or conflicting output?
- **Idle time**: Were there periods where agents waited unnecessarily because tasks were not assigned or dependencies were not communicated?

### Dimension 4: 資訊完整性 Information Completeness

Evaluate whether downstream agents received all the context they needed from upstream agents.

Check the following indicators:

- **SA completeness from PRD**: Did the SA document cover all User Stories and non-functional requirements from the PRD? Were any PRD requirements missing from the SA?
- **SD completeness from SA**: Did the SD document address all modules and data entities defined in the SA? Were any SA modules missing from the SD?
- **Test Cases completeness from PRD**: Does the 驗收測試對照表 cover every acceptance criterion in the PRD? Are there gaps?
- **Cross-document traceability**: Can every requirement be traced from PRD → SA → SD → Test Cases without breaks in the chain?

### Dimension 5: 改善機會 Missed Opportunities

Evaluate whether the team surfaced improvements, risks, or alternatives that no one else raised.

Check the following indicators:

- **Risk identification**: Were potential risks (technical, business, timeline) identified and documented during the process?
- **Alternative approaches**: Did any agent suggest a better approach that was not in the original plan?
- **Process improvements**: Did any agent identify and flag a process inefficiency during execution?
- **Knowledge gaps**: Were there areas where the team lacked expertise, and did anyone flag the need for external input?

## Retrospective Report Template

Produce the retrospective report in the following structure:

```
## Process Retrospective Report

- **Project**: {project name}
- **Review Period**: YYYY-MM-DD to YYYY-MM-DD
- **Reviewer**: {Process Reviewer agent name}
- **Report Date**: YYYY-MM-DD

### Overall Score

| Dimension | Score (1-5) | Summary |
|-----------|-------------|---------|
| 溝通品質 Inter-Agent Communication | {score} | One-sentence summary |
| 流程遵循 Workflow Adherence | {score} | One-sentence summary |
| 協作效率 Collaboration Efficiency | {score} | One-sentence summary |
| 資訊完整性 Information Completeness | {score} | One-sentence summary |
| 改善機會 Missed Opportunities | {score} | One-sentence summary |
| **Total** | **{sum}/25** | |

### Evidence and Findings

#### 溝通品質 (Score: {X}/5)

**Finding 1:** {Description of the issue or positive observation}
- **Evidence:** {Reference to specific task ID, message, or document section}
- **Impact:** {What effect this had on the project}

**Finding 2:** ...

#### 流程遵循 (Score: {X}/5)

{Same structure as above}

#### 協作效率 (Score: {X}/5)

{Same structure as above}

#### 資訊完整性 (Score: {X}/5)

{Same structure as above}

#### 改善機會 (Score: {X}/5)

{Same structure as above}

### Actionable Recommendations

| # | Recommendation | Target | Priority | Expected Benefit |
|---|---------------|--------|----------|-----------------|
| 1 | {Specific action to take} | {Which agent or process} | High/Medium/Low | {What improves} |

### Positive Highlights

- {What worked well — specific example with evidence}
- {Another positive observation}
```

## Example

### Input

Project cycle completed for "會員認證系統" (User Authentication System).

### Output (Partial — One Dimension Entry)

```
#### 溝通品質 (Score: 3/5)

**Finding 1:** Requirements Analyst handoff to System Architect was missing non-functional requirements context.
- **Evidence:** SA document initial draft (v0.1) did not include performance targets (API < 500ms) that were defined in PRD Section 7. System Architect had to request this information in a follow-up message, causing a 1-cycle delay.
- **Impact:** SA document required an additional revision cycle. Total SA completion time increased by approximately 1 turn.

**Finding 2:** System Architect to Test Case Designer handoff was thorough and well-structured.
- **Evidence:** SD document v1.0 included complete API endpoint specifications with request/response examples. Test Case Designer was able to produce API test cases (Section 5) without requesting additional information.
- **Impact:** Test Cases API section completed in a single pass with zero follow-up questions.
```

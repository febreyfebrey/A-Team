---
name: Cross Project Consistency
description: Enforce terminology and specification consistency across projects within the same company
---

# Cross Project Consistency

## Applicability

- Applies to: All agents in the requirements-docs team

## Rule Content

### Glossary Is the Single Source of Truth

Every agent must use the company glossary (`projects/{company-name}/glossary.md`) as the authoritative reference for terminology. When writing or reviewing any document:

1. Use the 偏好用法 from the glossary for every term that has an entry
2. Do not use 已棄用別名 in new documents — always use the current preferred term
3. When encountering a domain term not in the glossary, flag it to Knowledge Curator for addition before using it in a document

### Mandatory Cross-Project Scan at Project Start

Before stage ① (Interview) begins, Knowledge Curator must complete a cross-project scan and deliver a 專案背景包 to the Coordinator. The Coordinator must distribute relevant sections of this pack to execution agents.

The scan must cover:
- All projects under the same company directory
- Glossary terms related to the new project's domain
- Overlapping functionality, data entities, and API contracts

### Reference Existing Specifications

When a new project includes functionality that overlaps with an existing project:

1. System Architect must reference the existing project's SA and SD documents when producing the new SA/SD
2. Data entity names, field names, and API naming conventions must align with existing projects unless deviation is explicitly justified
3. Any intentional deviation must be documented in the SA/SD document's 限制與假設 section with: the deviation description, the reason, and the impact on existing systems

### Document Reviewer Must Check Glossary Compliance

Document Reviewer must include glossary compliance as part of every review cycle:

1. Verify all domain terms match the glossary's 偏好用法
2. Flag any 已棄用別名 still used in the document
3. Flag any new terms not yet in the glossary

Glossary non-compliance issues are classified as 中 (Medium) severity.

### Knowledge Curator Updates Glossary After Each Phase

After each phase's document receives PM confirmation, Knowledge Curator must scan the confirmed document for:

- New terms to add to the glossary
- Existing terms used in new contexts that require definition updates
- Terms that conflict with glossary definitions

## Violation Determination

- Agent uses a 已棄用別名 in a new document when the glossary has a 偏好用法 defined → Violation
- Project starts stage ① without Knowledge Curator delivering the 專案背景包 → Violation
- System Architect produces SA/SD for overlapping functionality without referencing existing project specifications → Violation
- Document Reviewer approves a document containing terms not in the glossary without flagging them for glossary addition → Violation
- Intentional deviation from existing project specifications is not documented in 限制與假設 section → Violation

## Exceptions

- If the company has zero existing projects (first project), the cross-project scan produces an empty result and Knowledge Curator focuses solely on establishing the initial glossary.

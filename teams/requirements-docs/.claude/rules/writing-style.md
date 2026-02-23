---
name: Writing Style
description: All documents must follow the team's writing style standards for clarity and readability
---

# Writing Style

## Applicability

- Applies to: All agents that produce documents in the requirements-docs team

## Rule Content

### Language

Use Traditional Chinese as the primary language for all documents.

### Technical Term Handling

On first occurrence of a technical term, write it in both Chinese and English:

- Correct: 資料庫 (Database)
- Correct: 應用程式介面 (API)

Subsequent occurrences may use either the Chinese or English form consistently within the same document.

### Sentence Length

Keep sentences under 30 characters where possible. Express one idea per sentence. Split compound sentences into separate sentences.

### Paragraph Length

Limit every paragraph to a maximum of 5 sentences. Start a new paragraph when shifting to a related but distinct point.

### Tone

Write in a factual and direct tone. You must not use marketing language or subjective adjectives such as「優異的」、「強大的」、「卓越的」、「驚人的」.

### Audience-Appropriate Language

Match the language register to the document's target audience:

| Document Type | Target Audience | Language Register |
|---------------|----------------|-------------------|
| PRD | PM, Client, Business stakeholders | Business language: focus on user value, business goals, acceptance criteria |
| SA | Developers, Technical leads | Technical language: focus on system components, data flows, integration points |
| SD | Developers | Technical language: focus on implementation details, data models, API contracts |
| Test Cases | QA engineers | Action-oriented language: focus on steps, inputs, expected results |

### Prohibited Patterns

The following patterns are prohibited in all documents:

- 「等等」without listing all items explicitly
- 「大概」
- 「應該可以」
- 「可能需要」
- 「大致上」
- 「差不多」

Replace every prohibited pattern with a specific, verifiable statement.

## Violation Determination

- SA document written in business language instead of technical language → Violation
- Paragraph containing more than 5 sentences → Violation
- Using「可能需要做資料遷移」instead of specifying whether data migration is required → Violation
- Using「等等」without enumerating all items → Violation
- Technical term appears for the first time without bilingual notation → Violation
- Document uses subjective adjectives like「強大的系統架構」→ Violation

## Exceptions

This rule has no exceptions.

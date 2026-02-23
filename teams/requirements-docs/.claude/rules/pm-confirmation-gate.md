---
name: PM Confirmation Gate
description: Every phase output must receive PM confirmation before proceeding to the next phase
---

# PM Confirmation Gate

## Applicability

- Applies to: All agents in the requirements-docs team

## Rule Content

### Phases Requiring PM Confirmation

The following phases require PM confirmation before the team proceeds to the next phase:

1. Requirements Summary (需求摘要)
2. Requirements Draft (需求初稿)
3. Full PRD (完整 PRD)
4. System Architecture (SA)
5. System Design (SD)
6. Test Cases (測試案例)

The Interview phase and Process Review phase do not require PM confirmation.

### Confirmation Flow

Every phase must follow this exact confirmation flow:

1. Executor completes the phase deliverable.
2. Document Reviewer reviews and approves the deliverable.
3. Coordinator presents the reviewed deliverable to the PM.
4. PM responds with one of: **approve** or **reject with comments**.
5. On approval, Coordinator records the confirmation and authorizes the next phase.

### Rejection Handling

On PM rejection:

1. Coordinator routes the PM's feedback to the original executor.
2. Executor revises the deliverable based on PM feedback.
3. Executor resubmits the revised deliverable to Document Reviewer.
4. Document Reviewer re-reviews and approves.
5. Coordinator presents the revised deliverable to PM again.
6. Repeat until PM approves.

No agent may begin work on the next phase during this revision cycle.

### Confirmation Recording

Coordinator must record the following for each phase confirmation:

- Phase name
- Confirmation status (approved / rejected / pending)
- PM's response summary (approval or rejection comments)
- Timestamp of PM's response
- Number of revision cycles (if any)

### No Premature Phase Advancement

No agent may begin work on the next phase until the current phase has received PM confirmation. This includes preparatory work, drafting, or research for the next phase.

## Violation Determination

- System Architect begins SA work before PRD has PM confirmation → Violation
- Coordinator skips Document Reviewer and sends the deliverable directly to PM → Violation
- PM rejects a deliverable but the executor starts the next phase anyway → Violation
- Coordinator fails to record confirmation status and timestamp → Violation
- Any agent performs preparatory work for the next phase while the current phase is pending PM confirmation → Violation

## Exceptions

This rule has no exceptions.

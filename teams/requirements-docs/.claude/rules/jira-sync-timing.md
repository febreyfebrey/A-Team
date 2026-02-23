---
name: JIRA Sync Timing
description: JIRA operations must be triggered at defined milestones only, by the Coordinator
---

# JIRA Sync Timing

## Applicability

- Applies to: All agents in the requirements-docs team (with primary enforcement on the Coordinator)

## Rule Content

### Sole Operator

Only the Coordinator triggers JIRA operations. No other agent creates, updates, or modifies JIRA tickets under any circumstance.

### Trigger Points

JIRA operations occur at exactly four milestones. Every milestone requires PM confirmation FIRST:

| # | Milestone | PM Confirmation Required | JIRA Operation |
|---|-----------|-------------------------|----------------|
| 1 | PRD confirmed | Yes | Create 1 Epic per project. Create 1 Story per User Story or functional requirement. |
| 2 | SA confirmed | Yes | Update each Story's Description field with system analysis context (components involved, integration points, constraints). |
| 3 | SD confirmed | Yes | Create Sub-tasks under relevant Stories for frontend, backend, and database tasks. |
| 4 | Test Cases confirmed | Yes | Create test-type Sub-tasks linked to corresponding Stories. |

### Standard Fields

Use only the following JIRA fields when creating or updating tickets:

- **Summary**: Concise title describing the ticket scope
- **Description**: Detailed content from the confirmed document
- **Priority**: One of Critical, High, Medium, Low
- **Labels**: Project-relevant labels for filtering and tracking

You must not use custom fields unless the PM explicitly defines them.

### Operation Logging

Every JIRA operation must be logged with the following information:

| Field | Description |
|-------|-------------|
| Operation type | Create / Update |
| Ticket IDs | All ticket IDs created or updated in this operation |
| Milestone | Which milestone triggered this operation |
| Timestamp | Date and time of the operation in YYYY-MM-DD HH:mm format |

### No Premature JIRA Operations

You must not create or update JIRA tickets before the corresponding milestone's PM confirmation is received. Preparatory drafting of ticket content is permitted, but no JIRA API calls may execute until confirmation.

## Violation Determination

- Requirements Analyst creates JIRA tickets after PRD is written but before PM confirmation → Violation
- System Architect directly creates Sub-tasks instead of going through Coordinator → Violation
- Coordinator creates Epic before PRD has PM confirmation → Violation
- JIRA operation performed without logging operation type, ticket IDs, and timestamp → Violation
- Any agent other than the Coordinator executes a JIRA API call → Violation

## Exceptions

This rule has no exceptions.

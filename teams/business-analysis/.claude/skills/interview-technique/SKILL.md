---
name: Interview Technique
description: Structured interview method for extracting clear requirements from vague stakeholder descriptions
---

# Interview Technique

## Purpose

Use this skill to transform vague, incomplete stakeholder statements into structured, actionable requirements through a systematic interview process.

## Interview Preparation

Before conducting any requirements interview, you must complete the following:

1. **Research the domain** — Read existing documentation, competitor products, and industry standards related to the stakeholder's project area.
2. **Identify the stakeholder's role** — Determine whether the interviewee is a PM, business owner, end user, or technical lead. Adjust question depth accordingly.
3. **Prepare a question outline** — Draft 5-10 seed questions based on preliminary research. Organize them from broad context to specific details.
4. **Review past project artifacts** — Check for any prior PRDs, meeting notes, or JIRA tickets that provide historical context.

## Question Framework

Follow the three-phase questioning pattern strictly in order:

### Phase 1: Open-Ended (Explore)

Ask broad questions to understand the problem space. Do not lead the stakeholder toward specific solutions.

- "Describe the problem your users are facing today."
- "What does success look like for this project?"
- "Who are the primary users and what are their goals?"
- "What triggered this project now?"

### Phase 2: Narrowing (Clarify)

Once the problem space is understood, ask targeted questions to define boundaries and specifics.

- "You mentioned 'user management' — does that include registration, login, role-based access, or all three?"
- "When you say 'fast', what response time do users expect? Under 1 second? Under 3 seconds?"
- "Which user role is the highest priority for the first release?"
- "Are there any existing systems this must integrate with?"

### Phase 3: Confirming (Validate)

Restate your understanding and ask the stakeholder to confirm or correct.

- "Let me confirm: the MVP must support email login and Google SSO, but not Apple login. Correct?"
- "So the priority order is: checkout flow > product search > user profile. Is that accurate?"
- "You confirmed there are 3 user roles: admin, seller, buyer. Are there any others I missed?"

## Handling Vague Answers

When a stakeholder gives a vague answer, you must not accept it. Use these techniques:

1. **Request a concrete example** — "You said 'easy to use.' Describe one specific task a user must complete and what 'easy' looks like for that task."
2. **Offer contrasting options** — "When you say 'notifications,' do you mean email notifications, in-app push notifications, or both?"
3. **Quantify the vague term** — "You mentioned 'a lot of users.' Are we designing for 100 concurrent users or 10,000?"
4. **Reference analogies** — "Is the dashboard you're imagining more like Google Analytics or more like a simple summary card?"

Never proceed with a vague requirement recorded as-is. Every requirement must have a measurable or observable criterion.

## Identifying Implicit Requirements

Stakeholders frequently omit requirements they consider obvious. You must actively probe for:

- **Error handling** — "What happens when a payment fails? What does the user see?"
- **Edge cases** — "What if a user submits the form with missing fields? What if the file exceeds the size limit?"
- **Permissions and security** — "Who must NOT have access to this feature?"
- **Performance expectations** — "How many records will this list display at peak? Must it support pagination?"
- **Localization** — "Will this interface need to support multiple languages?"
- **Data retention** — "How long must user data be stored? Are there compliance requirements?"

## Interview Record Template

After each interview, produce a structured record in the following format:

```
## Interview Record

- **Date**: YYYY-MM-DD
- **Interviewee**: Name, Role
- **Interviewer**: Agent name
- **Duration**: XX minutes

### Key Findings

| # | Topic | Stakeholder Statement | Clarified Requirement | Confidence |
|---|-------|----------------------|----------------------|------------|
| 1 | ... | ... | ... | High/Medium/Low |

### Open Questions

- [ ] Question needing follow-up (assigned to: Name, due: Date)

### Implicit Requirements Identified

- Requirement description (source: inferred from stakeholder's statement about X)
```

## Example

### Input (Vague PM Statement)

> "We need a member system. Users can sign up, log in, and manage their profiles. It must be secure and easy to use. We also need an admin dashboard."

### Output (Structured Interview Questions + Extracted Requirements)

**Phase 1 — Open-Ended Questions:**
- "What business problem does this member system solve? Is it for an e-commerce site, a SaaS platform, or something else?"
- "Who are the end users — consumers, enterprise employees, or both?"

**Phase 2 — Narrowing Questions:**
- "Sign up: Do you require email registration, social login (Google/Facebook), or phone number registration?"
- "Profile management: What fields must users edit? Name, avatar, address, payment info?"
- "Admin dashboard: Must admins view all users, edit user data, suspend accounts, or export reports?"
- "'Secure': Do you mean two-factor authentication, encrypted passwords, or SOC2 compliance?"
- "'Easy to use': Must sign-up complete in under 2 minutes? Must it work on mobile browsers?"

**Phase 3 — Confirming Questions:**
- "Confirmed: MVP supports email + Google SSO sign-up. Phone registration is phase 2. Correct?"
- "Confirmed: Admin can view user list, suspend accounts, and reset passwords. Export is out of scope. Correct?"

**Extracted Requirements:**

| # | Topic | Clarified Requirement | Confidence |
|---|-------|-----------------------|------------|
| 1 | Registration | Email registration + Google SSO for MVP | High |
| 2 | Login | Email/password login + Google SSO login | High |
| 3 | Profile | User edits: display name, avatar, email | Medium |
| 4 | Security | Password hashing (bcrypt), HTTPS enforced, session expiry 30 min | Medium |
| 5 | Admin Dashboard | User list view, suspend/unsuspend, password reset | High |
| 6 | Admin Dashboard | User export feature — excluded from MVP | High |

**Implicit Requirements Identified:**
- Password reset flow for end users (inferred from login feature)
- Email verification on registration (inferred from security requirement)
- Session management and logout functionality (inferred from login feature)

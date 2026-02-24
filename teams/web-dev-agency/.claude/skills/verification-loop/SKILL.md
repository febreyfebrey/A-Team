---
name: Verification Loop
description: Execute a six-phase verification system to ensure code quality before every merge and deployment
---

# Verification Loop

## Purpose

Run all six phases in sequence after every significant code change. Do not skip any phase. A single failure blocks the merge until resolved.

## Trigger Conditions

Run the full verification loop before every pull request, before every deployment, after every 15 minutes of continuous development, and after any major refactoring.

## Phase 1: Build Verification

**Command:** `npm run build`

Pass when exit code is 0 with no warnings treated as errors and bundle size within budget.

## Phase 2: Type Check

**Command:** `npx tsc --noEmit`

Pass when exit code is 0 with zero type errors.

## Phase 3: Lint Check

**Command:** `npx eslint . --ext .ts,.tsx`

Pass when exit code is 0 with zero errors. Track warnings separately.

## Phase 4: Test Suite

**Command:** `npm test -- --coverage`

Pass when all tests pass and coverage meets minimums: 80% overall, 100% for `services/` and `lib/`.

## Phase 5: Security Scan

Run these checks:
- Hardcoded secrets: `grep -rn "password\s*=\s*['\"]" src/ --include="*.ts" --include="*.tsx"`
- Console.log in production: `grep -rn "console\.log" src/ --include="*.ts" --include="*.tsx" | grep -v "__tests__" | grep -v ".test." | grep -v ".spec."`
- Exposed env vars: `grep -rn "NEXT_PUBLIC_.*SECRET\|NEXT_PUBLIC_.*PASSWORD\|NEXT_PUBLIC_.*KEY" src/ .env*`

Pass when all three return zero matches.

## Phase 6: Diff Review

**Command:** `git diff --stat && git diff`

Verify: all changed files relate to the current task, no debug code or TODO comments remain, every new async operation has error handling, new user inputs have validation, new API routes have auth checks.

## Output Format

```
============================================
  VERIFICATION REPORT
  Date: {ISO timestamp}
  Branch: {current branch}
  Trigger: {manual | pre-merge | scheduled}
============================================

Phase 1: Build Verification      [PASS/FAIL]
  Duration: {seconds}s

Phase 2: Type Check               [PASS/FAIL]
  Errors: {count}

Phase 3: Lint Check               [PASS/FAIL]
  Errors: {count} | Warnings: {count}

Phase 4: Test Suite               [PASS/FAIL]
  Tests: {passed}/{total} | Coverage: {percentage}%

Phase 5: Security Scan            [PASS/FAIL]
  Secrets: {count} | Console.log: {count} | Exposed env: {count}

Phase 6: Diff Review              [PASS/FAIL]
  Files changed: {count} | +{insertions} -{deletions}

============================================
  RESULT: {ALL PASS | BLOCKED}
  Blocking phases: {list of failed phases}
============================================
```

## Continuous Checkpoint Protocol

During active development, run a lightweight checkpoint every 15 minutes:

1. Save all files
2. Run Phase 2 (Type Check) -- catches type drift early
3. Run Phase 4 (Test Suite) -- catches regressions immediately
4. If either fails, stop feature work and fix before continuing

Run the full six-phase loop before committing.

## Example

```
============================================
  VERIFICATION REPORT
  Date: 2025-01-15T14:30:00Z
  Branch: feature/user-profile
  Trigger: pre-merge
============================================

Phase 1: Build Verification      [PASS]
  Duration: 28s

Phase 2: Type Check               [PASS]
  Errors: 0

Phase 3: Lint Check               [PASS]
  Errors: 0 | Warnings: 3

Phase 4: Test Suite               [PASS]
  Tests: 142/142 | Coverage: 87%

Phase 5: Security Scan            [FAIL]
  Secrets: 0 | Console.log: 2 | Exposed env: 0

Phase 6: Diff Review              [PASS]
  Files changed: 8 | +234 -56

============================================
  RESULT: BLOCKED
  Blocking phases: Phase 5 (Security Scan)
============================================
```

**Action:** Remove the two `console.log` statements in `avatar.ts`, then re-run the full verification loop.

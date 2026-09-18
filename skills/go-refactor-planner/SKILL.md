---
name: go-refactor-planner
description: >
  Generate a structured refactoring plan for Go source files based on audit findings.
  Triggers exclusively on the prefix "go-refactor-plan:" followed by a file path or audit report path.
  Example: "go-refactor-plan: documents/audit/audit-otp_mobile_request_msisdn.md".
  Can also accept a Go file path directly without an audit report.
  Always produces a prioritized, dependency-aware task plan saved as a .md file.
  Run go-arch-auditor first if no audit report is available.
---

# go-refactor-planner

Generate a structured, prioritized, dependency-aware refactoring plan from either:
- An audit report produced by `go-arch-auditor`
- A Go source file directly (skill will derive violations inline before planning)

Output is saved as a `.md` file to the directory specified by the user.

---

## Required Input

Collect the following before starting. If any is missing, ask the user first.

| Input | Description | Example |
|---|---|---|
| Audit report path | `.md` file from `go-arch-auditor` | `documents/audit/audit-otp_mobile_request_msisdn.md` |
| OR Go file path | If no audit report available | `services/internal/delivery/grpc_server/otp_handler.go` |
| Project root | Root path of the project | `services/` |
| Output directory | Where to save the plan | `documents/refactor/` |

Output filename: `refactor-plan-<filename>.md`
Example: `refactor-plan-otp_mobile_request_msisdn.md`

---

## Step 1 — Load Input

**If audit report is provided:**
- Use `view` tool to read the audit report
- Extract all violations, their severity, locations, and recommendations

**If Go file is provided without audit report:**
- Use `view` tool to read the Go file
- Derive violations inline using the same checklist as `go-arch-auditor`
- Proceed to planning with derived violations

---

## Step 2 — Analyze Dependencies Between Tasks

Before assigning phases, map out which tasks block others.

Rules for dependency mapping:

| Condition | Dependency |
|---|---|
| Task B requires code that Task A creates | Task B depends on Task A |
| Two tasks touch the same function/struct | Run sequentially, not in parallel |
| Task moves logic to a new file/layer | All tasks that use that logic depend on it |
| Task is a pure extraction with no downstream impact | No dependency — can run independently |

Common dependency chains in Go refactoring:

```
Create Value Object → Use Value Object in UseCase → Remove validation from Handler
Move logic to UseCase → Remove logic from Handler → Slim down Handler
Define error in pkg/errorcodes → Replace inline errors everywhere
Extract dispatcher → Remove action if-else from Handler
```

---

## Step 3 — Classify Tasks by Urgency

Assign each task to a phase based on urgency and dependency position:

### Phase 1 — Urgent
Criteria:
- `HIGH` severity violations
- Tasks that are dependencies for other tasks (blockers)
- Correctness bugs (ignored errors, silent failures)
- Security concerns (token/credential handling)

### Phase 2 — Important
Criteria:
- `MEDIUM` severity violations
- Tasks that improve maintainability and testability
- Tasks that depend on Phase 1 completions

### Phase 3 — Nice to Have
Criteria:
- `LOW` severity violations
- Style and convention improvements
- Tasks with no downstream impact

---

## Step 4 — Assign Effort per Task

Use this scale:

| Size | Description |
|---|---|
| S | Simple change — rename, extract helper, move constant. Under 1 hour. |
| M | Moderate change — create new file/struct, update call sites. Half day. |
| L | Large change — new layer component, multiple files affected, requires tests. 1+ day. |

---

## Step 5 — Write Refactor Plan

Save the plan to the output directory as `refactor-plan-<filename>.md`.

Use this format:

```markdown
# Refactor Plan: [filename]

**Source:** [audit report path or Go file path]
**Project root:** [root path]
**Generated at:** [date]

---

## Overview

| Phase | Tasks | Effort |
|---|---|---|
| Phase 1 — Urgent | X | S/M/L breakdown |
| Phase 2 — Important | X | S/M/L breakdown |
| Phase 3 — Nice to Have | X | S/M/L breakdown |
| **Total** | **X** | |

---

## Dependency Graph

```
[Task A] ← no dependency, run first
[Task B] ← depends on Task A
[Task C] ← depends on Task A
[Task D] ← depends on Task B and Task C
[Task E] ← no dependency, can run in parallel with Task A
```

---

## Phase 1 — Urgent

### Task 1.1 — Fix Ignored Error from HashPassword
**Violation:** E5 — Error discarded with `_`
**Location:** `MobilePhoneOtpRequest()` — lines 171, 192
**Effort:** S
**Dependency:** None — fix this first, other tasks depend on clean error handling
**Why urgent:** Silent failure — if hashing fails, token is empty and request continues undetected

**Steps:**
1. Replace `reqData.Token, _ = utils.HashPassword(...)` with explicit error handling
2. Return error via `exceptions.MapToGrpcStatusCodeErr(err)` if hashing fails

---

### Task 1.2 — Create TselEmail Value Object
**Violation:** A1, A5, B1, B2
**Location:** New file — `services/domain/valueobject/tsel_email.go`
**Effort:** M
**Dependency:** None — foundational, unblocks Task 1.3 and Task 2.1
**Why urgent:** Business rule currently leaks into delivery layer; blocks all email-path cleanup

**Steps:**
1. Create `services/domain/valueobject/tsel_email.go`
2. Implement `TselEmail` type with `NewTselEmail(raw string) (TselEmail, error)`
3. Move `strings.Contains(email, "telkomsel.co.id")` validation into constructor
4. Write unit test: `services/domain/valueobject/tsel_email_test.go`

---

[continue per task]

---

## Phase 2 — Important

### Task 2.1 — Remove Email Domain Validation from Handler
**Violation:** A1, A5
**Location:** `MobilePhoneOtpRequest()` — lines 51–58
**Effort:** S
**Dependency:** Task 1.2 (TselEmail Value Object must exist first)
**Why important:** Cleans delivery layer after domain rule is properly encapsulated

**Steps:**
1. Replace inline domain check with `valueobject.NewTselEmail(email)`
2. Remove `if !strings.Contains(...)` block from handler

---

[continue per task]

---

## Phase 3 — Nice to Have

### Task 3.1 — Rename Package from grpc_server to grpcserver
**Violation:** E6
**Location:** `package grpc_server` — line 1
**Effort:** S
**Dependency:** None
**Why deferred:** Convention improvement only, no functional impact

**Steps:**
1. Rename `package grpc_server` to `package grpcserver`
2. Update all import paths referencing this package

---

[continue per task]

---

## Parallel Execution Map

Tasks that can be worked on simultaneously (no shared dependency):

| Parallel Group | Tasks |
|---|---|
| Group A | Task 1.1, Task 1.2 |
| Group B | Task 2.2, Task 2.3 (after Group A completes) |
| Group C | Task 3.1, Task 3.2 (independent, anytime) |

---

## Notes

[Any clarifications, risks, or items flagged from "Notes on Other Files" in the audit report
that affect this plan but are out of scope for this file]
```

---

## Additional Rules

- If no audit report is provided and violations are derived inline, note this in the plan header: `Source: derived from file (no audit report)`.
- Do not create tasks for violations that are already compliant in the audit report.
- If a violation has multiple rule codes (e.g. A1, A5, B1 all point to the same root cause), merge them into one task — do not create duplicate tasks.
- Always check "Notes on Other Files" section in the audit report — if a dependency exists in another file, flag it in the task's dependency field.
- Effort size applies to the task in isolation. If a task requires changes in multiple files, size up accordingly (S → M or M → L).
- If output directory is not provided, ask before saving.

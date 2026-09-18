---
name: go-arch-auditor
description: >
  Audit Go source files against team coding standards: Clean Architecture, DDD tactical design,
  SOLID, DRY, and Go conventions. Triggers exclusively on the prefix "go-arch-audit:" followed
  by a file path. Example: "go-arch-audit: services/domain/grpc_server/otp_handler.go".
  Always run this skill before go-refactor-planner.
---

# go-arch-auditor

Audit Go source files against team standards and architecture principles. Produces a structured
audit report saved as a `.md` file, ready to be used as input for `go-refactor-planner`.

Reference standards:
- **Team coding standards** (BE_Coding_Standards.md)
- **Clean Architecture** — Robert Cecil Martin (Uncle Bob)
- **DDD Tactical Design** — Vaughn Vernon (Implementing Domain-Driven Design)
- **SOLID Principles**
- **DRY** — Don't Repeat Yourself

---

## Required Input

Collect the following before starting the audit. If any is missing, ask the user first.

| Input | Description | Example |
|---|---|---|
| File path(s) | Go file(s) to audit | `services/domain/grpc_server/otp_handler.go` |
| Project root | Root path of the project | `services/` or `go-auth/` |
| Output directory | Where to save the audit report | `documents/audit/` |

Output filename: `audit-<filename>.md` (example: `audit-otp_handler.md`)

---

## Step 1 — Read the File

Use the `view` tool to read the file content. If the file is not found, ask the user to confirm
the path. Do not guess or fabricate file contents.

---

## Step 2 — Detect Legacy vs New Project

Before running the audit checklist, detect whether the project follows the standard structure.

**Standard folder structure:**
```
domain/
  aggregate/
  enum/
  entity/
  event/
  valueobject/
  repository/       ← interfaces only, not implementations
  service/
application/
  usecase/
  repository/
infrastructure/
  repository/       ← concrete implementations
  service/
delivery/           ← HTTP, gRPC handlers
pkg/
  errorcodes/
    code.go
    message.go
```

**Legacy indicators** (any one is sufficient):
- Delivery layer uses non-standard names (`grpc_server`, `handler`, `controller`)
- No `domain/valueobject` or `domain/entity` folder
- Errors use inline `errors.New()` instead of `pkg/errorcodes`
- Business logic exists inside handler/controller functions

If legacy is detected → add `[LEGACY]` label to relevant violations and provide
**two path recommendations**: one for standard structure, one adjusted to existing structure.

---

## Step 3 — Run Audit Checklist

For each violation found, record:
- **Rule code** (e.g. `A1`, `B2`)
- **Location**: function name or code block
- **Severity**: `HIGH` / `MEDIUM` / `LOW`
- **Label**: `[LEGACY]` if related to folder structure mismatch
- **Problem**: why this is a violation
- **Recommendation**: what to do, with path adjusted to project structure

---

### A. Clean Architecture — Layer Integrity

| Code | Rule | Check |
|---|---|---|
| A1 | Business logic must not exist in delivery layer | Is there domain branching, business conditions, or rule-based logic inside a controller/handler? |
| A2 | Dependencies must point inward: domain ← application ← infrastructure ← delivery | Does the delivery layer contain logic that belongs deeper? |
| A3 | Controller responsibility: parse input → call usecase → map response only | Does the controller do anything outside these three? |
| A4 | Token generation or hashing must not exist in controller | Look for `HashPassword`, `GenerateUUID`, or token calculation inside handler |
| A5 | Domain validation must not exist in controller | Look for business rule string checks (email domain, MSISDN format) inside handler |
| A6 | Error handling must not be manually repeated per function | Look for `if err != nil { log; return }` pattern repeated more than 2x in one function |

---

### B. DDD Tactical Design

| Code | Rule | Check |
|---|---|---|
| B1 | Value Objects must exist for important domain types | Are types like `Email`, `Msisdn`, `Token`, `PhoneNumber` represented as plain `string`? |
| B2 | Value Object constructor must exist with business validation | Is there `NewEmail()`, `NewMsisdn()` with validation and error handling? |
| B3 | Business rules must live inside Value Object or Entity | Is validation like email domain whitelist or MSISDN format outside the domain layer? |
| B4 | Entity must have unique identity and lifecycle | Does the domain entity struct have an ID and lifecycle methods? |
| B5 | Aggregate Root must be the only entry point for domain state changes | Is domain state being mutated directly from usecase or delivery layer? |
| B6 | Repository interface must be in domain layer, implementation in infrastructure | Is the repository interface defined outside `domain/repository`? |

---

### C. SOLID Principles

| Code | Rule | Check |
|---|---|---|
| C1 | **S** — Single Responsibility: one struct/function has one reason to change | Does any function mix parse + validate + business logic + response mapping? |
| C2 | **O** — Open/Closed: open for extension, closed for modification | Is there a large if-else or switch on action/type that requires modification for every new case? |
| C3 | **L** — Liskov Substitution: implementations must honor interface contracts | Does any implementation change the behavior contract (inconsistent errors, different return semantics)? |
| C4 | **I** — Interface Segregation: interfaces must not be too large | Is there an interface with many methods where not all are used by every implementation? |
| C5 | **D** — Dependency Inversion: depend on abstraction, not concretion | Does any struct directly instantiate its dependencies instead of receiving them via constructor injection? |

---

### D. DRY — Don't Repeat Yourself

| Code | Rule | Check |
|---|---|---|
| D1 | No duplicated logic | Is there an identical or near-identical code block repeated more than once? |
| D2 | No duplicated error handling pattern | Is the same `if err != nil { ... return }` pattern copy-pasted across the function? |
| D3 | No duplicated response mapping | Is the response builder written multiple times across branches? |

---

### E. Go Conventions (Team Standards)

| Code | Rule | Check |
|---|---|---|
| E1 | Private functions must be declared before public functions in the same file | Check function declaration order |
| E2 | Constructor (`New*`) must be declared after all methods in the same file | Check position of `New*()` functions |
| E3 | Errors must use defined errors from `pkg/errorcodes`, not inline `errors.New()` | Look for `errors.New(` outside `pkg/errorcodes` files |
| E4 | Variables and constants must be grouped with `var()` or `const()` | Look for ungrouped `var x = ...` declarations |
| E5 | Errors must not be discarded with `_` | Look for `_, _ =` or `_` assigned to error values |
| E6 | Package name: lowercase, single-word, no underscores or mixedCaps | Check `package ...` declaration |
| E7 | Struct and its methods must be in the same file | Are any methods separated from their struct definition? |

---

### F. Testing

| Code | Rule | Check |
|---|---|---|
| F1 | Unit tests are mandatory for domain layer, minimum 80% coverage | Is there a `*_test.go` file for the entity/valueobject being audited? |
| F2 | Test file must be in the same package as the file under test | Check test file location and package name |
| F3 | Test data must be in `test/testdata`, not hardcoded inside test functions | Are there literal test values inside test functions? |
| F4 | Each test must be isolated with fresh data | Is there shared state between tests that could cause test pollution? |

---

### G. Observability

| Code | Rule | Check |
|---|---|---|
| G1 | Logger must use `logmanager` from salt-pkg | Is there any other logger import (logrus, log, zap)? |
| G2 | Sensitive data must not be logged (token, password, OTP) | Check `AddTags` or log statements that might expose sensitive values |
| G3 | gRPC: `traceparent` must be injected/extracted via interceptor, not manually in handler | Is there manual trace handling inside the handler? |

---

## Step 4 — Write Audit Report

Save the report to the output directory provided by the user as `audit-<filename>.md`.

Use this format:

```markdown
# Audit Report: [filename]

**File:** [full file path]
**Project root:** [root path]
**Project type:** New Project / Legacy Project
**Audited at:** [date]

---

## Summary

| Severity | Count |
|---|---|
| HIGH | X |
| MEDIUM | X |
| LOW | X |
| **Total** | **X** |

**Key finding:** [1-2 sentence summary of the biggest problem]

---

## Violations

### [A1] Business Logic in Delivery Layer — HIGH [LEGACY]

**Location:** `MobilePhoneOtpRequest()` — email domain validation
**Problem:** `strings.Contains(email, "telkomsel.co.id")` is a business rule.
It must live in a Value Object, not in a gRPC handler.
**Recommendation:**
- Standard structure → create `domain/valueobject/tsel_email.go` with `NewTselEmail(raw string) (TselEmail, error)`
- Legacy structure → create `services/domain/valueobject/tsel_email.go`

---

[continue per violation]

---

## Compliant

- [list rules with no violations found]

---

## Notes on Other Files

[If violations relate to files outside the audit scope, flag them here.
Do not recommend changes to files not provided by the user.]

---

## Next Step

Run `go-refactor-planner` using this audit report as input.
```

---

## Additional Rules

- If a file cannot be read, ask the user to fix the path. Do not fabricate contents.
- If the user pastes code without a path, audit proceeds but record `path: unknown` in the report header.
- Do not recommend changes outside the scope of the provided file(s). Flag them under "Notes on Other Files".
- If no output directory is provided, ask before saving.
- Severity guide:
  - `HIGH` — Layer dependency violation, business logic in controller, ignored errors, missing interface
  - `MEDIUM` — Missing Value Object, inline error definition, SOLID violation, duplicated logic
  - `LOW` — Function ordering, ungrouped variables, minor naming convention

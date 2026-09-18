---
name: flow-scanner
description: >
  Reads one or more Go flow files (controller, usecase, service, repository, etc.)
  and produces structured flow documentation: summary, tree diagram, request/response,
  dependency table, and important notes.
  Use this skill when the user wants to understand the behavior of a flow from a Go codebase,
  before creating a delta report or technical design.
  Trigger phrases: "scan flow", "document this flow", "read flow", "analyze flow",
  "explain this flow", "scan this controller", "scan this usecase", or when the user
  provides a Go file path and wants to understand its behavior.
  If CLAUDE.md or AGENTS.md exists in the repository, read it first as project context
  before analyzing the flow.
---

# Flow Scanner

Reads Go flow files and produces structured documentation describing the complete behavior of a flow.

## Input

- **Flow files** (required): one or more files — controller, usecase, service, repository, etc.
- **CLAUDE.md / AGENTS.md** (optional): read as project context (layer conventions, naming, known services)
- **Output path** (from prompt): where to save the documentation

If CLAUDE.md or AGENTS.md is available in the repository, read it before analyzing the flow.

## Output

One markdown file saved to the path specified by the user in the prompt.
If no output path is specified, save to the same directory as the input file.

Use a descriptive filename, e.g.: `flow-create-user-survey-mobile.md`

---

## Output Template

ALWAYS use this structure:

```
# Flow: [Flow Name]
[gRPC/HTTP] [endpoint] — [FunctionName()]
File: [path/to/file.go]

---

## Summary
[3–5 sentences: what this flow does, who calls it,
success condition, and the most important thing a new reader should know]

---

## Full Flow
[ASCII tree diagram showing execution order from entry point to response]

[FunctionName(ctx, r)]
  │
  ├─ [STEP] description
  │    └─ detail
  │
  └─ [STEP] description

---

## Request
| Parameter | Type | Source | Description |
|-----------|------|--------|-------------|
| [param]   | [type] | [source] | [description] |

## Response
| Status | Condition |
|--------|-----------|
| [status] | [condition] |

Success response format:
[response struct if available]

---

## Dependencies
| Component | File |
|-----------|------|
| [component name] | [path/file.go or UNKNOWN if not traced] |

---

## Important Notes
[Numbered. Non-obvious things that matter: async vs sync behavior and implications,
data inconsistency risk, race conditions, implementation quirks, architectural decisions
that are not visible from the tree diagram]
```

---

## How It Works

### Step 1: Read project context
If CLAUDE.md or AGENTS.md exists, read it first. Note:
- Layer and naming conventions used by the project
- Known external services (go-auth, go-pkg, etc.)
- Established patterns in this project

### Step 2: Read flow files
Read all files provided by the user. If only a controller is given,
trace its dependencies as far as needed to understand the full behavior.

### Step 3: Write Summary
3–5 sentences answering: what is this flow, who calls it, success condition, and the single most important thing to know.

### Step 4: Build Tree Diagram
Draw execution order as an ASCII tree. Use these labels:
- `[PRE-CHECK]` for early validation
- `[FEATURE FLAG]` for conditional features
- `[BRANCHING]` for logic branches
- `[PATH A/B/...]` for alternative flows
- `[DB]` for database calls
- `[gRPC]` for gRPC calls
- `[ASYNC goroutine]` for async execution
- `[SYNC]` for synchronous calls worth highlighting

### Step 5: Document Request & Response
Extract from proto or request/response structs. Mark the source of each parameter (body, metadata, header).

### Step 6: Build Dependency Table
List all external components called. If the file path is unknown, write `UNKNOWN` — do not skip it.

### Step 7: Write Important Notes
Things not visible from the tree diagram:
- Async vs sync behavior and its implications
- Potential data inconsistency or race conditions
- Implementation quirks (typos, commented-out code, etc.)
- Architectural decisions relevant before modifying this flow

### Step 8: Save output
Save to the path specified by the user in the prompt.

## Rules

- **All output must be written in English** — regardless of the language used in the conversation
- Focus on **behavior**, not line-by-line code explanation
- If there are multiple paths (A/B), document all paths separately
- Dependencies with `UNKNOWN` path must still be listed
- Do not write recommendations or technical design — that is out of scope for this skill

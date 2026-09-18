---
name: spec-delta-analyzer
description: >
  Analyzes the delta between two markdown documents — typically an existing flow/journey doc
  and a new API contract — and produces a structured delta-report.md.
  Use this skill whenever the user uploads or references two .md files and wants to understand
  what needs to change, what can be reused, and what is net-new. Trigger phrases include:
  "analyze delta", "buat delta report", "apa yang berubah", "compare these two docs",
  "what changed between", or any time the user has an existing-flow doc + an api-contract doc
  and wants to plan implementation. Always use this skill before running engineering-tripack-generator.
---

# Spec Delta Analyzer

Compares two markdown documents and produces a structured `delta-report.md`.
The delta-report is designed as the standard input for the `engineering-tripack-generator` skill.

## Input

Two markdown files:
1. **Existing Flow Document** — the current journey or flow (may have been adjusted before)
2. **API Contract Document** — the spec describing what needs to change or be added

Both files must be available in context before running this skill.

## Output

File `delta-report.md` saved to the output path specified by the user in the prompt.
If no output path is specified, save to the same directory as the input files.

ALWAYS use this template:

---

```
# Delta Report: [feature name]
Generated: [date]
Source: [existing file name] → [contract file name]

## 1. Change Type
<!-- Mark one -->
- [ ] Additive only (no breaking change)
- [ ] Breaking change (affects existing behavior)
- [ ] Mixed (partly additive, partly breaking)

Justification: [why]

## 2. What Changed
### New
- [item that does not exist in existing]

### Modified
- [item that exists in existing but changed in contract]

### Removed
- [item that exists in existing but absent in contract]

## 3. Impact Analysis
| Layer | Impact | Notes |
|-------|--------|-------|
| Transport/Handler | New / Modified / None | ... |
| Use Case | New / Modified / Reused as-is / None | ... |
| Service | New / Modified / Reused as-is / None | ... |
| Repository | New / Modified / Reused as-is / None | ... |
| Proto/Contract | New / Modified / None | ... |

## 4. Reuse Map
| Component | Status | Notes |
|-----------|--------|-------|
| [function/struct/service name] | Reused as-is / Needs wrapping / Replace | ... |

## 5. Net-New Inventory
- [ ] [net-new item] — Layer: [layer] — Reason: [why net-new]

## 6. Open Questions
- [ ] [question] — Blocking: [yes/no]

## 7. Recommendation
[One paragraph: is it safe to proceed to tripack generator, or are there blocking open questions?]
```

---

## How It Works

### Step 1: Read both documents
Read the existing flow doc and the API contract thoroughly. Understand:
- What the existing flow does end-to-end
- What the contract is asking for — new endpoint, request/response changes, new behavior

### Step 2: Identify change type
Determine whether the change is additive, breaking, or mixed.
- **Additive**: contract only adds something new, existing behavior untouched
- **Breaking**: contract changes an existing interface or behavior
- **Mixed**: both

### Step 3: Fill each section
Fill sections 2–6 in order. Never skip a section even if empty — write "None" explicitly.

### Step 4: Write recommendation
One paragraph: is this delta safe to pass to engineering-tripack-generator,
or are there blocking open questions that must be answered first?

### Step 5: Save output
Save to the path specified by the user in the prompt.
If no path is given, save to the same directory as the input files.

## Rules

- **All output must be written in English** — regardless of the language used in the conversation
- Do not start implementation or technical design — that is the job of engineering-tripack-generator
- If one of the files is not available, ask the user to provide it before proceeding
- Impact Analysis uses these layers: Transport, Use Case, Service, Repository, Proto — adjust if the project uses different conventions
- Blocking open questions must be resolved before passing the delta-report to tripack generator

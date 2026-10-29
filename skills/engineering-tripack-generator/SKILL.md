---
name: engineering-tripack-generator
description: >
  Generate or revise technical-design.md, tactical-strategy.md, and task-plan.md
  for new features, existing-feature adjustments, or tripack revisions after
  requirement changes or design/implementation findings with unchanged requirements.
  Assess design before deriving strategy and tasks; add decision-log.md only for
  business-driven technical decisions. Stack-agnostic: discover context from applicable
  project instructions (including AGENTS.md and CLAUDE.md) and verified code.
  Accept PRD, FSD, analysis, or direct descriptions. Use for "generate tripack",
  "buat technical design", "buatkan task plan", "generate implementation plan",
  "update tripack", "revisi technical design", or "re-sync doc".
  Exclude small bug fixes, document reviews without generation, and general architecture questions.
---

# Engineering Tripack Generator

Generates or revises three standard technical documents for planning feature work in any
software project. Output is designed for direct use by engineers and can be reviewed by
a tech lead or PM.

The skill is **stack-agnostic**: it makes no assumptions about language, framework,
architecture, or tooling. Everything project-specific is discovered from applicable
project instructions and the codebase (see Project Context Discovery), and the output
templates adapt to what is found there.

The tripack records binding contracts, current design choices, and remaining work.
Tasks reference technical-design sections for verification. Internal structure can
evolve within the authority described below; synchronize material findings so later
tasks use the current assessed design.

## Project Context Discovery

Before generating anything, discover the actual project context. **Never fill
architecture, reuse, or schema details from assumption.** This step is mandatory
in every mode.

1. Read available, applicable project instructions, including `AGENTS.md` and
   `CLAUDE.md` at the root and instructions scoped to affected directories. Check
   their scope and precedence; neither filename automatically replaces the other.
   Extract:
   - Tech stack (language, framework, database, API style — REST/gRPC/GraphQL/etc.)
   - Directory structure and where each kind of code lives
   - Code behavior conventions, patterns, and project-specific rules
   - Tooling (migration tool, codegen, test commands, lint)
2. Verify and complete the picture from the codebase: package/module structure,
   one or two similar existing features/flows as pattern references, interface
   definition files (proto/OpenAPI/GraphQL schema/route definitions), and the
   migrations directory if one exists. For reference flows, explain which parts
   fit the new need and which limitations matter. Separate mandatory conventions
   from incidental existing structure; assessing suitability does not authorize
   unrelated legacy cleanup.
3. Derive the project's **layer/component names** from what you find (e.g.
   handler/service/repository, controller/model/view, or whatever the project uses).
   Use the project's own vocabulary in all output documents — never impose a
   layer structure the project doesn't have.
4. Ask only about material information still missing or unresolved conflicts after
   discovery. A missing instruction file alone is not a blocker. Discovery is done
   when the affected components, applicable constraints, usable references, and
   remaining material uncertainties are identified and grounded in available sources.

## Input

Any of the following, in any combination:

- PRD or FSD from the product team
- An analysis document produced earlier in the conversation or provided as a file
- A direct feature/change description from the user
- For Mode C: the existing tripack files (required) plus the requirement change or
  design/implementation finding

Discover constraints (e.g. additive only, no DB change, no domain entity change) from
the input and applicable instructions. Clarify material gaps or conflicts before
finalizing affected work; keep independent work moving with uncertainties visible.

## Modes

Detect the mode from the input and the user's intent. If ambiguous, ask.

### Mode A — New Feature
No related implementation exists yet. Generate a fresh tripack.

### Mode B — Adjustment to an Existing Feature
The feature exists in the codebase, but no tripack (or none is provided).
Before generating, perform an **impact analysis** against the codebase:

- Identify affected components per layer (what is Modified vs Reused as-is)
- Identify affected entities/schema and whether migration is needed
- Identify backward-compatibility risks (API contract, consumers, data)
- Build the reuse map from actual code, not assumption

Then generate a fresh tripack that incorporates the impact analysis.

### Mode C — Revision of an Existing Tripack
A tripack exists and requirements changed, or design/implementation findings justify
revision while requirements remain unchanged. Implementation may be partially done.
**Update, do not regenerate from scratch.** Steps:

1. **Baseline** — compare the existing tripack against the current code state.
   Classify: implemented-and-conforming / implemented-but-deviating /
   not-yet-implemented / code-not-covered-by-design. Keep this coverage/progress
   classification separate from assessment of each difference: **contract violation**,
   **valid structural refinement**, or **unresolved decision**. Use code and available
   verification evidence; clarify material progress uncertainty rather than inferring
   completion from code presence alone.
2. **Impact analysis** — map the change or finding onto the tripack: which design sections
   change, which entities/schema are affected (migration from the *current actual*
   schema, not the originally planned one), which completed work becomes invalid,
   and whether it contradicts earlier binding decisions. Apply Design Authority
   below: resolve conflicts with binding contracts/constraints through the relevant
   decision owner; ordinary authorized internal refinements need no new approval.
3. **Draft and assess technical-design.md** — use the assessment step in How It Works.
   Before planning reconciliation, decide whether **code, documents, or both** need
   to change and explain why. The resulting doc must be fully self-consistent:
   a new reader should understand the current design without knowing its history.
   Record the change in the **Revision History** section (what changed, why).
4. **Update tactical-strategy.md** — adjust reuse map, net-new inventory, testing
   and rollout/rollback as needed.
5. **Update task-plan.md for remaining work** —
   - Preserve completed tasks and their `[x]` status if still valid
   - Mark invalidated tasks as `~~obsolete~~` with a one-line reason (do not delete)
   - Preserve task IDs; allocate new IDs without renumbering existing tasks
   - Add code/schema reconciliation only when implementation actually needs to change;
     a documents-only refinement does not require a code-repair task
   - Place reconciliation before tasks that depend on its result; unrelated work can proceed
   - Mark tasks affected by unresolved decisions as blocked, with the decision and
     readiness condition; a deferred design is not executable for its dependents
   - Preserve Revision History and decision history, including superseded entries

## Design Authority and Handoff

Distinguish these in the technical design:

- **Binding contracts:** business rules, accepted inputs/states, normalization, errors
  and their ordering, failure timing, side effects, lifecycle, and caller/data compatibility.
  Changes require a resolved decision appropriate to their impact and existing authority.
- **Internal structure:** components, helpers, types, and placement may be refined
  within scope when the executor explains the benefit and supplies evidence that
  binding behavior remains preserved. A structural difference alone is not a defect.
- **Explicitly required structure:** record the constraint, source, and reason.
  Resolve a conflict before changing it; a familiar existing pattern is not by
  itself an explicit requirement.

Treat aggregate boundaries, transaction changes, or effects outside scope as material
when guarantees or responsibilities change. Assess that impact before calling a
change internal. This authority does not expand the task's scope.

Material design changes affecting later tasks must be synchronized in the tripack
before those tasks start. At task closure, align affected design, strategy, and task
status with the result. Small local structural edits need no step-by-step history.
This defines the generator's handoff; changing separate executor instructions is
follow-up work.

## Output

Three files:

1. `technical-design.md`
2. `tactical-strategy.md`
3. `task-plan.md`
4. `decision-log.md` — **optional, conditional**: generate only when the input or
   analysis surfaces technical decisions driven by business decisions/constraints
   (see "When to generate decision-log.md" below). Never create it as an empty
   formality. Its audience is PM / product manager / tech lead — see the template
   for tone rules.

Save to the output directory specified by the user. If none is specified:
save next to the input file if the input is a file; otherwise ask
(suggest `./docs/<feature-name>/` as the default).

---

## Output Templates

The templates below are the baseline structure. **Adapt terminology and sections to
the project discovered in Project Context Discovery** — use the project's actual
layer names, and rename/omit sections that don't apply (noted per section).

### File 1: technical-design.md

```
# Technical Design: [feature name]

## 1. Overview
[Brief description of the feature and its context]

### Goals
- [what this delivers]

### Non-Goals
- [explicitly out of scope]

## 2. API / Interface Contract Changes
[Adapt to the project's API style: gRPC/proto changes, REST endpoints & OpenAPI,
GraphQL schema, event/message contracts, CLI commands, or public library API]
[If none: "No contract changes required"]

## 3. Data Model & Schema Changes
[If none: "No schema changes required"]
- Affected entities and final schema (per the project's DB and migration tooling)
- Migration plan: forward migration + rollback path
- Impact on existing data (backfill, defaults, data loss risk)

### Concepts and rules (when domain models/rules are affected)
- Business concepts/terms, their meaning, and the context where each applies
- Identity/lifecycle, value semantics, and consistency boundaries where relevant

| Material rule | Owner | Enforcement paths | Proof plan |
|---------------|-------|-------------------|------------|
| [rule/invariant] | [responsible component/model] | [creation/restoration/transition/other callers] | [observable cases and verification] |

[For changes without domain impact: state that domain assessment is not applicable
and why. Record any material non-domain rules with their owners in component details.]

## 4. Component Breakdown
[Use the project's actual layers/components as rows — discovered, not assumed]

| Layer/Component | Status | Justification |
|-----------------|--------|---------------|
| [project's layer 1] | New / Modified / Reused as-is | ... |
| [project's layer 2] | New / Modified / Reused as-is | ... |

### Detail per component
[For each New or Modified component: explain what changes and why; include material
non-domain rules, owners, enforcement paths, and proof plans not covered above]

### Material structure decisions and assessment
| Choice | Reason and material alternatives/tradeoffs | Authority and refinement boundary | Assessment outcome |
|--------|-------------------------------------------|-----------------------------------|--------------------|
| [chosen or retained structure] | [concrete benefit/cost vs viable alternative] | [internal choice / explicit constraint with source and reason; binding guarantees] | [retained / revised / deferred, reason and decision needed] |

[Scale detail to impact; a simple retained structure can need only a brief reason.
Keep assessment in these design sections rather than a separate report/checklist.]

## 5. Flow Sequence
[Step-by-step from entry point to result — request/response, event handling,
job execution, or whatever fits the feature]
1. [step 1]
2. [step 2]

## 6. Request / Response (or Input / Output)
[Diff or full spec of new input and output vs existing if changed]

### Creation, restoration, and transition contracts (when relevant)
| Path | Accepted inputs/states and normalization | Errors/order and failure timing | Side effects and compatibility | Proof plan |
|------|------------------------------------------|--------------------------------|--------------------------------|------------|
| [new input / load persisted state / state transition] | [path-specific policy] | [observable behavior] | [caller/data/effects] | [cases to verify] |

[Explain whether restoration trusts, validates, normalizes, or migrates stored state.
Share policy only where the path contracts agree; note inapplicability briefly.]

## 7. Error Handling
[New errors added, their propagation path]

## 8. Constraints Checklist
- [ ] [constraint 1 from input]
- [ ] [constraint 2 from input]

## 9. Risks & Open Questions
[Implementation risks, unresolved questions, and deferred assessment decisions.
For each blocker: affected components/task IDs, decision owner or source needed,
and condition for readiness. Independent tasks may remain executable.]

## 10. Revision History
| Version | Date | Change | Reason |
|---------|------|--------|--------|
| v1 | [date] | Initial design | — |
```

### File 2: tactical-strategy.md

```
# Tactical Strategy: [feature name]

## 1. Implementation Strategy
[Additive-only / breaking change / mixed — and the chosen approach]

## 2. Code Reuse Map
| Component | Location | Used for | Fit and limitations |
|-----------|----------|----------|---------------------|
| [function/class/module name] | [package/file] | [purpose] | [what fits; relevant gaps] |

## 3. Net-New Code Inventory
| Item | Layer/Component | Type | Notes |
|------|-----------------|------|-------|
| [name] | [component] | [contract/handler/service/etc.] | ... |

## 4. Branching / PR Strategy
[Recommendation: one large PR or split, safe merge order; commit per task]

## 5. Testing Strategy
[Use the project's verified test framework and commands from applicable instructions
(including AGENTS.md / CLAUDE.md) and codebase. Derive cases from assessed rule
ownership and path contracts; distinguish planned checks from existing evidence.]

### Unit Tests
- [what to unit test, focus on new logic]

### Integration Tests
- Happy path: [scenario]
- Error cases: [scenario per new error]

### Regression Tests
- [existing endpoints or flows that must still pass]

## 6. Rollout & Rollback
- Rollout: [feature flags, phased rollout, monitoring to add]
- Rollback: [trigger conditions, how to revert code and migrations safely]
```

### File 3: task-plan.md

```
# Task Plan: [feature name]

## Legend
- ID: T1, T2, ... (stable — never renumber on revision)
- Complexity: S (< 2h) | M (2–4h) | L (> 4h)
- Status: [ ] Todo | [~] In Progress | [x] Done | ~~obsolete~~
- ref: technical-design.md section this task implements
- DoD: definition of done (verifiable)
- Blocked: decision/prerequisite and readiness condition; not executable until resolved

## Tasks

### Reconciliation (Mode C only — when code/schema changes are needed)
[Must precede dependent tasks. Omit for a documents-only revision.]
- [ ] T1. [align existing code/schema with revised design] — [Component] — [S/M/L]
      — depends: none — ref: design §X — DoD: [criteria]

### Sequential (must be done in order)
- [ ] T2. [Task title] — [Component] — [S/M/L] — depends: T1 — ref: design §X
      — DoD: [e.g. tests pass, lint clean, endpoint callable]

### Parallel (can be done simultaneously)
- [ ] T5. [Task title] — [Component] — [S/M/L] — depends: T1 — ref: design §Y
      — DoD: [criteria]

### Blocked / Deferred (when applicable)
- [ ] T6. [Affected work] — [Component] — [S/M/L] — depends: [decision/task]
      — ref: design §X — blocked by: [decision and owner/source]
      — ready when: [resolved choice and synchronized design] — DoD: [criteria]

## Dependency Graph
T1 → T2
T1 → T5 (parallel with T2)
```

### File 4 (optional): decision-log.md

**When to generate decision-log.md:** only when the input (PRD/FSD/analysis/user
statements) or the impact analysis reveals technical decisions that are *driven by
business decisions or constraints* — e.g. a product rule shaping where logic lives,
a deadline forcing a simplification, a compliance/regulatory requirement dictating
architecture, a pricing/tier decision shaping access control. Pure technical
trade-offs (library choice, internal patterns) do NOT belong here — they stay in
technical-design.md Justifications. If no business-driven decision exists, do not
create the file, and say so in the final summary.

**Audience & tone:** written for PM / product manager / tech lead. Business-first
ordering, plain language, no unexplained jargon. A PM must be able to read every
entry without an engineer translating.

```
# Decision Log: [feature name]

Technical decisions in this feature that exist because of business decisions or
constraints. Each entry records why, what it means for the product, and when it
should be revisited.

## D1: [short decision title, plain language]
- **Business driver:** [the business decision/constraint that triggered this,
  and who decided it — e.g. "Feature is premium-only (decided by PM, PRD §2)"]
- **Technical decision:** [what was chosen, explained in plain language]
- **What this means for the product:** [observable consequence for users/business —
  e.g. "free users get a clear upgrade prompt instead of an error"]
- **Alternatives considered:** [briefly, and why rejected — fair, no strawman]
- **Trade-offs accepted:** [honest, including the negative ones]
- **Revisit when:** [the business condition that should trigger re-evaluating this —
  e.g. "if the feature opens to all tiers" / "after launch deadline passes"]
- **Status:** active | superseded by D[x] (on [date], reason)
```

Decision IDs (D1, D2, ...) are stable and referenceable from technical-design.md
(e.g. "caching skipped — see decision-log D2") and from Component Breakdown
Justifications.

---

## How It Works

1. **Discover project context** from applicable instructions (including AGENTS.md
   and CLAUDE.md) and verified code — see Project Context Discovery. Identify the
   mode (A/B/C) and clarify material gaps/conflicts in constraints.
2. **Analyze/baseline** — Mode A: map requirements and affected boundaries.
   Mode B: impact analysis against the codebase. Mode C: baseline + impact analysis
   against the existing tripack and code, with separate coverage and difference
   assessments. Finish with affected behavior, reuse, and compatibility risks known
   or explicitly identified as unresolved.
3. **Draft technical-design.md** — what is new/reused, rule ownership and enforcement,
   path contracts, structure choices, and explicit constraints. Record business-driven
   decisions in decision-log.md when applicable; keep technical reasons in the design.
4. **Assess and refine the design** — read [Design Assessment](references/design-assessment.md)
   in every mode before deriving strategy/tasks. Apply domain criteria when the
   change affects models/rules; briefly explain inapplicability otherwise. Record
   retained/revised/deferred outcomes within the technical design. This step is done
   when every material rule has an owner, enforcement path, and proof plan, every
   material structure choice has a reason and refinement boundary, and blockers
   identify affected work and readiness conditions. Deferred choices remain blocked
   for dependent tasks; continue planning independently settled work.
5. **Generate/update tactical-strategy.md** from the assessed design — reuse,
   net-new work, safe order, and verification for material rules and contracts.
   Cover happy paths, new errors, relevant creation/restoration/transitions, and regression.
6. **Generate/update task-plan.md** from that strategy — granular tasks, each
   independently verifiable and completable in one implementation session. Every task has: stable ID, title,
   component, complexity, dependency, design-section ref, and DoD. Mark decision-blocked
   tasks explicitly. In Mode C, decide reconciliation direction before adding work.
7. **Check consistency** — design outcomes, reuse/inventory, test strategy, task
   dependencies, and DoD must agree. Check each material rule/contract has planned
   verification and no dependent task is marked ready with a blocking decision open.
   In Mode C, preserve valid task IDs/status and revision/decision history. When an
   active business-driven decision is contradicted, surface it to the user; after
   resolution, mark the old entry `superseded by D[x]` (never delete it) and add the
   new entry. Synchronize affected documents before handing off dependent tasks.
8. **Save the files** and summarize: mode used, key decisions, whether a
   decision-log was produced (and why not, if skipped), assessment outcomes, and
   open decisions with their blocked work.

## Rules

- **All output documents must be written in English**, regardless of conversation language
- Use the project's own layer/component names and conventions as discovered from
  applicable project instructions (including AGENTS.md and CLAUDE.md) and verified code;
  assess reference-flow suitability within scope instead of copying structure automatically
- Do not write implementation code — design and planning only
- Never fill the reuse map, component status, or schema details from assumption —
  verify against the codebase first
- Surface blocking questions with their affected work; keep dependent tasks blocked
  until resolved and continue independently settled planning
- In Mode C, never silently drop prior decisions: contradictions between old and new
  requirements or findings must be assessed under Design Authority, and history is
  preserved via Revision History, ~~obsolete~~ task markers, and `superseded`
  decision-log entries
- Apply the assessment completion gate before strategy/tasks. Internal refinements
  follow Design Authority and Handoff; documents-only updates require no code repair
- decision-log.md is conditional: create it only when business-driven technical
  decisions exist; keep its language accessible to PM/PO (plain language, no
  unexplained jargon), unlike the other three files which target engineers
- Task IDs are stable across revisions — add new IDs, never renumber
- Complexity estimates (S/M/L) are rough — engineers must still validate

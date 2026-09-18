# ENFORGE

**Engineering skills for understanding, planning, and executing software changes with AI coding agents.**

ENFORGE is a collection of reusable engineering skills designed to help AI coding agents work with existing software systems in a structured, traceable, and implementation-focused way.

Instead of jumping directly into code, ENFORGE promotes a simple workflow:

```text
Understand → Plan → Decide → Execute
```

The goal is not to generate more code.

The goal is to make better engineering decisions before and during implementation.

---

## Installation

ENFORGE can be installed using the [`skills` CLI](https://skills.sh).

### Install ENFORGE

Install the complete ENFORGE skill set:

```bash
npx skills add rfanazhari/enforge
```

### Install a Specific Skill

Install only the skill you need:

```bash
npx skills add rfanazhari/enforge --skill flow-scanner
```

```bash
npx skills add rfanazhari/enforge --skill tripack
```

```bash
npx skills add rfanazhari/enforge --skill task-executor
```

### Update ENFORGE

Update your installed skills to the latest version:

```bash
npx skills update
```

After installation or update, restart your AI coding agent so the latest skills are loaded.

---

## Core Workflow

ENFORGE currently consists of three core skills:

```text
                  ┌─────────────────┐
                  │  Flow Scanner   │
                  │   Understand    │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │     Tripack     │
                  │ Plan & Decide   │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Task Executor   │
                  │    Execute      │
                  └─────────────────┘
```

### 1. Flow Scanner

**Purpose:** Understand how an existing system actually works.

Flow Scanner analyzes existing code and reconstructs execution flows.

It can be used at different scopes:

```text
File
Directory / Path
Endpoint
Feature Flow
```

Typical questions it helps answer:

* Where does this request enter the system?
* Which components are involved?
* How does data move through the application?
* Which repository, service, or domain logic is executed?
* Where are side effects introduced?
* What existing assumptions or dependencies should be preserved?

The output describes the **current state of the system** rather than immediately proposing a redesign.

---

### 2. Tripack

**Purpose:** Turn system understanding into an implementation-ready engineering plan.

Tripack produces four complementary artifacts:

```text
Technical Design
Tactical Strategy
Task Plan
Decision Logs
```

#### Technical Design

Defines the intended technical solution and affected system boundaries.

#### Tactical Strategy

Explains how the change should be approached within the existing architecture and constraints.

#### Task Plan

Breaks the solution into concrete implementation tasks that can be executed sequentially.

#### Decision Logs

Capture important engineering decisions, trade-offs, assumptions, and rejected alternatives.

Tripack bridges the gap between:

```text
"Here is how the system works."

                ↓

"Here is how we should change it."

                ↓

"Here are the exact tasks required."
```

---

### 3. Task Executor

**Purpose:** Execute an existing task plan without losing the original engineering intent.

Task Executor consumes the implementation plan produced during the planning stage and carries out the defined work.

The emphasis is on:

* following the agreed scope
* preserving architectural boundaries
* implementing tasks in a controlled sequence
* validating changes against the plan
* avoiding unnecessary refactoring
* keeping implementation aligned with documented decisions

The executor is not intended to replace engineering judgment.

It reduces the gap between **a well-defined plan and its implementation**.

---

## Design Principles

### Understand Before Changing

Existing behavior should be understood before proposing modifications.

### Plan Before Implementing

Complex changes benefit from an explicit technical plan before code changes begin.

### Separate Decisions From Execution

Architectural decisions should remain visible instead of being hidden inside implementation steps.

### Prefer Focused Changes

Avoid unnecessary refactoring when the requested change can be implemented cleanly within the existing system.

### Preserve Context

Important assumptions and decisions should remain available to both humans and future AI agents.

### Optimize for Maintainability

The objective is not merely to produce working code.

The objective is to produce changes that other engineers can understand, review, maintain, and extend.

---

## Current Technology Support

ENFORGE currently focuses on **Go-based backend systems**.

The skill architecture is intentionally designed to remain technology-agnostic where possible, allowing additional technology-specific skills to be introduced over time.

---

## Repository Structure

```text
enforge/
├── skills/
│   ├── flow-scanner/
│   ├── tripack/
│   └── task-executor/
│
├── docs/
├── examples/
└── README.md
```

Each skill is independently reusable while also working as part of the complete ENFORGE workflow.

---

## Recommended Usage

For a non-trivial change in an existing codebase:

```text
1. Scan
   ↓
2. Understand the existing flow
   ↓
3. Build the engineering plan
   ↓
4. Record important decisions
   ↓
5. Execute the task plan
   ↓
6. Validate the implementation
```

For example:

```text
Feature Request
      │
      ▼
Flow Scanner
      │
      ▼
Existing System Understanding
      │
      ▼
Tripack
 ┌────┼───────────────┐
 ▼    ▼               ▼
Design Strategy    Task Plan
 └────────┬───────────┘
          ▼
   Decision Logs
          │
          ▼
   Task Executor
          │
          ▼
     Code Changes
```

This workflow is particularly useful when working with unfamiliar or legacy systems where the largest risk is not writing code, but misunderstanding the system before changing it.

---

## Example

A typical workflow might look like:

```text
Request:
"Move price plan handling from registration-level assumptions
to a survey-level snapshot."

        ↓

Flow Scanner
→ trace current price plan flow
→ identify affected endpoints
→ inspect domain/application/repository interactions
→ document existing assumptions

        ↓

Tripack
→ define technical design
→ define migration/change strategy
→ produce task plan
→ record architectural decisions

        ↓

Task Executor
→ implement tasks sequentially
→ update affected components
→ add/update tests
→ validate implementation against the plan
```

The important distinction is that implementation starts **after the system and solution have been made explicit**.

---

## Intended Use

ENFORGE is designed for:

* AI-assisted software development
* backend engineering
* existing codebase analysis
* architectural planning
* refactoring
* feature implementation
* engineering documentation
* repeatable development workflows

It can be used by individual developers, engineering teams, or AI coding agents.

---

## Philosophy

Software engineering is rarely difficult because writing the code is impossible.

It is difficult because the system already exists.

There are constraints, assumptions, historical decisions, dependencies, and behaviors that are not always visible from a single file.

ENFORGE is built around a simple idea:

> **Understand the system. Make the decisions explicit. Then change it.**

---

## Status

ENFORGE is under active development.

The current focus is improving the workflow for Go-based backend systems while keeping the overall skill architecture extensible for additional technology stacks.

---

## Contributing

Contributions, improvements, examples, and new skills are welcome.

When introducing a new skill, prefer skills that:

1. solve a repeatable engineering problem
2. produce a clear and actionable output
3. work well with existing software systems
4. complement the existing ENFORGE workflow
5. remain useful across different projects

---

## License

See [LICENSE](LICENSE) for details.

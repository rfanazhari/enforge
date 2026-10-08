# Journey document template

Output path in the target repo:

```text
docs/business/journeys/UJ-00N-<slug>.md
```

Use this structure exactly. Keep every heading, in this order, even when a section is
short — write "None." rather than removing a section. Replace every `<...>` placeholder;
leave nothing in angle brackets in the finished document.

```markdown
---
id: UJ-00N
title: <Journey title>
mode: compose | discovery
primary_actor: <one business role>
business_goal: <one sentence, from the actor's point of view>
status: draft | planned
updated: YYYY-MM-DD
sources:
  - <path or link to the business document>
  - <path to each capability doc consulted>
---

# UJ-00N — <Journey title>

## 1. Metadata

| Field | Value |
|---|---|
| **Primary actor** | <role, named as the business names it> |
| **Business goal** | <what the actor is trying to achieve> |
| **Mode used** | `compose` / `discovery` |
| **Status** | `draft` — placeholders or unresolved conflicts remain / `planned` — every step cites an existing capability; nothing verified against code |
| **Source documents** | <business doc>, <capability docs> |
| **Supporting actors** | <roles that appear on individual steps, or "None."> |

## 2. Trigger and preconditions

**Trigger:** <the event or decision that starts the journey>

**Preconditions:**
- <what must already be true before the trigger counts>

**End state:** <what is true for the primary actor when the journey has succeeded>

## 3. Happy path

| Step | Actor | Action | Capability | Rules | Outcome |
|---|---|---|---|---|---|
| S1 | <Primary actor> | <business action> | [<cap-id>](../capabilities/<cap-id>.md) | `BR-XXX-0NN`, `BR-XXX-0NN` | <business-visible result> |
| S2 | <Supporting actor> | <business action> | [<cap-id>](../capabilities/<cap-id>.md) | `BR-XXX-0NN` | <business-visible result> |
| S3 | <Primary actor> | <business action> | `CAP-TBD-<suggested-slug>` | — | <business-visible result> |

## 4. Alternate and exception paths

### A1 — <Business situation that causes this path>

Branches from: `S<n>`. Ends: <rejoins at `S<m>` / terminates with <outcome>>.

| Step | Actor | Action | Capability | Rules | Outcome |
|---|---|---|---|---|---|
| A1.1 | <actor> | <business action> | [<cap-id>](../capabilities/<cap-id>.md) | `BR-XXX-0NN` | <business-visible result> |

### A2 — <Business situation>

Branches from: `S<n>`. Ends: <...>.

| Step | Actor | Action | Capability | Rules | Outcome |
|---|---|---|---|---|---|
| A2.1 | <actor> | <business action> | <capability ref> | <rule ids> | <outcome> |

## 5. Cross-capability invariants and detected conflicts

### Invariants

| Invariant | Holds across | Enforced by |
|---|---|---|
| <condition that must stay true across steps or capabilities> | `S<n>`–`S<m>` | `BR-XXX-0NN`, `BR-YYY-0NN` |

### Detected conflicts

| Conflict | Side A | Side B | Decider |
|---|---|---|---|
| <what the two capabilities disagree about> | `BR-XXX-0NN` — <what it requires> | `BR-YYY-0NN` — <what it requires> | <who decides> |

<"None." under either table if there is nothing to record.>

## 6. Depends on / Used by

### Depends on

| Journey | What this journey needs from it | Consumed at |
|---|---|---|
| [`UJ-00M`](./UJ-00M-<slug>.md) | <data or state the other flow must have produced> | `S<n>` |
| `UJ-TBD-<slug>` | <data or state> — journey not written yet, see Gaps | `S<n>` |

### Used by

| Journey | What it consumes from this journey |
|---|---|
| [`UJ-00K`](./UJ-00K-<slug>.md) | <state this journey produces> |

## 7. Gaps and open questions

### Capabilities needed

<discovery mode, or compose mode when a gap was found. One entry per `CAP-TBD-*` placeholder. "None." otherwise.>

| Placeholder | Suggested capability title | Owning service (if known) | Covers steps | Must describe |
|---|---|---|---|---|
| `CAP-TBD-<slug>` | <Suggested Title> | <service or "unknown"> | `S<n>`, `A1.2` | <the business behaviours and decisions this capability has to contain> |

### Missing journeys

- `UJ-TBD-<slug>` — <the other actor's flow this journey depends on, and why it needs a journey of its own>

### Open questions

- <Unresolved product decision> — **Decider:** <who>
- <Rule the business document implies but no capability declares> — **Decider:** <who>

## 8. Traceability matrix

| Step | Capability | Rules |
|---|---|---|
| S1 | <cap-id> | `BR-XXX-0NN`, `BR-XXX-0NN` |
| S2 | <cap-id> | `BR-XXX-0NN` |
| S3 | `CAP-TBD-<slug>` | — |
| A1.1 | <cap-id> | `BR-XXX-0NN` |

<Every rule id cited anywhere above appears here; nothing appears here that no step cites.>

## 9. Recommended next skill

**Next:** `<skill name>` — <one sentence on why, given the mode and what this journey found>

<For discovery with placeholders: list the "Capabilities needed" entries to run
`capability-doc-generator` on, then note that `journey` should be re-run in `compose` mode.
For compose with unresolved conflicts: "None yet" plus the conflicts to resolve first.>
```

## Section notes

**Metadata** — `status` is about completeness of the journey, never about implementation.
`draft` while any `CAP-TBD-*` / `UJ-TBD-*` placeholder or unresolved conflict remains;
`planned` once every step cites an existing capability. Nothing in this document is
verified against code.

**Happy path** — the actor's view, not the system's. One business action per row, one
business-visible outcome. Rule cells contain ids only; the rule text lives in the
capability doc.

**Alternate paths** — named after the business situation, anchored to the happy-path step
they branch from, and explicit about how they end. Ids are `A<n>.<m>` and are stable
across revisions.

**Invariants and conflicts** — an invariant with no rule in "Enforced by" is a gap and
belongs in section 7 as well. A conflict is never resolved here; it is described from both
sides and assigned a decider.

**Depends on / Used by** — other journeys only. A dependency on another actor's flow is a
row here, not a second primary actor and not a set of borrowed steps.

**Capabilities needed** — the handoff to `capability-doc-generator`. "Must describe" is
written as business behaviour the capability has to decide, not as rules: writing the
rules is that skill's job.

**Traceability matrix** — the mechanical check that nothing was cited without being used
and nothing was used without being cited.

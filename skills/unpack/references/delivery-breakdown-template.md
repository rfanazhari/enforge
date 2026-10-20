# Delivery Breakdown Template

Use exactly five top-level sections below the document title. Replace bracketed
instructions with sourced content or explicit missing-information statements.
Repeat group blocks, work blocks, and table rows as needed.

The template is written in English for maintainers. Translate headings, labels,
and status wording to the selected output language. Keep original identifiers,
contract fields, and source locators unchanged.

| English heading | Indonesian heading |
|---|---|
| Delivery summary | Ringkasan delivery |
| Work groups | Kelompok pekerjaan |
| Detailed breakdown | Detail breakdown |
| Coverage and dependencies | Cakupan dan ketergantungan |
| Technical references | Referensi teknis |

## Document structure

```markdown
# Delivery Breakdown: [feature name]

## 1. Delivery summary

[Purpose, intended users, and the planned result, supported by the references.
If the business context is absent, say what is unknown.]

- Selected scope: [FE / BE / Mobile / explained other]
- Planning basis: [tripack identities and requirement references]
- Grouping basis: [AC scenarios / API capabilities / FSD functions / other]
- Acceptance boundary: [what this scope contributes and what requires others]

## 2. Work groups

### [Outcome-oriented group name]

[The result this group is intended to provide.]

- Requirement sources: [original IDs or explicitly marked local aliases]
- Work items: [DB-001, DB-002]
- Group acceptance: [observable conditions supported by the sources]
- End-to-end dependencies: [other contributions or decisions needed; identify
  conditional acceptance rather than claiming the selected scope satisfies all AC]

## 3. Detailed breakdown

### DB-001: [work name]

- ID: DB-001
- Work name: [concrete planned contribution]
- Purpose: [why this work is needed, based on the sources]
- Work scope: [included behavior and relevant boundaries]
- Deliverable: [result that will be available]
- Acceptance criteria:
  - [specific, verifiable condition supported by a source]
  - [when unresolved: pending decision, source disagreement, and acceptance impact]
- Dependencies: [other DB IDs, outside-scope contributions, recorded decisions,
  or unresolved decisions; state none identified when appropriate]
- Responsibility by scope: [BE / FE / Mobile / explained other; name a person or
  team only if assigned in the sources, otherwise say the owner is not assigned]
- Requirement sources: [behavior / API contract / recorded policy / engineering
  need, each with document and source locator]

## 4. Coverage and dependencies

Tripack coverage describes planned work for the selected scope, not delivery
progress or verified implementation.

| Requirement / source | Work items | Scope position | Tripack coverage | Covered and missing contributions | Decision needed and acceptance impact |
|---|---|---|---|---|---|
| [original ID or marked alias + locator] | [DB IDs or reason none] | [In scope / Outside scope] | [Fully planned / Partially planned / Not planned / Not assessed for outside scope] | [specific planned and missing parts, including external dependencies] | [source positions, required decision and impact, or None identified] |

### Outside-scope dependencies

[Contribution, responsible scope if known, affected group/work IDs, and the
condition needed for acceptance. Do not invent external task IDs or owners.]

### Scope changes

[On revision: retained IDs with scope changes; new IDs; retired IDs with reasons;
split/merge/predecessor relationships. On an initial run, omit this subsection.]

## 5. Technical references

| Work item | Tripack identity | Task-plan location and original task IDs | Design / strategy sections | Decision references |
|---|---|---|---|---|
| DB-001 | [identity or marked local alias] | [document locator + T1, T2] | [document locators and relevant sections] | [document locator + D1, or none] |

[For requirements not planned in a tripack, keep the DB reference and explain
that no technical mapping exists. For shared technical tasks, say that repeated
references describe shared support, not additional effort.]

[Source register: document locations and tripack identities, plus any local
requirement aliases with their original sections. Keep this within section 5.]
```

## Work field labels

Use these labels consistently for Indonesian output:

| English | Indonesian |
|---|---|
| ID | ID |
| Work name | Nama pekerjaan |
| Purpose | Tujuan |
| Work scope | Cakupan pekerjaan |
| Deliverable | Hasil yang diserahkan |
| Acceptance criteria | Kriteria penerimaan |
| Dependencies | Ketergantungan |
| Responsibility by scope | Penanggung jawab berdasarkan scope |
| Requirement sources | Sumber kebutuhan |

Keep unknowns descriptive, for example "Penanggung jawab belum ditetapkan" or
"Perlu keputusan: masa berlaku file". A work item can still have clear scope
responsibility while its person/team assignment is unknown.

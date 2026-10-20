---
name: unpack
description: >
  Turn existing engineering tripacks and requirement references into an outcome-based
  delivery breakdown for PMs, System Analysts, and Project Analysts. Use only when the
  user explicitly invokes unpack (for example, $unpack or unpack:) after a tripack
  exists. Supports FE, BE, Mobile, or a specified combination of scopes. Produces
  traceable work items, acceptance criteria, dependencies, and planning gaps.
  Does not generate tripacks, implement code, or document actual changes as tickets.
---

# Unpack

Translate technical plans into delivery work that stakeholders can understand,
assign, and verify. Each work item describes a planned result within its scope;
each group explains the shared outcome and its end-to-end acceptance boundaries.

## Invocation and boundaries

Run only when explicitly requested by the user. A completed tripack or a general
request for technical planning must not automatically trigger this skill.
On Codex, `agents/openai.yaml` disables implicit invocation. On other platforms,
use an equivalent setting when supported and retain this explicit-use instruction.

Read the supplied documents; do not require codebase discovery or implementation
verification. Generate only the requested breakdown. Do not modify requirements,
contracts, tripacks, code, or external trackers. Tripack tasks and completion
checkboxes are planning context, not independent proof of implemented behavior.

## 1. Validate inputs

| Input | Requirement |
|---|---|
| Tripacks | Required: a folder or document list containing technical design, tactical strategy, and task plan for each tripack. Multiple tripacks are supported. |
| References | Required: at least one accessible document, with its location and type: `acceptance-criteria`, `api-contract`, `fsd`, or `other`. For `other`, identify the document kind. |
| Scope | Required: FE, BE, Mobile, or an explained `other`; combinations are allowed. |
| Output language | `id` or `en`; default to `id`. This controls the output, not the language of these instructions. |
| Output location | Optional. Return Markdown in the conversation unless saving is requested or a location is supplied. |
| Existing breakdown | Optional for revisions; use a supplied document or an existing file at the chosen output destination as the baseline. |

Use information already present in the conversation or discoverable from the
provided locations. Resolve an unambiguous reference type from its contents.
Do not ask again for known inputs or scan unrelated directories for documents.

Read all three documents for every tripack and any available decision log.
A missing decision log is not a blocker. Note an inaccessible optional log;
do not assume it contains no decisions.

If required inputs are missing, ambiguous, invalid, or inaccessible, ask one concise
question covering all unresolved inputs before generating. Do not block solely
because the references and plans disagree; handle content conflicts in step 4.

When saving without an explicit location, use `delivery-breakdown.md` beside the
single tripack, or in the shared directory explicitly supplied for multiple tripacks.
If no such destination can be determined, ask where to save. Preserve existing
work IDs when updating that document.

## 2. Map sources and planned coverage

Give each tripack a distinguishable identity, reusing its existing identifier
or assigning a clearly marked local alias. Always qualify technical task references
with tripack identity and document location: `export-be / task-plan.md / T1`.
Preserve original task and decision IDs; multiple tripacks may each contain `T1`.

Extract requirements and observable outcomes with source IDs and section locators.
When a source has no IDs, assign stable, clearly marked local aliases and record
their document and section. Do not present an alias as an original AC identifier.

Distinguish the evidence behind each statement:
- User behavior from AC or FSD.
- Data exchange and supported operations from an API contract.
- Additional policy from an explicit recorded decision.
- Necessary supporting work from the engineering plan, labeled as an
  engineering need (in Indonesian: "kebutuhan engineering").

Follow explicit recorded decisions when they resolve a conflict, and cite them.
Unaccepted proposals remain proposals. Never infer stakeholder approval.
Keep unresolved differences visible rather than silently picking a source.

## 3. Group outcomes and define work items

Use two levels:

1. **Work groups:** capabilities or observable outcomes, with group-level
   acceptance boundaries and external contributions needed for end-to-end acceptance.
2. **Delivery work items:** independently assignable, verifiable contributions
   within the selected scope.

If AC exists, use related acceptance scenarios as the primary grouping framework.
This is a grouping rule, not an automatic precedence rule for conflicting content.
Keep every original AC reference; several AC may support one group and one AC
may require several work items.

Otherwise group API operations by the capability they support, FSD content by
business function/use case/user flow, and other references by evidenced outcomes.
For technical-only references, describe supported operations neutrally and mark
missing business context. Do not invent actors, goals, or product rules.

Split work when responsibility or independently verifiable acceptance differs.
This also applies within one scope when services or shared packages have
separately owned, independently acceptable deliverables. Each item should have
a clear responsibility and completion boundary. Keep one shared contribution
when scenarios reuse the same deliverable; scenario grouping alone is not a
reason to duplicate work.
For combined FE/BE/Mobile scope, keep a shared outcome group and separate work
items where each scope has its own deliverable. Do not split by technical layer
or force one work item per endpoint or AC.

Include necessary engineering support within the selected scope and explain its
contribution clearly. Name technical items by the concrete change and the component
or operation involved, using source-supported mechanisms when they clarify the work.
Keep the benefit in Purpose rather than using it as a substitute for the work name.
Technical acceptance criteria are appropriate for technical deliverables. Keep groups
understandable through their outcomes; use the writing guide to choose item detail.
Leave implementation mechanics that do not define the deliverable or its
acceptance in technical references.
Reference shared technical tasks from multiple work items where needed;
repeated references do not create additional effort.

Each work item must contain the nine fields in the output template. Write
specific, source-supported acceptance criteria. Assign scope responsibility,
not invented people or team assignments. Mark unknown owners explicitly.
Include estimates only if supplied by a source or requested by the user;
do not convert tripack complexity labels into delivery durations automatically.

## 4. Expose scope, gaps, and dependencies

Keep these three dimensions separate in the coverage table:

| Dimension | Meaning |
|---|---|
| Scope position | In scope or outside the selected scope. |
| Tripack coverage | Fully planned, partially planned, or not planned for the selected scope's contribution; outside-scope contributions may be marked not assessed. |
| Decision needed | The specific unresolved issue, source differences, and acceptance impact; "None identified" when applicable. |

Account for every referenced requirement relevant to the delivery, including
outside-scope dependencies. Explain any intentionally excluded requirement.
For partial coverage, name what is planned and what is missing.

If a requested scope is absent from the tripack, summarize its sourced requirements
as not planned. Leave its technical mapping explicitly empty with a reason;
do not invent a design or task ID.

Continue with clear work even when other work has unresolved conflicts. For
affected work, show both source positions, their impact, and the decision needed.
State only agreed acceptance criteria as final; mark unresolved criteria as
pending a decision. A decision may be needed even when technical work is planned.

Keep outside-scope work as dependencies, not assigned delivery work items.
A fully planned BE contribution must not imply that an entire dashboard flow
or end-to-end AC is covered. State missing contributions at group level.

In each item's Dependencies field, distinguish prerequisites to start or complete
that contribution from dependencies for end-to-end acceptance. State the required
result or stage; do not make an entire item a prerequisite when only part is needed.
Check prerequisite relationships for cycles. Surface a source-plan cycle as a gap
rather than inventing an execution order to hide it.

Keep the full explanation of each open issue in one register in section 4,
using stable local IDs when the sources have none. Distinguish unresolved decisions
from provisioning or validation prerequisites. Identify whether each issue blocks
implementation, acceptance, or production activation; more than one may apply.
Elsewhere cite its ID and local impact. Highlight material blockers in the summary
without repeating their full history. Fully planned work may still have open gates.

## 5. Preserve identities on revision

Use `DB-001`, `DB-002`, and so on for new delivery work items. Keep existing
valid IDs and local source aliases, even if their order changes.

- Keep an ID when the same deliverable remains identifiable; explain scope changes.
- For a split, retain the original ID only if its deliverable still exists.
  Give distinct new deliverables new IDs and record their relationship.
- For a merge, preserve an ID only if it still describes the resulting deliverable;
  otherwise allocate a new ID and record the predecessor IDs.
- Retire removed or replaced work in the scope-changes notes with a reason and
  replacement IDs where applicable. Never reuse retired IDs or silently erase them.

Use the highest previously allocated DB number, including retired work, when
allocating new IDs. Revision notes belong in section 4, keeping five top-level
sections rather than adding a separate changelog.

## 6. Write and check the breakdown

Read [the output template](references/delivery-breakdown-template.md) before
writing. Read [the writing guide](references/writing-guide.md) for the selected
language. Localize headings, field labels, and explanatory text; preserve source
identifiers, API field names, and significant source wording.

Before returning or saving:
- Check all five sections and all nine fields for each work item.
- Trace each work item to source evidence and technical references, or an
  explicit not-planned explanation.
- Confirm every relevant requirement has a coverage row and every pending
  decision names its acceptance impact.
- Check that scope responsibility, group acceptance, and dependencies agree.
- Check independently assignable boundaries, prerequisite stages, and shared
  contributions for accidental duplication or double counting.
- Preserve source meaning, quantities, and ID relationships after editing.
- Check that no planned behavior is described as verified implementation.

## Example invocation

```text
$unpack

Tripack: docs/report-export/

References:
- Type: acceptance-criteria
  Location: docs/requirements/report-export.md
- Type: api-contract
  Location: api/report-export.yaml

Scope: BE
Output language: id
```

This returns Markdown in the conversation. Add an output location to save it.

## Related skills

- `engineering-tripack-generator` produces the technical plans consumed here.
- `go-task-executor` continues to consume the technical task plan for implementation.
- `ticket-generator` documents actual changes after implementation. This breakdown
  does not replace its required inputs or its diff-based verification.

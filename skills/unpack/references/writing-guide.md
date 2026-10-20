# Writing Guide

Write for PMs, System Analysts, and Project Analysts reviewing planned delivery.
The purpose is to explain what will be available, its boundaries, and how it can
be checked. Developer details remain traceable in technical references.

Do not ask who the audience is or what the document is for when this context is
already established. Ask only for missing information that changes the breakdown.

## Shared rules

- Lead each paragraph with its main point and keep one idea per paragraph.
- Use concrete verbs, observable results, and active voice when the actor is known.
- Separate planned outcomes from completed or verified behavior. Neither a tripack
  entry nor a checked task independently proves that behavior is implemented.
- Preserve facts, quantities, contract fields, identifiers, and source meaning.
  Do not strengthen a claim while simplifying its wording.
- Quantitative claims need a source. Acceptance criteria can also describe
  observable behavior without a numeric threshold.
- Do not invent improvement measurements, owners, approvals, business benefits,
  or causes. Identify a proposal as a proposal.
- State exactly what is unknown and why it matters. Prefer "Owner not assigned"
  or "Decision needed: file validity period" to an unexplained placeholder.
- Use tables for mappings and comparisons, lists for parallel items, and prose
  for reasoning. Keep the required output structure without making every
  explanation a uniform list.
- Keep familiar terms such as export, filter, dashboard, download, endpoint,
  deploy, and rollback when appropriate. Use them consistently; italics are optional.

## Indonesian output

Use natural, professional Indonesian: write as someone explaining a topic they
understand to a colleague. Stay polite and direct without sounding ceremonial.
Use neutral descriptions of actors, responsibilities, and planned results instead
of switching between "saya", "kami", and "tim".

Prefer short to medium sentences, roughly 10-25 words when natural. This is an
editing guideline, not a required word count. Vary sentence openings and length;
split stacked subordinate clauses.

Use familiar professional words such as "pakai", "lalu", "karena", and "supaya"
when they fit. Avoid literal English translations and heavy nominal phrases:
use "menerapkan" rather than "melakukan penerapan".

Avoid:
- Generic introductions such as "Dalam era digital" or "Seiring berkembangnya".
- Repetitive closing summaries such as "Dengan demikian, dapat disimpulkan".
- Unsupported adjectives such as "signifikan", "komprehensif", "holistik",
  "krusial", or "optimal", and filler such as "memegang peranan penting".
- Stock contrasts such as "bukan hanya X, tetapi juga Y", forced three-part
  lists, excessive bold text, em dashes, and exclamation marks.
- Implementation jargon in work names and main explanations when the result
  can be described directly.

Use "Menyediakan..." for a planned work name where suitable. Describe expected
behavior in acceptance criteria. Avoid "Tim telah menyediakan..." unless actual
completion is independently evidenced and relevant to the requested document.

These examples illustrate wording only; they do not define product requirements:

| Abstract wording | Outcome-oriented wording |
|---|---|
| Mengimplementasikan mekanisme asynchronous export processing | Menyiapkan laporan dan menyediakan informasi kesiapan unduhan |
| Melakukan optimalisasi validasi authorization | Membatasi akses laporan sesuai hak pengguna |
| Menangani exception pada proses generation | Memberi informasi ketika laporan gagal disiapkan |

Only use the behavior in an example if the project's sources support it.
Never copy example-specific file formats, date limits, columns, or durations.

## English output

Use plain professional English with the same evidence and scope discipline.
Prefer "Prepare the report and expose its readiness" to implementation-heavy
phrasing when that result is supported. Avoid promotional benefits, vague
improvement claims, ceremonial introductions, and literal translations from
Indonesian. Localize all explanatory text while preserving source identifiers.

## Final language pass

Read the draft once for clarity and once for fidelity:
1. Simplify phrases that would sound unnatural in a professional conversation.
2. Remove filler and unsupported claims.
3. Replace vague placeholders with the missing information and its impact.
4. Confirm all source facts, numbers, identifiers, boundaries, and pending
   decisions survived the edit unchanged.

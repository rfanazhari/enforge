# Writing Guide

Write for PMs, System Analysts, and Project Analysts reviewing planned delivery.
The purpose is to explain what will be available, its boundaries, and how it can
be checked. Developer details remain traceable in technical references.

Do not ask who the audience is or what the document is for when this context is
already established. Ask only for missing information that changes the breakdown.

## Detail and repetition

Keep sections 1-4 focused on planned results, observable acceptance, responsibility,
and decisions. Match the vocabulary to the work: API contracts, database migrations,
retry handling, and other technical deliverables may use technical criteria.
For Indonesian titles, follow the title patterns below. For English titles, name
the concrete change and where it happens in one clause. Put the benefit in Purpose.
If the plan does not name a mechanism, name the known change and place without
inventing an implementation. Keep groups focused on shared outcomes.

Keep technical detail when it identifies the deliverable or determines acceptance,
such as a schema change, retry limit, required payload field, delivery deadline,
or condition for releasing a held operation. Put lower-level implementation
mechanics in section 5's technical references. Avoid forcing a technical task into
vague business wording or adding a business benefit absent from the sources.

Acceptance criteria must remain verifiable after simplification. Avoid replacing
a concrete failure or recovery rule with phrases such as "handles errors well".
Keep sourced quantities and implementation constraints traceable through precise
technical references; do not delete or change them to make the document shorter.

Use the source register for document locations and the open-issue register for full
gap explanations. Elsewhere use IDs, section locators where needed, and the impact
on the current item. Keep technical task references qualified by tripack and location.
State shared boundaries once at the appropriate document or group level, repeating
them only when they change an item's acceptance. Do not impose a fixed word limit
or drop relevant requirements to reduce length.

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

Technical Indonesian uses an Indonesian sentence with the technical words the
team already says. Keep payload, queue, event, retry, timeout, endpoint, token,
service names, and operation names from the plan, such as capture, submit, hold,
and release. Do not translate an operation name into an Indonesian verb that
also means something else.

### Judul

Use two patterns. These examples define wording only, not product requirements.

Scope Pekerjaan states a capability or rule, the way a requirement is said:

`[Objek bisnis] bisa/dapat [hasil]`

`Hanya [objek] yang dapat [hasil]` when the outcome is an eligibility boundary.

| Gunakan | Hindari |
|---|---|
| Survey Berbayar bisa dilaporkan ke Salesforce | Pembayaran survei berbayar terkirim ke Salesforce |
| Hanya Survey Berbayar yang dapat dilaporkan ke Salesforce | Mengaitkan pembayaran dengan pemilik yang benar |

"bisa" and "dapat" are both natural. Prefer "bisa" for a capability and
"Hanya ... yang dapat" for an eligibility rule. Keep the product name used in
the requirements, including "Survey" when that is the name in the source.
A Scope Pekerjaan title does not use a completed-status verb such as "terkirim".

Pekerjaan states one addition or change at a place people already recognize:

`Menambahkan/Mengubah [objek yang biasa disebut] pada [tempat]`

| Gunakan | Hindari |
|---|---|
| Menambahkan payload hitungan pada antrian (queue) event refund/cancel | Menyertakan bukti hitung pada refund reject dan cancel sampai antrean go-point |
| Menambahkan retry pada pengiriman Salesforce | Mengimplementasikan dispatcher Salesforce dengan ordering, retry, dan failure handling |
| Menambahkan payload pemilik pada request submit Salesforce | Menyiapkan dan memvalidasi data payload Salesforce |
| Capture perhitungan pembayaran pada submit survey regular, promo, dan chain | Menangkap perhitungan pembayaran pada submit survei regular, promo, dan chain |

Copy the operation word from the task title. Do not translate it and do not
choose a near Indonesian verb. "Capture" stays "capture". Use "Menambahkan"
or "Mengubah" only when the task title has no shorter operation word. Gloss
once when both languages appear, as in "antrian (queue)". Keep paired path
names such as "refund/cancel".

Leave a second mechanism, a benefit, and the acceptance boundary out of the
title. They belong in Tujuan, cakupan, or kriteria penerimaan.

Avoid:
- Generic introductions such as "Dalam era digital" or "Seiring berkembangnya".
- Repetitive closing summaries such as "Dengan demikian, dapat disimpulkan".
- Unsupported adjectives such as "signifikan", "komprehensif", "holistik",
  "krusial", or "optimal", and filler such as "memegang peranan penting".
- Stock contrasts such as "bukan hanya X, tetapi juga Y", forced three-part
  lists, excessive bold text, em dashes, and exclamation marks.
- A title that paraphrases a familiar technical noun, or stacks several mechanisms.
- A completed-status Scope Pekerjaan title, such as "terkirim" or "telah dilaporkan".

Describe expected behavior in acceptance criteria. Avoid "Tim telah menyediakan..."
unless actual completion is independently evidenced and relevant to the requested
document. In body prose, state what happens, when, and with what result. For
example, explain what a service does when an RPC times out instead of only
writing "kepastian hasil finansial".

Body wording follows the same noun rule as titles:

| Hindari | Gunakan |
|---|---|
| Mengimplementasikan mekanisme asynchronous export processing | Menambahkan pemrosesan export di background |
| Melakukan optimalisasi validasi authorization | Menambahkan validasi hak akses pada endpoint |
| Menangani exception pada proses generation | Menambahkan penanganan error saat membuat laporan |

Only use the behavior in an example if the project's sources support it.
Never copy example-specific file formats, date limits, columns, or durations.

## English output

Use plain professional English with the same evidence and scope discipline.
For technical items, name the concrete change and target directly, such as
"Implement request retry and transaction outcome synchronization with the payment
service", when supported by the plan. Avoid promotional benefits, vague improvement
claims, ceremonial introductions, and literal translations from Indonesian.
Localize all explanatory text while preserving source identifiers.

## Final language pass

Read the draft once for clarity and once for fidelity:
1. Check Scope Pekerjaan titles against `[Objek] bisa/dapat [hasil]` and work
   titles against `Menambahkan/Mengubah [objek] pada [tempat]`. Keep familiar
   technical nouns. Remove a second mechanism or a benefit from the title.
2. Remove filler and unsupported claims.
3. Replace vague placeholders with the missing information and its impact.
4. Confirm all source facts, numbers, identifiers, boundaries, and pending
   decisions survived the edit unchanged.

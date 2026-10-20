# Writing Guide

Write for PMs, System Analysts, and Project Analysts reviewing planned delivery.
The purpose is to explain what will be available, its boundaries, and how it can
be checked. Developer details remain traceable in technical references.

Do not ask who the audience is or what the document is for when this context is
already established. Ask only for missing information that changes the breakdown.

## Detail and repetition

Keep sections 1-4 focused on planned results, observable acceptance, responsibility,
and decisions. Match the vocabulary to the work: API contracts, database migrations,
retry handling, and other technical deliverables may use technical titles and
criteria. In work titles, state the concrete change and the component or operation
involved. Name the mechanism when it is documented and clarifies the work, such as
request retry, outcome synchronization, or a delivery dispatcher. A title describing
only a desired benefit or uncertain situation leaves the work ambiguous; put the
benefit in Purpose. If the plan does not specify a mechanism, name the known operation
and change without inventing an implementation. Keep groups focused on shared outcomes.

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

Technical Indonesian can be direct and natural. Keep familiar terms such as retry,
timeout, endpoint, token, and idempotency when they are more precise than a forced
translation. Explain an unfamiliar term briefly when needed to understand the
work. Prefer a sentence stating what happens, when, and with what result over a
chain of abstract nouns. For example, explain what a service does when an RPC
times out instead of only describing "kepastian hasil finansial".

Avoid:
- Generic introductions such as "Dalam era digital" or "Seiring berkembangnya".
- Repetitive closing summaries such as "Dengan demikian, dapat disimpulkan".
- Unsupported adjectives such as "signifikan", "komprehensif", "holistik",
  "krusial", or "optimal", and filler such as "memegang peranan penting".
- Stock contrasts such as "bukan hanya X, tetapi juga Y", forced three-part
  lists, excessive bold text, em dashes, and exclamation marks.
- Abstract titles or strings of technical terms that leave the actual work unclear.

Choose a verb that fits the work, such as "Menambahkan", "Mengubah", "Mengecek",
or "Mencegah"; do not force every title to start with "Menyediakan". Describe
expected behavior in acceptance criteria. Avoid "Tim telah menyediakan..." unless
actual completion is independently evidenced and relevant to the requested document.

These examples illustrate wording only; they do not define product requirements:

| Stiff or unclear wording | Direct wording |
|---|---|
| Mengimplementasikan mekanisme asynchronous export processing | Menambahkan pemrosesan export di background |
| Melakukan optimalisasi validasi authorization | Menambahkan validasi hak akses pada endpoint |
| Menangani exception pada proses generation | Menambahkan penanganan error saat membuat laporan |
| Menangani pembayaran dan refund yang hasilnya belum diketahui | Mengimplementasikan retry request dan sinkronisasi hasil transaksi go-point |
| Mengaitkan laporan dengan pemilik dan induk survei yang benar | Menyiapkan dan memvalidasi data payload Salesforce |
| Menjaga nilai terbaru saat pengiriman terlambat atau tidak pasti | Mengimplementasikan dispatcher Salesforce dengan ordering, retry, dan failure handling |
| Menyediakan kemampuan autentikasi layanan eksternal | Menambahkan autentikasi OAuth untuk API eksternal |
| Menjamin keunikan efek finansial pada redelivery | Menambahkan idempotency agar retry tidak memotong saldo dua kali |

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
1. Simplify phrases that would sound unnatural in a professional conversation.
   Check that each title identifies the concrete work and target, rather than only
   its benefit or a situation to handle, and that each technical term adds precision.
2. Remove filler and unsupported claims.
3. Replace vague placeholders with the missing information and its impact.
4. Confirm all source facts, numbers, identifiers, boundaries, and pending
   decisions survived the edit unchanged.

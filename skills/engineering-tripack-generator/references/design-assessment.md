# Design Assessment

Read at the assessment step in every mode. Use the quality criteria for all changes;
apply the domain criteria when models or domain rules are affected. Scale the depth
to the change's impact. A short reason to retain a simple design can be sufficient.

Contents: source basis; decision references; evidence before precedent; domain
assessment; design quality; conditional patterns; contracts across paths; worked
decisions; completion.

## Source basis

These summaries attribute the concepts. The assessment questions, evidence criteria,
KISS criterion, completion gate, and handoff rules below are our proposed workflow,
not requirements prescribed by the authors.

- Eric Evans's [Domain-Driven Design Reference](https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf)
  (2015, CC BY 4.0), especially *Putting the Model to Work*, *Building Blocks of a
  Model-Driven Design*, and *Core Domain*: language has meaning within a bounded
  context; domain experts and developers evolve the model together; code and model
  inform each other. Entities, value objects, services, aggregates, repositories,
  and factories address different modeling needs. Aggregates delimit consistency.
- Andrew Hunt and David Thomas's [The Pragmatic Programmer — Tips](https://pragprog.com/tips/),
  tip 15: DRY concerns a single authoritative representation of knowledge.
- Robert C. Martin's [The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)
  (2014): reasons for change relate to the people or business functions a module
  serves, rather than its method count.
- Martin's [SOLID Relevance](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)
  (2020): separate different reasons for change; support extension; preserve the
  meaning of interfaces across implementations; limit caller dependencies; direct
  dependencies toward abstractions.

## Decision references

Use sources according to the question they can answer. These roles are not a new
instruction-precedence hierarchy; resolve binding conflicts under Design Authority
and Handoff in `../SKILL.md`.

| Source to consult | Role in a decision |
|-------------------|--------------------|
| Supplied requirements, business-rule IDs, and user decisions | Establish required behavior, scope, and accepted constraints. |
| Applicable project instructions and active architecture decisions | Establish explicitly required structure; check scope, status, and the stated reason. |
| Actual code and callers, storage mappings, tests, and available data | Establish current behavior, integration obligations, and compatibility evidence; existing structure is not proof of suitability. |
| Domain assessment, Design quality, Conditional patterns, and Contracts across paths below | Select relevant evaluation criteria and compare viable designs against the present requirements. |
| Primary references linked in Source basis | Clarify the underlying concepts when the local criteria leave a material interpretation uncertain. |

Start with the supplied artifacts and applicable local sources. Read the relevant
assessment criteria before choosing a structure. Consult the linked primary source
when uncertainty about a concept could change the decision; for a technology-specific
claim outside this reference, consult official documentation matching the project's
version. Read the relevant passage rather than citing an unread link or relying on
remembered authority. Additional research is unnecessary when the available criteria
and evidence already settle the choice.

For each material decision, make this reasoning traceable in the existing technical
design sections, using a short paragraph or the structure-decisions table:

**Rule and source → problem → viable alternatives → comparison using applicable
criteria and observed evidence → decision and accepted consequences.**

Identify sources precisely enough to inspect: requirement ID or user decision,
instruction/ADR path and section, code path and symbol, test case, or external link
and relevant concept. Explain how the criterion applies to these paths and callers;
naming DDD, SOLID, or an author alone is not a justification. Separate observed facts
from inference, assumptions, and planned verification. If a necessary source is
unavailable, record the evidence gap and its impact; defer dependent decisions only
when that gap prevents a defensible choice. Never present a proposed test as proof
that a design already works.

## Evidence before precedent

Treat existing code as evidence of current behavior and integration constraints,
not proof that its structure is suitable. Assess the present requirements first,
then compare the existing shape with the relevant candidate from this reference.
Retaining a shape and introducing one carry the same burden of justification.

For a material choice influenced by precedent, record in the technical design:

- The binding rule or constraint and its source. Separate required behavior and
  compatibility from incidental implementation choices.
- How the relevant candidate would own the rule and what callers would do on an
  ordinary and invalid-input path, compared with the proposed choice.
- The concrete benefit or cost that decides the comparison. If departing from an
  applicable recommendation here, explain why its benefit does not apply or which
  demonstrated constraint/cost outweighs it; name how the chosen design supplies
  the required guarantees.

“Another feature does it,” fewer types, and private fields alone do not settle that
comparison. Private fields are useful enforcement evidence, but also inspect all
creation, mutation, and restoration paths and the obligations left to callers.
Different rules for similarly named concepts argue against sharing their policy;
they do not by themselves argue against modeling each concept separately.

## Domain assessment

Use business terms in the context where they have the stated meaning. DDD is a way
to understand and express the problem, not a requirement to add layers or patterns.

| Concern | When relevant and assessment question | Tradeoff or misuse risk | Evidence to record or plan |
|---------|---------------------------------------|-------------------------|----------------------------|
| Language and context | A term affects a rule, boundary, or interface: does it have the same meaning for product, code, and callers in this context? | Sharing a name across contexts can conceal incompatible meanings. | Requirement examples and the corresponding code/contract terms; name the context and any translation. |
| Collaboration on ambiguity | Competing interpretations would change behavior: what concrete example distinguishes them, and who can resolve it? | Guessing creates a business rule; escalating every internal choice stalls work. | The unresolved example, affected tasks, decision needed, and applicable source or decision owner. |
| Core domain focus | The change touches distinguishing business behavior: where should modeling effort protect it? | Elaborate modeling of generic plumbing can obscure the important rule. | The business capability and failure consequence that justify the effort; a simpler option for supporting work. |
| Model and code alignment | A model or implementation finding changes understanding: can the code express the agreed concept directly? | Freezing an early diagram forces awkward code; renaming alone does not repair behavior. | Mapping from concept to responsible component and a representative call/flow; findings that require a model revision. |
| Invariants and consistency | Values or state changes must satisfy rules: who owns each invariant and which paths could bypass it? | Handler-only checks miss jobs/restoration; an oversized boundary adds contention. | Rule → owner → enforcement path → proof plan, including transaction/concurrency cases when material. |

## Design quality

Assess SRP here as part of SOLID, without a second responsibility checklist.
Evaluate the complexity callers and maintainers must handle, not counts of types,
methods, interfaces, or files.

| Criterion | When relevant and assessment question | Tradeoff or misuse risk | Evidence to record or plan |
|-----------|---------------------------------------|-------------------------|----------------------------|
| DRY | The same rule appears in several paths: is it the same knowledge, with one reason to change? | Combining similar syntax with different meanings couples unrelated policies. | Identify the authoritative rule, its consumers, and a change example that must remain consistent. Distinguish transport checks from domain policy. |
| KISS (our criterion) | Compare plausible structures: which has the lowest total complexity for the present requirements, including callers, conversions, errors, and tests? | Fewer lines may push obligations to callers; extra abstractions may hide a straightforward flow. | Walk one ordinary call and one failure through the chosen structure and a material alternative; explain the work each imposes. |
| SRP | A component mixes policies or concerns: do they respond to independent business actors or reasons for change? | One type per method fragments cohesive behavior. | Name the distinct change drivers and show what changes together or independently. |
| OCP | Required variants exist: where can a variant be added without changing unrelated policy? | Designing speculative extension points adds indirection with no current benefit. | Name the required variants and show their extension path; retain a branch when that is simpler for the known cases. |
| LSP | Callers can use multiple implementations: do all preserve the same accepted inputs, results, errors, and effects? | Matching signatures can hide stricter preconditions or different failure behavior. | Representative caller scenarios and shared behavioral checks for implementations, including failure cases. |
| ISP | A caller depends on a broad interface: which operations does it actually need? | Mechanical splitting creates adapters without reducing meaningful coupling. | Caller-to-operation mapping and an example of an irrelevant dependency removed or reasonably retained. |
| DIP | Domain/application policy encounters storage, transport, or other details: does the dependency direction keep policy independent where needed? | An interface around every concrete type can increase complexity without isolating policy. | A dependency sketch and a concrete testing, replacement, or change-isolation need; explain when direct use is adequate. |

## Conditional patterns

Choose patterns for a demonstrated problem. Existing project conventions may constrain
the choice; distinguish an explicit requirement from an incidental existing shape.

| Candidate | When relevant and assessment question | Tradeoff or misuse risk | Evidence to record or plan |
|-----------|---------------------------------------|-------------------------|----------------------------|
| Entity | Does the concept retain identity through changes over its lifecycle? | Giving identity to a descriptive value complicates equality and persistence. | Identity examples, lifecycle transitions, and behavior before/after mutation. |
| Value object | Does a value have domain meaning, equality, normalization, or validity rules worth owning together? | A wrapper that callers can bypass adds ceremony; enforcing creation rules on restoration may break old data. | One owned rule and simpler caller usage; valid/invalid/boundary values, equality/normalization, and relevant path contracts. Immutable state, a validated constructor, and accessors may suffice. |
| Aggregate | Which changes must preserve invariants atomically, and what is the smallest boundary that can own them? | A boundary chosen from table relationships may cause locking or weaken consistency. | Invariant, root and mutation entry points, transaction/concurrency proof plan, and treatment of cross-boundary coordination. |
| Domain service | Is a domain operation naturally shared across concepts with no suitable entity/value owner? | A service can drain behavior from its natural owner or absorb orchestration and I/O. | Explain why an entity/value is unsuitable and distinguish domain policy from application coordination. |
| Repository | Does aggregate access need a domain-facing abstraction that hides retrieval/persistence mechanics? | Generic CRUD wrappers can leak schema or create unnecessary indirection. | Required caller queries/writes, aggregate boundary, mapping responsibility, and relevant persistence checks. |
| Factory | Is creation sufficiently complex that callers should not assemble valid state themselves? | A separate factory for trivial construction obscures the flow; reusing it for restoration can change behavior. | Construction invariants and caller simplification, with distinct new-input/restoration cases where needed. |

A boundary or transaction change is material when consistency guarantees or
responsibilities change, even when public signatures remain identical. Expose
effects on other components and consumers before classifying it as internal refinement.

## Contracts across paths

Before moving validation into a constructor, value object, or shared helper, compare
the affected paths. Read actual callers, storage mappings, and existing checks;
record uncertainty rather than assuming old state obeys today's creation rules.

| Path | Questions to settle | Useful proof plan |
|------|---------------------|-------------------|
| Creation / new input | Which raw values are accepted? Which normalization occurs, in what order, before which effect? | Valid, invalid, boundary, and simultaneously invalid inputs; exact observable error precedence and no unintended writes/events. |
| Restoration | Which persisted states can be loaded? Are values trusted, validated, normalized, or migrated? Does loading invoke creation policy? | Representative old rows/events and current data shapes, round trips, previously accepted states, load failures and their timing. |
| Transition | Which source states and values permit the action? Who enforces the invariant, atomically if needed? | Allowed/forbidden transitions, retries/concurrency where relevant, error precedence, and side effects on success/failure. |

For each affected path compare accepted inputs/states, normalization, error meaning
and ordering, failure timing, side effects, and compatibility for callers and data.
Different path contracts can be legitimate; document how ownership supports them
without copying the same knowledge. An inability to represent old state in a new
value type requires an explicit compatibility choice, not an unchecked bypass.

Label evidence accurately: an existing test or data sample is observed evidence;
a test to be added is a proof plan. Passing tests only support the cases they cover.

## Worked decisions

### Retain a simple structure

Mode B adds a `format` option to a report command. Discovery finds one command
function and two required output variants, both using the existing formatter.
No domain rules or stored state change. Retain the function and a local branch:
it owns option validation, and a strategy interface would add wiring without
reducing caller obligations. Plan checks for both formats, an unknown format,
and unchanged default output. Record domain assessment as not applicable and
the structure as retained; do not invent entities or repositories.

### Use a value object where it helps

A publishing context permits creation of a title after trimming boundary whitespace,
with a length of 1–120 Unicode code points. Several creation callers currently copy
this policy. Choose an immutable `Title` value owning this creation rule and its
defined normalization; the `Article` aggregate owns the draft-to-published transition
and publication invariants. Callers pass a validated value instead of repeating
checks. An alternative shared function leaves callers holding unchecked strings;
the value earns its cost if it actually prevents that misuse.

Record rule owners and creation enforcement, plus tests for whitespace, Unicode,
length boundaries, and competing errors. Before using the same construction path
for loaded articles, inspect the restoration contract and existing data. This is a
structural candidate, not permission to strengthen restoration validation.

### Existing primitive titles do not settle a new title design

A new blog title must be trimmed and contain 10–200 Unicode code points. An
existing task title uses a string with a 1–100 rule. Assess a blog-specific `Title`
against keeping a string inside `Blog`; the different limits rule out casually
sharing the task policy, not a blog-specific value object. Compare who normalizes
and validates input, what create/update callers can pass, and how restoration
preserves its contract. Where a validated value removes unchecked-string handling
and centralizes meaningful policy, choose it even though the task feature uses a
string. Leave the unrelated task implementation outside scope.

Retaining a string requires a concrete comparison showing why aggregate-owned
policy gives adequate enforcement with lower total complexity for these paths.
A list of constructors and private fields without this comparison is an incomplete
assessment. The title example guides the comparison; it is not a universal mandate
to wrap every string.

### Restoration exposes a behavioral decision

Mode C finds a proposed `Title` constructor shared by creation and restoration.
The current loader accepts historical blank titles; the proposal rejects them during
load. New-input rules are unchanged, but accepted persisted state and failure timing
would change. Classify the difference as an unresolved decision (or a contract
violation if already implemented against a binding compatibility promise).

Compare keeping a representation that supports historical state with its explicit
path policy, and migrating/rejecting old data under an approved compatibility plan.
Name the responsible decision owner and block affected loader/publish tasks until
resolved. Keep unrelated work available. Add code reconciliation only if code needs
to change after that decision; documentation alone may need revision for a proposal.
Preserve valid completed tasks and existing IDs in either case.

## Completion

Record findings inside the technical design's relevant sections, not a separate
assessment report or a copied checklist. Every material rule needs an owner,
enforcement path, and proof plan; every material structure choice needs a reason,
material alternatives/tradeoffs, and a refinement boundary. Mark the outcome
**retained**, **revised**, or **deferred** with a reason and any decision needed.
Check that each material decision is traceable through Decision references above.
A choice justified only by precedent, or a departure from an applicable candidate
without the comparison in Evidence before precedent, has not passed assessment.
Revise its rationale or design before deriving dependent strategy/tasks; defer it
only when material evidence or a decision is actually missing.
For deferred choices, identify the blocked work and condition for readiness.

Derive strategy, dependencies, and DoD from the assessed design. If implementation
reveals a material refinement, use Mode C to synchronize affected documents before
dependent work starts and at task closure. Local structural edits do not require
a log of every step. The generator specifies this handoff; applying it in a separate
executor is follow-up work.

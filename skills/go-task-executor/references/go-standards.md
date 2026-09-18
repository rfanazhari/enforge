# Go Backend Coding Standards (extracted, execution-relevant subset)

Source: internal `BE_Coding_Standards.md`. This file keeps only the sections
needed to write or modify Go code — package naming, structs/constructors,
error handling, testing/TDD, Clean Architecture rules, DDD tactical design,
folder layout, and observability. Language-agnostic sections (SQL, TS, PHP,
Python, Lua) are intentionally omitted.

## Table of Contents
1. Naming Conventions
2. Struct / Constructor Rules
3. Variables & Constants
4. Formatting & Linting
5. Testing (TDD)
6. Code Organization (folder layout)
7. Error Handling
8. Clean Architecture Rules
9. DDD Tactical Design
10. Log Standardization
11. Observability (tracing spans)

---

## 1. Naming Conventions

- Package names: lower case, single word, no underscores or mixedCaps.
- Getter for an unexported field `owner` is `Owner`, not `GetOwner`.
- Type methods matching a well-known interface reuse its name (e.g. `String()`, not `ToString()`).
- MixedCaps / mixedCaps for multiword names, never underscores.
- Error is always the last return value: `func (f *File) Write(b []byte) (n int, err error)`.
- **Private functions come first in the file, before all public functions.**
- All functions/variables scoped to one package only should be private; default to private.

```go
// Do this
func helloWorld() { log.Println("Hello world!") }
func main() { helloWorld() }
```

## 2. Struct / Constructor Rules

- All methods (functions with a receiver) and constructors for a type live in the same file.
- **Order inside a file: methods first, then the constructor, then private helpers last.**
- A constructor MUST include its business validation and return `(T, error)`.
- Constructors are used for commands/inputs coming from use cases — NOT for reconstructing data read back from a repository (use a plain struct literal for that).
- Structs use public fields.

```go
type Name struct {
    First string
    Last  string
}

func (n Name) Full() string { return n.First + " " + n.Last }

func NewName(first, last string) (Name, error) {
    if first == "" {
        return Name{}, errors.New("first name cannot be empty")
    }
    return Name{first, last}, nil
}
```

## 3. Variables & Constants

- Private package-level vars/consts go at the top of the file, grouped in a `var (...)` / `const (...)` block — never one `var` per line.

## 4. Formatting & Linting

- `gofmt` output is non-negotiable (VS Code Go extension / GoLand handle this automatically).
- `golangci-lint` must pass on every project — treat lint failures as build failures.

## 5. Testing (TDD)

- TDD is mandatory for **new** domain/use-case logic: write the failing test first, then the implementation.
- Unit tests are mandatory for the **domain** layer, minimum **80% coverage**. Infrastructure layer tests are optional (but encouraged for repositories/mocked externals).
- Use [Testify](https://github.com/stretchr/testify); the `suite` package is optional.
- Use `go-sqlmock` (database/sql) or `pgxmock` (pgx) to assert SQL queries when relevant.
- Don't create mocks you don't use in a test.
- Build Testify mocks manually (don't auto-generate) using `github.com/stretchr/testify/mock`.
- Test files live in the same package as what they test: `email_test.go` next to `email.go`.
- Test fixtures go in `test/testdata`; infra tests go in `test/infrastructure`.
- Test-data factories must return pointers/slices fresh per call (avoid shared-pointer bleed between tests):

```go
GraPARI = func() *grapari.GraPARI {
    g, _ := grapari.NewGraPARI(Name, Coordinate)
    return g
}
GraPARIs = func() grapari.GraPARIs { return []*grapari.GraPARI{GraPARI()} }
```

### Legacy-code testing note (not in the original standards doc, added for execution guidance)
When the touched legacy code has no existing tests, write a **characterization
test** capturing current behavior before changing anything — this is the
safety net, not classic TDD. Once a change introduces genuinely new logic
(a new branch, a new use case) inside that legacy file, switch to normal
red-green-refactor for that new logic. Don't force a full TDD rewrite of
surrounding legacy code that is out of scope for the task.

## 6. Code Organization (folder layout)

Sonar coverage scanning only targets `domain` and `application`.

| Folder | Purpose |
|---|---|
| `api` | OpenAPI/Swagger, JSON schema, protocol defs |
| `build` | Dockerfile, CI packaging |
| `cmd/<app>` | Executable entrypoint, one dir per binary |
| `docs` | Design/user docs |
| `domain/aggregate` | Aggregates |
| `domain/enum` | Enums |
| `domain/entity` | Entities |
| `domain/event` | Domain events |
| `domain/valueobject` | Value objects |
| `domain/repository` | Repository interfaces |
| `domain/service` | Domain services |
| `application/usecase` | Use cases |
| `application/repository` | Application-level repository interfaces |
| `infrastructure/repository` | Repository implementations |
| `infrastructure/service` | Service implementations |
| `delivery` | HTTP/gRPC routing/handlers |
| `migrations` | SQL migration files |
| `pkg/errorcodes` | Error codes + messages |
| `pkg/*` | Other public/shared modules |
| `proto` | Protobuf + Buf config + generated code |
| `test/testdata`, `test/infrastructure` | Test fixtures & integration tests |

## 7. Error Handling

- Error codes live in `pkg/errorcodes/code.go` as an `errors.New(CODE)` var per code, e.g. `ErrCTPF01001 = errors.New(CTPF01001)`.
- Error messages live in `pkg/errorcodes/message.go`, keyed by error var, with an English + Bahasa Indonesia switch and a `Message(err, opts...)` accessor.
- **Return predefined errors — never construct ad-hoc `errors.New("...")` inline in business/repository code.**
- Map errors to transport status centrally (HTTP status / gRPC `codes.*`) via a single `Header(err error) int` (or gRPC equivalent) function, not scattered per-handler.
- gRPC methods return `status.Error(codes.X, "message")`; follow https://grpc.io/docs/guides/status-codes/.
- JSON API envelope: `{status: success|fail|error, error: {message, code, errors: [...]}, data: {...}, meta: {pagination: {...}}}`.

## 8. Clean Architecture Rules (do not break these)

| Rule | Description |
|---|---|
| Dependency Rule | Domain/use-cases never depend on frameworks/DB; dependencies point inward. |
| Framework independence | Domain + use-cases stay framework-agnostic. |
| Isolated business rules | Business logic lives in domain/use-cases only, not in I/O or framework code. |
| Testing independence | Each layer testable in isolation via mocks/fakes. |
| Interface-driven design | Depend on interfaces, not concrete implementations. |
| Data flow via DTOs | Pass data across layers with structs/DTOs specific to that layer. |
| Single responsibility per layer | No logic leaking across layer boundaries. |
| Limited framework exposure | Don't leak DB entities into use-case/domain layers. |
| Persistence ignorance | Domain/use-case layers don't know how/where data is persisted. |
| Centralized error handling | Translate infra errors into domain/use-case errors at the boundary. |
| Flexibility across layers | New infra shouldn't ripple into unrelated layers if interfaces are respected. |

## 9. DDD Tactical Design

- Understand and contribute to domain models (class diagrams) before implementing.
- Domain models/class diagrams are mandatory (senior devs design them; junior/mid devs get SA help, optional for them).
- Use ubiquitous language shared with business stakeholders — no leaking technical jargon into domain naming.

## 10. Log Standardization

- Use `logmanager` (`scm.salt.id/salt-library/salt-pkg/logmanager`) instead of `log`/Logrus.
- Logs are JSON, one segment per traced operation:

```go
txn := logmanager.StartOtherSegment(ctx, logmanager.OtherSegment{Name: "your-process"})
defer txn.End()
```

- Never log secrets (passwords, tokens). Always propagate `trace_id`.

## 11. Observability (tracing spans)

Every layer that does real work should open a span, named by convention:

| Layer | Span name pattern | Example |
|---|---|---|
| HTTP controller | `http.controller.<method>.<path>` | `http.controller.GET./users/{id}` |
| Kafka/RabbitMQ consumer | `message.consumer.<topic|queue>` | — |
| External API call | `external.api.<service>.<method>` | `external.api.userService.getUserById` |
| Redis cache | `cache.redis.<op>.<keyPattern>` | `cache.redis.GET.user:{id}` |
| Repository/DB | `repository.db.<entity>.<operation>` | `repository.db.UserRepository.FindByID` |

- Propagate `traceparent` (W3C Trace Context) across HTTP, gRPC metadata, and MQ message headers; keep the same `trace-id`, new `span-id` per hop.
- Mask/exclude PII in any span attributes or logs.

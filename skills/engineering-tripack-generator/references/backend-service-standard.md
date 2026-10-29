# Backend Service Standard

## Clean Architecture layers

```
delivery/        HTTP/gRPC handlers + middleware (no unit tests here)
application/     use cases, query handlers, DTOs, application services, events
domain/          aggregates, entities, value objects, enums, domain events,
                 repository interfaces — pure business logic, no I/O
infrastructure/  repository implementations (GORM/pgx), external clients
```

Dependencies point inward. `domain/` depends on nothing outward; `infrastructure/` implements
interfaces declared by `domain`/`application`.

## Directory scope and design discretion

This document is the generator's default baseline for Go backend work, with the
layer layout scoped to Clean Architecture services. Explicit user and project
instructions take precedence. Within that scope, its explicit rules are constraints;
unspecified details remain design choices.

The layer layout above is required. It does not prescribe separate subpackages for
aggregates, entities, or value objects. For unspecified placement, follow consistent
repository conventions that fit the requirements. If no suitable convention exists,
choose the simplest structure that meets the requirements and record the reason for
material structural decisions in the technical design.

Classify domain concepts by their responsibilities, independently of package names
and ownership. A value object held by an aggregate remains a value object, including
when both live in `domain/aggregate`. Inventory entries must distinguish each item's
classification, package location, and ownership relationship.

## Go language rules

- Use `any`, not `interface{}`.
- Package names: short, lowercase, single word (no underscores, no mixedCaps).
- Multi-word identifiers: `MixedCaps` / `mixedCaps`.
- Private functions appear **before** public ones in a file.
- Private vars/consts grouped at the top of the file in a `var ( … )` block.
- `error` is always the last return value.
- Don't re-alias types across layers (`type Specifications = query.Specifications`) — import
  and use directly.

## Structs & constructors

- Use defined structs (no anonymous structs); public fields.
- Add a constructor `NewX` **only** when it performs business validation and returns an error.
- **No** constructors in repositories or infrastructure — use plain structs.
- A struct, its methods, and its constructor live in the **same file**.

Example value object with validation (domain layer):

```go
type NanoID string

func ParseNanoID(id string) (NanoID, error) {
    if id == "" {
        return NanoID(""), nil
    }
    if len(id) != NanoIDLength {
        return "", errs.ErrInvalidNanoIDLength
    }
    return NanoID(id), nil
}
```

## Testing

- Cover `domain/`, `application/`, and `infrastructure/` only — **never** `delivery/`.
- Table-driven, named struct slice; `tt` loop var; `t.Run(tt.name, …)`.
- First field `name string`; include `wantErr bool` when the function returns an error.
- testify: `assert.*` (non-fatal), `require.*` (fatal); `expected` before `actual`.
- Use `assert.ErrorIs(t, err, ErrX)` for specific errors; never `assert.True(t, a == b)`.
- All tests in `_test` packages; don't test private functions directly.
- Build objects with factory functions, never raw struct literals; reuse
  `test/testdata/testdata.go`. Generate mocks with `mockery` (`.mockery.yml`) — never hand-write.

## Performance

- Use maps for O(1) in-memory lookups/dedup; slices for ordered/repository data.
- Prefer a single JOIN over multiple sequential DB queries.
- Parallelize independent I/O with goroutines.

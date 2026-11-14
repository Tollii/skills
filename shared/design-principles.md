# Shared design principles

This is the generic design reference for lifecycle skills. Keep task workflow in the local skill files; use this file for reusable design judgment.

## Boundary-first design

Treat loose data as a boundary concern. Convert it into the representation the rest of the system wants before domain behavior or side effects depend on it.

Common boundaries:
- HTTP/API DTOs and generated clients
- UI forms, route params, and query strings
- persistence rows, documents, and projections
- external service responses and webhooks
- configuration, environment variables, and feature flags
- event replay or import inputs
- weak-language or reflection-heavy interop surfaces

Schema or wire-shape parsers prove structure. Domain parsers prove business meaning. Keep those responsibilities separate when both are present: shape first, then domain meaning before domain behavior runs.

## Parse, don't validate

Validation that returns `void`, `bool`, or only throws often proves a fact and then discards it. Prefer a parser, mapper, smart constructor, or factory method that returns a stronger representation.

Use this when:
- downstream code repeats null, flag, range, status, permission, or shape checks
- code contains "should never happen" branches after a prior check
- raw strings, loosely typed IDs, optional bags, or generic maps flow into domain code
- a `Kind`/`Type` field controls which unrelated optional payload fields are meaningful

Guidance:
- Push proof to the earliest responsible boundary, but no further than the point where the needed branch is known.
- Let downstream functions accept the trusted representation they need.
- Prefer making illegal calls impossible by strengthening function arguments over accepting broad inputs and defending inside the function.
- Keep wire contracts separate from internal domain/value types.
- Accumulate independent boundary-field errors when the caller can fix them together.
- Short-circuit dependent domain steps with explicit outcomes when later steps rely on earlier facts.

## Mock roles, not objects

Use collaboration tests as a design probe, not as a way to mirror object structure. Start from the object under test, name the service it provides, then discover the immediate-neighbour roles it needs.

Guidance:
- Mock only application-owned role interfaces. Wrap vendor, framework, runtime, and SDK APIs behind owned seams before mocking.
- Stub collaborator queries that provide answers. Expect collaborator commands that represent required effects.
- Specify order, call counts, exact arguments, and negative expectations only when the requirement depends on them.
- Treat deep mock graphs, smart mocks, hidden `new` calls, static dependency access, or many collaborators as design feedback.
- Prefer state/value assertions for pure functions, parsers, mappers, and value objects.

## Responsibility-driven types

Repeated checks usually mean a responsibility has no home. Name the responsibility first, then decide whether it belongs in a value object, parser, discriminated outcome, aggregate method, state type, collaborator role, or orchestration type.

Use semantic types when they remove repeated guards, clarify legal state, or prevent meaningful mistakes. Do not introduce heavy abstractions just to apply a pattern.

Prefer variant-specific payloads over one broad record with a discriminator plus unrelated optional fields. State transitions and event replay should reject impossible histories explicitly instead of silently ignoring them.

## FP-friendly implementation

Prefer explicit inputs and outputs, small pure transformations, and composition when they make behavior easier to read. Use repo-local modern syntax that reduces ceremony:
- records or value objects for named facts
- discriminated unions or sealed outcomes for meaningful states
- switch/pattern matching for exhaustive branching
- immutable transformations when mutation adds no clarity

Avoid style churn. Modern syntax is useful only when it makes intent easier to scan and harder to misuse.

## Smell-to-risk review

Use Fowler-style code smells as evidence, not as automatic findings. A smell matters when it points to a concrete correctness, maintainability, or evolution risk.

Review questions:
- Could duplicated registration, mapping, validation, or branching run side effects twice, create duplicate service instances, or let coupled logic diverge?
- Does the code check state and then act later, leaving a stale-read or race window that should be collapsed into one conditional write, transaction, or explicit re-read?
- Does one feature change require edits across many unrelated files, suggesting a missing slice boundary or registration point?
- Is behavior living in the wrong place, such as a controller knowing feature internals, a test mock carrying production logic, or a service reaching through another object for data?
- Do names, stale imports, or mixed old/new patterns obscure the boundary enough that the next change is likely to happen in the wrong place?

For reviews, report the risk, not just the smell. Prefer small local improvements when they remove real complexity, and avoid broad rewrites unless the current shape is actively causing risk.

## Lifecycle responsibilities

- Research detects loose boundaries, repeated validation, hidden responsibilities, foreign API seams, and mock friction.
- Planning turns those findings into concrete boundary/type/seam steps with ownership and validation.
- Implementation applies the planned shape without scattering new guards or broad abstractions.
- Plan critique challenges missing parse points, unowned invariants, foreign mocks, and style-driven churn.
- Review verifies that trusted representations stay inside the boundary, collaborator tests express meaningful protocols, and repeated checks were not reintroduced.
- PR-comment follow-up preserves the planned boundary/type/seam shape or surfaces conflicts.

## Smells and fixes

| Smell | Prefer |
| --- | --- |
| `void`/`bool` validator followed by raw data use | parser or smart constructor returning trusted data |
| nullable or optional bags passed inward | semantic input type with named required/optional parts |
| `Kind`/`Type` plus unrelated optional payload fields | discriminated variants with variant-specific fields |
| raw string IDs, statuses, kinds, or permissions | value type, enum, discriminated case, or owned constant set |
| repeated guards across call sites | type that owns the invariant |
| function accepts broad input then defensively rejects most of it | stronger argument type or trusted parsed input |
| replay or state transition silently ignores impossible history | explicit invalid-transition outcome |
| duplicated registration/mapping/branching | single owner or idempotent composition point |
| check-then-act around mutable state | conditional write, transaction, or re-read that distinguishes missing vs changed state |
| one change scattered across many files | slice boundary, feature registration point, or moved responsibility |
| stale names/imports after a move | cleanup or note when they weaken the intended boundary |
| broad helper extraction for repeated validation | named responsibility with a clear contract |
| vendor/framework type mocked in unit tests | application-owned wrapper or role interface |
| deep mock graph or getter chain | immediate-neighbour role with Tell, Don't Ask protocol |
| smart mock containing behavior | production collaborator or pure function |
| exact order/count assertions everywhere | only specify protocol details the requirement depends on |

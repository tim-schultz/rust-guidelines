# Rust Guidelines

Apply these rules to changed and affected code in any Rust project. Microsoft
guideline IDs identify the source principles; the requirements here are the
adopted policy. Review applicable rules explicitly: passing tools alone does
not establish semantic conformance.

## Design and organization

- Keep abstractions simple and modules focused. Separate independently useful
  components into crates when that improves their interfaces or dependency
  boundaries. Avoid speculative wrappers, visibly nested abstractions, and
  unnecessary generic machinery. (`M-SIMPLE-ABSTRACTIONS`, `M-SMALLER-CRATES`,
  `M-BALANCED-MODULES`, `M-AVOID-WRAPPERS`)
- Separate calculations and decisions from acquisition, storage, clocks, and
  application setup. Borrow caller-owned inputs where appropriate; inject I/O
  and time at boundaries instead of reading global state inside calculations.
  (`M-MOCKABLE-SYSCALLS`, `M-AVOID-STATICS`)
- Use strong types for values with different meanings. Validate external inputs
  when they enter the system, and prevent invalid states from reaching core
  logic. Preserve exact numeric representations when rounding would change an
  outcome. (`M-STRONG-TYPES`, `M-STRONG-TYPES-GUARD`)
- Use descriptive names and consistent parameter conventions. Prefer ordinary
  functions when an associated function adds no useful relationship to a type.
  Explain non-obvious constants where they are defined. (`M-REGULAR-FN`,
  `M-PARAMETER-CONSISTENCY`, `M-DOCUMENTED-MAGIC`)

## Public APIs and dependencies

- Expose essential operations through inherent methods so callers can discover
  them without importing a trait. Avoid leaking third-party types through
  public APIs unless they serve a deliberate interoperability boundary.
  (`M-ESSENTIAL-FN-INHERENT`, `M-DONT-LEAK-TYPES`)
- Give each public item one clear public path. Avoid glob re-exports and
  convenience preludes that obscure ownership. (`M-SINGLE-ITEM-PATH`,
  `M-NO-GLOB-REEXPORTS`, `M-NO-PRELUDE`)
- Public types implement `Debug`; types intended for human-readable output
  implement `Display`. Both must redact secrets. Verify redaction through the
  public interface in integration tests. (`M-PUBLIC-DEBUG`, `M-PUBLIC-DISPLAY`)
- Make public types and futures usable across threads where their semantics
  permit it. Review `Send` and `Sync` requirements at API boundaries.
  (`M-TYPES-SEND`)
- Cargo features are additive: enabling a feature must not disable previously
  available behavior or create conflicting meanings. Libraries should work
  with a normal build on every declared platform. (`M-FEATURES-ADDITIVE`,
  `M-OOBE`)
- Declare and test the minimum supported Rust version. Use the latest stable
  edition compatible with that support policy. Review dependency sources,
  licenses, versions, and enabled features; remove unused dependencies and
  preserve reproducible resolution. (`M-MSRV`, `M-LATEST-EDITION`)

## Errors and safety

- Keep project-owned Rust safe; forbid unsafe code. Review dependency safety
  separately, and never accept unsound code. (`M-UNSAFE`,
  `M-UNSAFE-IMPLIES-UB`, `M-UNSOUND`)
- Represent recoverable failures as errors rather than panics. Preserve error
  causes and useful diagnostic context. Missing or invalid input must produce
  an explicit outcome rather than an invented success value. (`M-PANIC-IS-STOP`,
  `M-STRONG-TYPES-GUARD`)
- Use canonical error structs for library APIs, with an intentional error kind,
  contextual information, and source chain as appropriate. Keep application
  reporting separate from reusable library error contracts.
  (`M-ERRORS-CANONICAL-STRUCTS`)
- Propagate fallible arithmetic and conversion failures. Do not silently change
  results, labels, or precision to keep execution moving.

## Documentation

- Document public APIs and modules. Start with a concise, single-line summary
  of approximately 15 words or fewer, then describe contracts and usage.
  (`M-FIRST-DOC-SENTENCE`, `M-MODULE-DOCS`)
- Include usable examples and applicable `Errors`, `Panics`, `Safety`, and
  `Abort` sections. Explain externally meaningful constraints and failure
  behavior. (`M-CANONICAL-DOCS`)
- Design APIs and examples so humans and coding tools can discover, validate,
  and use them correctly. Keep internal design history and compliance reports
  outside API documentation. (`M-DESIGN-FOR-AI`,
  `M-NO-META-DESIGN-DOCUMENTATION`)

## Logging and performance

- Use structured logging through a facade. Log stages at `info`, component
  decisions at `debug`, and individual events at `trace`; time expensive I/O
  and queries. This logging-depth rule is an adopted addition to the upstream
  guidance. (`M-LOG-STRUCTURED`; adopted `M-LOG-STORY`)
- Keep reusable calculations free of direct stdout/stderr printing. Application
  boundaries own output. Keep machine-readable stdout clean and redact
  sensitive fields in logs. (`M-LOG-NOT-PRINT`)
- Bound expensive work, profile hot paths, and benchmark before optimizing or
  claiming improvements. Choose allocators and concurrency strategies from
  measured requirements. (`M-HOTPATH`, `M-THROUGHPUT`)

## Testing and verification

- Every feature, bug fix, and behavior change requires integration tests of
  real components through public interfaces. Unit tests are prohibited.
  Follow [TESTING_GUIDELINES.md](TESTING_GUIDELINES.md), including
  RED → GREEN → REFACTOR and independent outcome assertions.
  (`M-INTEGRATION-TESTS`, `M-TAUTOLOGICAL-TESTS`)
- Run formatting, compiler, Clippy, rustdoc, and test gates with warnings
  rejected. Use pedantic and selected restriction lints in addition to default
  Clippy checks. (`M-STATIC-VERIFICATION`)
- Fix diagnostics rather than weakening checks. Item-level lint exceptions use
  `#[expect(..., reason = "...")]` with a specific justification. Review the
  [lint configuration](examples/workspace-lints.toml) for the chosen toolchain.
  (`M-LINT-OVERRIDE-EXPECT`)

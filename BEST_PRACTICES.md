# Development Best Practices

These practices apply to any Rust project alongside the
[Rust guidelines](RUST_GUIDELINES.md) and
[integration testing policy](TESTING_GUIDELINES.md).

## Development and completion

- Define input requirements, expected behavior, and acceptance evidence before
  implementation. Include a case that distinguishes the intended behavior from
  a plausible misinterpretation.
- Work in small, reviewable steps. Run focused checks while iterating, then
  complete the relevant quality gates before handoff. Reuse unchanged results.
- Report implemented capability separately from conclusions supported by
  evidence. Record limitations and checks not performed.
- Keep changes focused. Serialize builds that overwrite shared executable
  paths with different feature sets, or use separate target directories.

## Rust implementation

- Format with rustfmt. Use `snake_case` functions, `PascalCase` types, and
  `SCREAMING_SNAKE_CASE` constants. Document non-obvious constants.
- Keep calculations separate from I/O, clocks, storage, and application setup.
  Borrow inputs where appropriate and make ownership clear.
- Validate external input with strong types. Preserve diagnostic context and
  error source chains. Do not use panics for recoverable errors.
- Avoid global mutable state, speculative abstractions, and unnecessary
  dependency exposure. Keep Cargo features additive.
- Document public contracts with concise summaries and usable examples. Public
  types need `Debug`; readable types need `Display`. Redact secrets in both.
- Forbid project-owned unsafe code. Review dependency sources, licenses,
  versions, and features; keep dependency resolution reproducible.
- Log through a facade using structured fields. Narrate stages, decisions, and
  events at appropriate levels, and time expensive operations. Keep reusable
  logic free of printing and protect machine-readable output.
- Benchmark before optimizing or claiming performance improvements.

## Tests and evidence

- Integration tests are required for every feature, bug fix, and behavior
  change. Do not write unit tests.
- Follow RED → GREEN → REFACTOR through public APIs. Begin fixes with a
  reproducing integration test. Pure documentation and configuration changes
  without behavioral impact require appropriate validation.
- Assert observable outcomes against independently determined expectations.
  Cover meaningful success, invalid-input, boundary, and failure cases. Use
  appropriate tolerances for floating-point comparisons.
- Exercise real application components. Keep substitutes at external system
  boundaries; do not mock the logic under test or couple assertions to private
  implementation details.
- Keep fixtures deterministic and appropriate to the domain. Document their
  provenance when results depend on an external source. Name intentional
  alterations used to exercise edge cases.
- Keep large or raw acceptance evidence out of version control. Explicitly
  identify tests requiring external services or unavailable local evidence;
  report their execution separately and never silently skip a required gate.

## Quality and review

- Formatting, compilation, Clippy, rustdoc, and tests reject warnings. Fix
  diagnostics; never weaken checks merely to pass. Narrow lint exceptions
  require `#[expect(..., reason = "...")]` with a specific reason.
- Review changed and affected code against applicable guideline IDs. Update
  project applicability or enforcement records when they change; keep any such
  records in the adopting project.
- Review documentation links and examples. Validate changed shell scripts
  with syntax checks and ShellCheck when available; integration-test tooling
  behavior changes.
- Use concise imperative commit subjects. Pull requests describe resulting
  behavior, public API changes, relevant guideline findings, and validation.
  Numerical coverage targets do not replace required behavioral tests.

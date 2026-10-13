# Contributor and Agent Instructions

These instructions apply to Rust applications, libraries, and workspaces that
adopt this guideline set.

- Before writing, modifying, or reviewing Rust code, read the applicable rules
  in [RUST_GUIDELINES.md](RUST_GUIDELINES.md) and
  [BEST_PRACTICES.md](BEST_PRACTICES.md).
- Every feature, bug fix, and behavior change requires integration tests that
  exercise real components through public interfaces and assert observable
  outcomes. Follow [TESTING_GUIDELINES.md](TESTING_GUIDELINES.md).
- Do not write unit tests, including tests in source files or `#[cfg(test)]`
  modules. Documentation or clarification is not an exception.
- Follow RED → GREEN → REFACTOR. Start bug fixes with a failing integration
  test that reproduces the problem. Pure documentation and configuration
  changes without behavioral impact require appropriate validation.
- Keep project-owned Rust safe. Validate external inputs, preserve diagnostic
  context, and keep I/O and clocks explicit at boundaries.
- Document public APIs, redact secrets, and use structured logging without
  contaminating machine-readable output.
- Run focused integration tests while iterating and the full relevant test
  suite before handoff. Formatting, compilation, Clippy, and rustdoc must pass
  with warnings rejected.
- Fix diagnostics instead of weakening checks. Narrow lint exceptions require
  `#[expect(..., reason = "...")]` and a specific justification.
- Keep changes focused. Report validation results and material limitations;
  do not claim performance or correctness without supporting evidence.

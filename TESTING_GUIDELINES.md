# Integration Testing Policy

## Required scope

Every feature, bug fix, and behavior change must have integration tests.
Exercise real application components through their public interfaces and
assert observable outcomes. A feature is incomplete until its integration
tests and the relevant regression suite pass.

Unit tests are prohibited. Do not place tests in Rust source files or
`#[cfg(test)]` modules. Documentation or clarification does not create a
unit-test exception. Put integration tests in each crate's `tests/` directory;
test libraries through exported APIs and executables through their command
interfaces. Doctests may demonstrate public API usage, but do not replace
required integration tests.

This policy overrides recommendations for unit tests or a unit-test-heavy
testing pyramid in upstream guidelines and other reference material.

## RED → GREEN → REFACTOR

1. **RED:** Write an integration test describing the required observable
   behavior. Run it and confirm it fails for the intended reason. For a bug
   fix, reproduce the reported failure before changing production code.
2. **GREEN:** Make the smallest coherent implementation change that satisfies
   the test. Run the focused integration suite and confirm it passes.
3. **REFACTOR:** Improve the implementation while preserving its public
   behavior. Re-run the affected integration tests and existing regression
   tests, then complete the relevant quality gates.

Refactors preserve behavior through integration tests. Add a failing test
first when the intended behavior is not yet covered or changes.
Pure documentation, static content, and configuration changes without
behavioral impact may skip RED; validate their links, syntax, and examples.

## Test design

- Name tests after the behavior they verify. Include meaningful success,
  invalid-input, boundary, and failure scenarios for each feature.
- Construct expectations independently of the implementation. Do not call the
  same calculation to produce both the actual result and expected result.
- Assert returned values, emitted output, persisted state, error contracts, or
  other effects visible to callers. Avoid private helper assertions and
  implementation-specific call counts.
- Run real components together. Use isolated filesystems, local test servers,
  and test service instances where appropriate. Restrict substitutes to
  external boundaries; do not replace the feature's own logic with mocks.
- Keep tests deterministic and isolated. Make time, source inputs, and service
  endpoints explicit at public boundaries when behavior depends on them.
- Test public error precedence and diagnostic context when they are part of
  the contract. Verify secret redaction through public formatting or output.
- Exercise supported Cargo feature combinations that change public behavior.
  An all-features run alone does not prove mutually independent configurations
  work.
- Keep fixtures small, document their origin where relevant, and make edge
  case alterations explicit. Do not misrepresent fixture data as live evidence.

## Regression checks and handoff

Run focused integration tests during development and the full relevant suite
before claiming completion. For a Cargo workspace, typical checks are:

```sh
export RUSTFLAGS="${RUSTFLAGS:-} -D warnings"
export RUSTDOCFLAGS="${RUSTDOCFLAGS:-} -D warnings"
cargo fmt --all -- --check
cargo clippy --locked --workspace --all-targets --all-features -- -D warnings
cargo doc --locked --workspace --no-deps --all-features
cargo test --locked --workspace --all-features
cargo check --locked --workspace --no-default-features
```

Use the project's selected toolchain and additional supported feature/platform
checks. These commands preserve existing compiler flags while rejecting
warnings. Standalone crates omit workspace-specific options.
Run with default features separately when they differ from the all-features
configuration.

Live-service acceptance tests may be separate from deterministic integration
tests. Document their prerequisites and report whether they ran. A skipped
required acceptance check is an outstanding requirement, not a passing result.

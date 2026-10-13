# Rust Guidelines

Reusable development rules for new Rust applications, libraries, and workspaces.
The guidelines cover design, documentation, errors, logging, dependencies,
testing, and review without prescribing a domain, directory layout, runtime,
provider, or exact Rust version.

Every feature, bug fix, and behavior change requires integration tests through
public interfaces. Unit tests are prohibited.

## Apply to a project

1. Adopt [AGENTS.md](AGENTS.md) as contributor and coding-agent instructions.
2. Read [RUST_GUIDELINES.md](RUST_GUIDELINES.md) and
   [BEST_PRACTICES.md](BEST_PRACTICES.md) before changing Rust code.
3. Follow [TESTING_GUIDELINES.md](TESTING_GUIDELINES.md) for every feature,
   behavior change, and bug fix.
4. Merge the [workspace lint configuration](examples/workspace-lints.toml) into
   your workspace's `Cargo.toml`. Each member opts in with
   `[lints]` and `workspace = true`. For a standalone crate, use `[lints.rust]`
   and `[lints.clippy]` instead of `[workspace.lints.rust]` and
   `[workspace.lints.clippy]`.

Keep project-specific requirements and evidence in the adopting project.
Use its selected toolchain, supported platforms, and public interfaces when
applying these rules.

## Reference guidelines

Microsoft guideline IDs refer to version 2026.6 at
[revision c1d2efc4692630d5d2143b4ca446790005a714c9](https://github.com/microsoft/rust-guidelines/tree/c1d2efc4692630d5d2143b4ca446790005a714c9).
These documents express an adopted policy rather than reproducing the complete
upstream book. The integration-only testing policy overrides unit-test
recommendations in other reference material.

# Agent instructions

Read `recipes/magpie/BUILD_PROMPT.md`, its recursive dependencies and the relevant
acceptance specifications before implementation. Record actual coverage in
`recipes/magpie/COVERAGE.md`; preserve the complete requirement set.

Implementation lives in the independent Git submodule
`generated/magpie-rs-source/`. Keep `generated/` as a container for source
submodules and keep recipe payloads UTF-8 Markdown only. Source, packaging and
build scripts belong inside the source submodule. Commit and publish the source
before updating its parent gitlink; preserve unrelated changes in both repos.

Use the shared skills under `.agents/skills/` for Recipkg work. Rust and GPUI are
user requirements. CLI/headless builds must work without display/GPU startup.
Use isolated synthetic fixtures for tests and benchmarks, never real account
credentials. Store build artifacts outside this checkout. Distinguish local
build/test results, published packages, and actual OS/device execution.

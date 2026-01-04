# Shared GitHub Workflows

Reusable GitHub Actions workflows and composite actions for Embeddenator projects.

## 📋 Overview

This repository provides centralized CI/CD workflows and actions that can be reused across all Embeddenator repositories. Version at `@v1` ensures automatic updates while maintaining stability.

## 🔄 Reusable Workflows

### `reusable-ci.yml`

Main CI workflow with configurable options for Rust projects.

**Usage:**

```yaml
name: CI

on: [push, pull_request]

jobs:
  ci:
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: true
      run-benchmarks: false
      enable-fuse: false
```

**Inputs:**

- `rust-version` (string, default: `1.84`): Rust version to use
- `run-tests` (boolean, default: `true`): Run tests
- `run-benchmarks` (boolean, default: `false`): Run benchmarks
- `benchmark-isolation` (boolean, default: `false`): Enable CPU pinning and frequency locking
- `enable-fuse` (boolean, default: `false`): Install FUSE support
- `cache-key-prefix` (string, default: `cargo`): Cache key prefix

## 🧩 Composite Actions

### `setup-rust`

Install Rust toolchain with specified version and components.

**Usage:**

```yaml
- name: Setup Rust
  uses: tzervas/.github-workflows/.github/actions/setup-rust@v1
  with:
    rust-version: '1.84'
    components: 'rustfmt,clippy'
    targets: 'wasm32-unknown-unknown'
```

**Inputs:**

- `rust-version` (default: `1.84`): Rust version
- `components` (default: `rustfmt,clippy`): Comma-separated components
- `targets` (default: ``): Comma-separated targets

---

### `cache-cargo`

Cache Cargo registry, git dependencies, and build artifacts.

**Usage:**

```yaml
- name: Cache Cargo
  uses: tzervas/.github-workflows/.github/actions/cache-cargo@v1
  with:
    cache-key-prefix: 'cargo'
    save-on-fail: 'false'
```

**Inputs:**

- `cache-key-prefix` (default: `cargo`): Cache key prefix for multiple caches per repo
- `save-on-fail` (default: `false`): Save cache even if workflow fails

**Cache Keys:**

- Registry: `{prefix}-registry-{os}-{rust-version}-{Cargo.lock hash}`
- Build: `{prefix}-build-{os}-{rust-version}-{Cargo.lock hash}-{*.rs hash}`

---

### `setup-fuse`

Install and configure FUSE for filesystem testing.

**Usage:**

```yaml
- name: Setup FUSE
  uses: tzervas/.github-workflows/.github/actions/setup-fuse@v1
  with:
    fuse-version: '3'
    allow-other: 'true'
```

**Inputs:**

- `fuse-version` (default: `3`): FUSE version (2 or 3)
- `allow-other` (default: `true`): Enable `user_allow_other` in fuse.conf

---

### `benchmark-isolation`

Configure system for stable benchmark execution.

**Usage:**

```yaml
- name: Setup benchmark isolation
  uses: tzervas/.github-workflows/.github/actions/benchmark-isolation@v1
  with:
    cpu-cores: '0,1'
    disable-turbo: 'true'
    disable-hyperthreading: 'false'
```

**Inputs:**

- `cpu-cores` (default: `0,1`): CPU cores to pin benchmarks to
- `disable-turbo` (default: `true`): Disable CPU turbo boost
- `disable-hyperthreading` (default: `false`): Disable hyperthreading

**Environment Variables Set:**

- `BENCHMARK_CPU_CORES`: Cores allocated for benchmarks
- `CARGO_BENCH_OPTS`: Cargo bench options (`--jobs 1`)

## 🏷️ Versioning

Use `@v1` for automatic updates:

```yaml
uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
```

Or pin to specific release:

```yaml
uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1.0.0
```

## 📦 Complete Example

Full CI workflow with all features:

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:

jobs:
  test:
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: true
      enable-fuse: true
      cache-key-prefix: 'embeddenator-fs'

  benchmark:
    if: github.ref == 'refs/heads/main'
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: false
      run-benchmarks: true
      benchmark-isolation: true
      cache-key-prefix: 'embeddenator-bench'
```

## 🤝 Contributing

To update workflows:

1. Make changes in this repository
2. Tag new version: `git tag v1.x.x && git push --tags`
3. Update `v1` tag: `git tag -f v1 && git push --tags -f`

## 📄 License

MIT License - See [LICENSE](LICENSE)

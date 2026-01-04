# Using Shared Workflows in Embeddenator Projects

## Quick Start

Replace your existing `.github/workflows/ci.yml` with:

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
```

## Examples by Project Type

### Standard Library (VSA, Retrieval, IO, etc.)

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
      cache-key-prefix: 'embeddenator-vsa'
```

### Filesystem Library (with FUSE)

```yaml
name: CI

on: [push, pull_request]

jobs:
  ci:
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: true
      enable-fuse: true
      cache-key-prefix: 'embeddenator-fs'
```

### Benchmark Projects

```yaml
name: Benchmarks

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 0 * * 0'  # Weekly on Sunday

jobs:
  benchmark:
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: false
      run-benchmarks: true
      benchmark-isolation: true
      cache-key-prefix: 'embeddenator-bench'
```

### Core Orchestrator (All Features)

```yaml
name: CI

on:
  push:
    branches: [main, develop, 'feat/**']
  pull_request:

jobs:
  test:
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: true
      enable-fuse: true
      cache-key-prefix: 'embeddenator-core'

  benchmark:
    if: github.ref == 'refs/heads/main'
    needs: test
    uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1
    with:
      rust-version: '1.84'
      run-tests: false
      run-benchmarks: true
      benchmark-isolation: true
      cache-key-prefix: 'embeddenator-core-bench'
```

## Advanced: Custom Actions Only

If you need more control, use individual actions:

```yaml
name: Custom CI

on: [push, pull_request]

jobs:
  custom:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Rust
        uses: tzervas/.github-workflows/.github/actions/setup-rust@v1
        with:
          rust-version: '1.84'
          components: 'rustfmt,clippy,miri'
      
      - name: Cache Cargo
        uses: tzervas/.github-workflows/.github/actions/cache-cargo@v1
        with:
          cache-key-prefix: 'custom'
      
      - name: Custom build steps
        run: |
          cargo build --release
          cargo test --release
          cargo clippy -- -D warnings
```

## Migration Guide

### 1. Component Libraries (6 repos)

**Before:**
- embeddenator-vsa
- embeddenator-retrieval
- embeddenator-fs
- embeddenator-interop
- embeddenator-io
- embeddenator-obs

**Action:** Replace CI with standard library template above. For `embeddenator-fs`, use the FUSE template.

### 2. Phase 1 Repos (8 repos)

**Before:**
- embeddenator-testkit
- embeddenator-contract-bench
- embeddenator-agent-mcp
- embeddenator-context-mcp
- embeddenator-security-mcp
- embeddenator-workspace
- embeddenator-webpuppet
- embeddenator-webpuppet-mcp

**Action:** Use standard library template. MCP servers might need custom steps for TypeScript/Node.

### 3. Core Orchestrator

**Before:**
- embeddenator (main repo)

**Action:** Use "Core Orchestrator (All Features)" template above.

## Maintenance

### Updating Workflows

Repos using `@v1` automatically get updates when this repo releases new `v1.x.x` versions.

### Pinning to Specific Version

If stability is critical:

```yaml
uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@v1.0.0
```

### Testing New Versions

```yaml
uses: tzervas/.github-workflows/.github/workflows/reusable-ci.yml@main
```

## Benefits

✅ **Consistency:** All repos use same CI configuration  
✅ **Maintainability:** Update CI logic in one place  
✅ **Speed:** Optimized caching strategies  
✅ **Features:** FUSE, benchmark isolation built-in  
✅ **Automatic Updates:** `@v1` gets new features automatically

## Next Steps

1. Replace CI workflows in all repos (start with component libraries)
2. Test workflows with a small PR
3. Remove old workflow files
4. Update repo README badges if needed

## Questions?

See main [README.md](README.md) for full API documentation.

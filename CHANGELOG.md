# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-01-04

### Added
- Initial release of shared GitHub workflows
- Reusable CI workflow (`reusable-ci.yml`)
- Composite action: `setup-rust` - Install Rust toolchain
- Composite action: `cache-cargo` - Cache Cargo artifacts
- Composite action: `setup-fuse` - Install FUSE support
- Composite action: `benchmark-isolation` - Configure stable benchmark environment
- Complete documentation with usage examples
- MIT License

### Features
- Configurable Rust versions
- Multi-level cargo caching (registry + build)
- FUSE 2/3 support for filesystem testing
- Benchmark isolation with CPU pinning and frequency locking
- Version pinning at @v1 for automatic updates
